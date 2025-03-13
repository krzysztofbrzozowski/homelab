## Installation
Install CLI
```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

genarate PAT on GH for flux -> https://fluxcd.io/flux/installation/bootstrap/github/

export GITHUB_TOKEN=<some_token>

next
```
flux bootstrap github \
  --token-auth \
  --owner=krzysztofbrzozowski \
  --repository=homelab_gitops \
  --branch=master \
  --path=homelab_gitops \
  --personal \
  --private=false
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

## Uninstall
```bash
flux uninstall --namespace=flux-system
```
