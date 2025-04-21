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

Get all clusters
```
k get clusters -A

user@k8smaster:~$ k get clusters -A
NAMESPACE         NAME                 AGE   INSTANCES   READY   STATUS               PRIMARY
network-metrics   network-metrics-db   13m   1                   Setting up primary
```

Get status of cluster
```
k cnpg status network-metrics-db

user@k8smaster:~$ k cnpg status network-metrics-db
Error: while trying to get cluster network-metrics-db in namespace default: clusters.postgresql.cnpg.io "network-metrics-db" not found
```
or info from https://cloudnative-pg.io/documentation/1.25/troubleshooting/
```
kubectl logs -n cnpg-system \
  deployment/cnpg-controller-manager --all-containers=true

user@k8smaster:~$ kubectl logs -n cnpg-system \
  deployment/cnpg-controller-manager --all-containers=true
error: error from server (NotFound): deployments.apps "cnpg-controller-manager" not found in namespace "cnpg-system"
```

Here trying some example
```
k cnpg status cluster-example -n database

user@k8smaster:~$ k cnpg status cluster-example -n database
Cluster Summary
Primary server is initializing
Name               database/cluster-example
PostgreSQL Image:  ghcr.io/cloudnative-pg/postgresql:17.4
Primary instance:   (switching to cluster-example-1)
Status:            Setting up primary Creating primary instance cluster-example-1
Instances:         3
Ready instances:   0
Size:              container not found

Continuous Backup status
Not configured

Physical backups
Primary instance not found

Streaming Replication status
Primary instance not found

Instances status
Name  Current LSN  Replication role  Status  QoS  Manager Version  Node
----  -----------  ----------------  ------  ---  ---------------  ----
```


Trying to get some logs
```
k cnpg logs cluster cluster-example -n database

it empty
```

Current error:
```
Defaulted container "initdb" out of: initdb, bootstrap-controller (init)
```

Error has been fixed and described in:
https://krzysztofbrzozowski.com/post/cluster-stuck-on-init-msg-creating-new-job/