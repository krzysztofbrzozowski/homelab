```
kubectl get pod -n flux-system

user@k8smaster:~$ kubectl get pod -n flux-system
NAME                                       READY   STATUS    RESTARTS   AGE
helm-controller-b6767d66-6zd5j             1/1     Running   0          30m
kustomize-controller-57c7ff5596-9k6zn      1/1     Running   0          30m
notification-controller-58ffd586f7-4xn5b   1/1     Running   0          30m
source-controller-6ff87cb475-2x4cm         1/1     Running   0          30m
```


helm-controller-b6767d66-6zd5j             1/1     Running   0          30m
is the helm operator which will be used to install helm charts
![flux_operator](https://fluxcd.io/img/helm-controller.png)

Generally the most important thing is
1. Add/subscribe your repository (HelmRepositoryCRD)
2. Download the HelmChart (HelmChartCRD)
3. Install the release with scpecific verison (HelmReleaseCRD)

From now on NOT use helm command except
- helm create mychart
- helm --debug --dryrun install-name ./mychart
Nice explanation about helm templating -> https://helm.sh/docs/chart_template_guide/getting_started/

Basically in the gotk-components.yaml
```yaml
...
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/component: helm-controller
    app.kubernetes.io/instance: flux-system
    app.kubernetes.io/part-of: flux
    app.kubernetes.io/version: v2.5.1
    control-plane: controller
  name: helm-controller
  namespace: flux-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: helm-controller
  template:
...
```

it is the pod we see is running here
```
helm-controller-b6767d66-6zd5j             1/1     Running   0          30m
```

## Subscribing HelmRepository (1)
Added infrastructure folder
One can run test if everything is building before push (now doing it from separate system so no able to do it)
```
kustomize build fluxcd/infrastructure/homelab
```

can download it fast
```
flux reconcile kustomization infrastructure --with-source
```

See helmrepos
```
kubectl get helmrepository -n kube-system

user@k8smaster:~$ kubectl get helmrepository -n kube-system
NAME     URL                       AGE   READY   STATUS
cilium   https://helm.cilium.io/   24m   True    stored artifact: revision 'sha256:b70419cb975ed089b7d3f7647a34034b7f203fe1901b9b47607a0a6af48af7bd'
```

example subscription configuration
```yaml
# Flux Helm repository for installing stuff from Cilium (e.g. Cilium ingress controller)
apiVersion: source.toolkit.fluxcd.io/v1
kind: HelmRepository
metadata:
  name: cilium
  namespace: kube-system
spec:
  interval: 1h
  url: https://helm.cilium.io/
```

> [!TIP]
> If using OCI repository, READY and STATUS will not be visible

## Instruct SourceController to grab HelmChart - Helm chart custom resource (2)
HelmChart example
```yaml
# flux HelmChart to grab the nginx ingress controller chart
#  this example is not really used - we are creating this from the HelmRelease crd
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmChart
metadata:
  name: nginx
  namespace: nginx
spec:
  chart: nginx-ingress
  version: 1.2.x
  interval: 5h
  sourceRef:
    kind: HelmRepository
    name: nginx
```

Below example is actual HelmRelease
In general probably there is no need to separate it per file (HelmRepoCRD and HelmChartCRD), it can be done in one file
```yaml
apiVersion: helm.toolkit.fluxcd.io/v2
kind: HelmRelease
metadata:
  name: cilium
  namespace: kube-system

spec:
  releaseName: cilium
  interval: 15m # Check for drifting
  chart:
    spec:
      chart: cilium # Specify the chart you want to grab
      version: 1.17.x
      interval: 6h # Check every 6h new release within 1.17.x is there
      sourceRef:            
        kind: HelmRepository # Reference the HelmRepository according to HelmRepository CRD you want to get chart from
        name: cilium
        namespace: kube-system

  valuesFrom:
    - kind: ConfigMap
      name: cilium-values
```


See helmcharts
```
kubectl get helmchart -n kube-system

user@k8smaster:~$ kubectl get helmchart -n kube-system
NAME                 CHART    VERSION   SOURCE KIND      SOURCE NAME   AGE   READY   STATUS
kube-system-cilium   cilium   1.17.x    HelmRepository   cilium        37s   True    pulled 'cilium' chart with version '1.17.2'
```

## Helm Release CRD (3)
Probably besides installing the release, applying the values it runs helm tests
(HelmRelease is done in point 2)

Check for helmrelease
```
kubectl get helmrelease -n kube-system
```

Check all services and objects in kube-system
```
kubectl get all -n kube-system
```

Debug the error
```
flux stats

RECONCILERS             RUNNING FAILING SUSPENDED       STORAGE
GitRepository           1       0       0               157.2 KiB
OCIRepository           0       0       0               -
HelmRepository          0       0       0               -
HelmChart               0       0       0               -
Bucket                  0       0       0               -
Kustomization           4       1       0               -
HelmRelease             0       0       0               -
Alert                   0       0       0               -
Provider                0       0       0               -
Receiver                0       0       0               -
ImageUpdateAutomation   0       0       0               -
ImagePolicy             0       0       0               -
ImageRepository         0       0       0               -
```

```
flux events
41s                     Warning BuildFailed                     Kustomization/apps              kustomize build failed: accumulating resources: accumulation err='accumulating resources from '../base': read /tmp/kustomization-25720710/fluxcd/apps/base: is a directory': recursed accumulation of path '/tmp/kustomization-25720710/fluxcd/apps/base': accumulating resources: accumulation err='accumulating resources from 'namespace.yaml': open /tmp/kustomization-25720710/fluxcd/apps/base/namespace.yaml: no such file or directory': must build at directory: not a valid directory: evalsymlink failure on '/tmp/kustomization-25720710/fluxcd/apps/base/namespace.yaml' : lstat /tmp/kustomization-25720710/fluxcd/apps/base/namespace.yaml: no such file or directory
32s (x7 over 6m34s)     Normal  GitOperationSucceeded           GitRepository/flux-system       no changes since last reconcilation: observed revision 'master@sha1:18f3d57a5f9f1bdc07ec9f0c3d79205864840ad5'     
```