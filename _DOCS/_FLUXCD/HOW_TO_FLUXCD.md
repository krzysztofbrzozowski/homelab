## Installation
Install CLI
```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

before bootstrap do a check
```
flux check --pre
```

genarate PAT on GH for flux -> https://fluxcd.io/flux/installation/bootstrap/github/
If ued fine grained access token select -> Repository permissions -> contets -> R/W

```bash
export GITHUB_TOKEN=<some_token>
```

next
```bash
flux bootstrap github \
  --token-auth \
  --owner=krzysztofbrzozowski \
  --repository=homelab \
  --branch=master \
  --path=clusters/homelab \
  --personal
```

[!error]
> Looks it is hanging in some step
> ```
> user@k8smaster:~$ flux bootstrap github   --token-auth   --owner=krzysztofbrzozowski   --repository=homelab_gitops   --branch=master   --path=homelab_gitops   --personal   --private=false
> ► connecting to github.com
> ► cloning branch "master" from Git repository "https://github.com/krzysztofbrzozowski/homelab_gitops.git"
> ✔ cloned repository
> ► generating component manifests
> ✔ generated component manifests
> ✔ component manifests are up to date
> ► installing components in "flux-system" namespace
> ✔ installed components
> ✔ reconciled components
> ► determining if source secret "flux-system/flux-system" exists
> ► generating source secret
> ► applying source secret "flux-system/flux-system"
> ✔ reconciled source secret
> ► generating sync manifests
> ✔ generated sync manifests
> ✔ sync manifests are up to date
> ► applying sync manifests
> ✔ reconciled sync configuration
> ◎ waiting for GitRepository "flux-system/flux-system" to be reconciled
> ✗ gitrepository 'flux-system/flux-system' not ready: 'building artifact'
> ◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
> ✗ client rate limiter Wait returned an error: context deadline exceeded
> ► confirming components are healthy
> ✔ helm-controller: deployment ready
> ✔ kustomize-controller: deployment ready
> ✔ notification-controller: deployment ready
> ✔ source-controller: deployment ready
> ✔ all components are healthy
> ✗ bootstrap failed with 2 health check failure(s): [error while waiting for GitRepository to be ready: 'gitrepository 'flux-system/flux-system' not ready: 'building artifact'', error while waiting for Kustomization to be ready: 'client rate limiter Wait returned an error: context deadline exceeded']
>```

Try:
```
user@k8smaster:~$ flux check
► checking prerequisites
✔ Kubernetes 1.32.2 >=1.30.0-0
► checking version in cluster
✔ distribution: flux-v2.5.1
✔ bootstrapped: false
► checking controllers
✔ helm-controller: deployment ready
► ghcr.io/fluxcd/helm-controller:v1.2.0
✔ kustomize-controller: deployment ready
► ghcr.io/fluxcd/kustomize-controller:v1.5.1
✔ notification-controller: deployment ready
► ghcr.io/fluxcd/notification-controller:v1.5.0
✔ source-controller: deployment ready
► ghcr.io/fluxcd/source-controller:v1.5.0
► checking crds
✔ alerts.notification.toolkit.fluxcd.io/v1beta3
✔ buckets.source.toolkit.fluxcd.io/v1
✔ gitrepositories.source.toolkit.fluxcd.io/v1
✔ helmcharts.source.toolkit.fluxcd.io/v1
✔ helmreleases.helm.toolkit.fluxcd.io/v2
✔ helmrepositories.source.toolkit.fluxcd.io/v1
✔ kustomizations.kustomize.toolkit.fluxcd.io/v1
✔ ocirepositories.source.toolkit.fluxcd.io/v1beta2
✔ providers.notification.toolkit.fluxcd.io/v1beta3
✔ receivers.notification.toolkit.fluxcd.io/v1
✔ all checks passed
```

```
user@k8smaster:~$ flux get sources all
NAME                            REVISION        SUSPENDED       READY   MESSAGE
gitrepository/flux-system                       False           Unknown building artifact
```

```
user@k8smaster:~$ flux get kustomizations
NAME            REVISION        SUSPENDED       READY   MESSAGE
flux-system                     False           False   Source artifact not found, retrying in 30s
```

```
user@k8smaster:~$ flux get sources all --all-namespaces
NAMESPACE       NAME                            REVISION        SUSPENDED       READY   MESSAGE                                                                                            
flux-system     gitrepository/flux-system                       False           False   failed to checkout and determine revision: unable to clone 'https://github.com/krzysztofbrzozowski/homelab_gitops.git': Get "https://github.com/krzysztofbrzozowski/homelab_gitops.git/info/refs?service=git-upload-pack": dial tcp: lookup github.com: i/o timeout
```

```
user@k8smaster:~$ flux logs --level=error
failed getting logs: the server could not find the requested resource ( pods/log helm-controller-b6767d66-cmcdt)
failed getting logs: the server could not find the requested resource ( pods/log kustomize-controller-57c7ff5596-d5jr9)
failed getting logs: the server could not find the requested resource ( pods/log source-controller-6ff87cb475-7xq67)
✗ failed to collect logs from all Flux pods
```

Looks like DNS resolution not working in the cluster, removed the DNS - it was not the best idea since I am not able to apply it again
Reinitialized k8s cluster

After fresh cluster intialization looks like GitRepository is reconciled, now have problem with Kustomization
```
user@k8smaster:~$ flux bootstrap github \
  --token-auth \
  --owner=krzysztofbrzozowski \
  --repository=homelab_gitops \
  --branch=master \
  --path=clusters/homelab_gitops \
  --personal
► connecting to github.com
► cloning branch "master" from Git repository "https://github.com/krzysztofbrzozowski/homelab_gitops.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed component manifests to "master" ("923e21a2d3e459842c052ba07ebc17ba33dd5c85")
► pushing component manifests to "https://github.com/krzysztofbrzozowski/homelab_gitops.git"
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
► generating source secret
► applying source secret "flux-system/flux-system"
✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ committed sync manifests to "master" ("affeffe2ce1e72dadb9ce2d355e13fab930eb260")
► pushing sync manifests to "https://github.com/krzysztofbrzozowski/homelab_gitops.git"
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for GitRepository "flux-system/flux-system" to be reconciled
✔ GitRepository reconciled successfully
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled

✗ kustomization 'flux-system/flux-system' not ready: 'Reconciliation in progress'
► confirming components are healthy
✔ helm-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ notification-controller: deployment ready
✔ source-controller: deployment ready
✔ all components are healthy
✗ bootstrap failed with 1 health check failure(s): error while waiting for Kustomization to be ready: 'kustomization 'flux-system/flux-system' not ready: 'Reconciliation in progress''
```

```
user@k8smaster:~$ flux get sources all
NAME                            REVISION                SUSPENDED       READY   MESSAGE
gitrepository/flux-system       master@sha1:affeffe2    False           True    stored artifact for revision 'master@sha1:affeffe2'
```
Error here
```
user@k8smaster:~$ flux get kustomizations
NAME            REVISION        SUSPENDED       READY   MESSAGE
flux-system                     False           Unknown Reconciliation in progress
```

```
NAMESPACE       NAME                            REVISION                SUSPENDED       READY   MESSAGE
flux-system     gitrepository/flux-system       master@sha1:affeffe2    False           True    stored artifact for revision 'master@sha1:affeffe2'
```

Looks like with one node (one master + one node) in Proxmox boostsrap for FluCD is workin
```
user@k8smaster:~$ flux bootstrap github \
  --token-auth \
  --owner=krzysztofbrzozowski \
  --repository=homelab_gitops \
  --branch=master \
  --path=clusters/homelab_gitops \
  --personal
► connecting to github.com
► cloning branch "master" from Git repository "https://github.com/krzysztofbrzozowski/homelab_gitops.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ component manifests are up to date
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
► generating source secret
► applying source secret "flux-system/flux-system"
✔ reconciled source secret
► generating sync manifests
✔ generated sync manifests
✔ sync manifests are up to date
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for GitRepository "flux-system/flux-system" to be reconciled
✔ GitRepository reconciled successfully
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
✔ Kustomization reconciled successfully
► confirming components are healthy
✔ helm-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ notification-controller: deployment ready
✔ source-controller: deployment ready
✔ all components are healthy
```
[!IMPORTANT]
> Looks like all of the issues which had with reconciliation happend
> because was running k8s master and node as VMs in Proxmox
> During runing it as baremetal no issues at all...

Reconcile fast everything
```
flux reconcile kustomization flux-system --with-source
```

## Uninstall
```bash
flux uninstall --namespace=flux-system
```
