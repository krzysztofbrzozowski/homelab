## How to databases

If you don't have pgadmin running in Cluster you can use 'clasic' method of checking stuff in DBs
1. Enter the Pod of interesting cluster of DBs
```
exec into it using kubectl exec -i -t pod_name -n your_namespace -- /bin/bash
```
e.g.
```
k exec -i -t network-metrics-database-1 -n databases -- /bin/bash
```

run pgsql and do your stuff e.g.
show databases
```
\l
```