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

Added infrastructure folder
One can run test if everything is building before push (now doing it from separate system so no able to do it)
```
kustomize build fluxcd/infrastructure/homelab
```