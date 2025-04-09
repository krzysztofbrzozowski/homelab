# HOW to CloudNativePG

I will use https://cloudnative-pg.io

## Install CloudNativePG operator
Since I am running FluxCD I deployed CloudNativePG operator, it is installed -> https://github.com/krzysztofbrzozowski/homelab/tree/master/fluxcd/infrastructure/base/cloudnativepg

## Install cnpg plugin for kubectl
```bash
curl -sSfL \
  https://github.com/cloudnative-pg/cloudnative-pg/raw/main/hack/install-cnpg-plugin.sh | \
  sudo sh -s -- -b /usr/local/bin
```

Now on you can use cnpg as 
```
k cnpg ...
```

