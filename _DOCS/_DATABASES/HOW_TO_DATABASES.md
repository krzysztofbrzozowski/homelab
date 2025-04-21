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

Tesing
kubectl run psql-client --rm -it --image=postgres --env="PGPASSWORD=admin" -- bash
psql -h cluster-example-rw.databases.svc.cluster.local -U admin1 -d app2
psql -h test-pg-rw.default.svc.cluster.local -U testdb -d testdb


kubectl run psql-client --rm -it --image=postgres --env="PGPASSWORD=testdb" -- bash
+
psql -h test-pg-rw.default.svc.cluster.local -U testdb -d testdb
working with
```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/basic-auth
data:
  username: dGVzdGRi
  password: dGVzdGRi
metadata:
  name: app-secret
---
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: test-pg
spec:
  instances: 1
  bootstrap:
    initdb:
      database: testdb
      owner: testdb
      secret:
        name: app-secret
  storage:
    size: 1Gi
```


kubectl -n default create secret generic ss \
--from-literal=username=admin \
--from-literal=password=admin \
--dry-run=client \
-o yaml > ss.yaml

kubeseal --format=yaml --cert=$HOME/sealed_secrets_public_key.pem \
< ss.yaml > ss-sealed.yaml

kubeseal --format=yaml  --controller-namespace=sealed-secrets-system < ss.yaml > ss-sealed2.yaml
kubeseal --scope cluster-wide --format=yaml  --controller-namespace=sealed-secrets-system < abc-secret.yaml > abc-secret-sealed.yaml

kubectl run psql-client --rm -it --image=postgres --env="PGPASSWORD=elo0" -- bash
psql -h pg-elo2-rw.default.svc.cluster.local -U elo2 -d elo2

Above is working with 
```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: pg-elo1
  namespace: default
spec:
  instances: 1
  bootstrap:
    initdb:
      database: elo0
      owner: elo0
      secret:
        name: ss3
  storage:
    size: 1Gi
```
```yaml
apiVersion: v1
data:
  password: ZWxvMA== 
  username: ZWxvMA==
kind: Secret
type: kubernetes.io/basic-auth
metadata:
  creationTimestamp: null
  name: ss3
  namespace: default
```


Working example with sealed secrets

kubectl run psql-client --rm -it --image=postgres --env="PGPASSWORD=abcabcabc" -- bash
psql -h abcabcabc-pg-test-rw.default.svc.cluster.local -U abcabcabc -d abcabcabc

user@k8smaster:~/testing_dbs$ k apply -f abc-secret-sealed.yaml
sealedsecret.bitnami.com/abc-secret created
user@k8smaster:~/testing_dbs$ k apply -f abc-pod.yaml
cluster.postgresql.cnpg.io/abc-pg-test created
user@k8smaster:~/testing_dbs$ k delete -f abc-pod.yaml
cluster.postgresql.cnpg.io "abc-pg-test" deleted
user@k8smaster:~/testing_dbs$ k apply -f abc-pod.yaml
cluster.postgresql.cnpg.io/abcabcabc-pg-test created
```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: abcabcabc-pg-test
  namespace: default
spec:
  instances: 1
  bootstrap:
    initdb:
      database: abcabcabc
      owner: abcabcabc
      secret:
        name: abc-secret
  storage:
    size: 1Gi 
```

```yaml
---
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  annotations:
    sealedsecrets.bitnami.com/cluster-wide: "true"
  creationTimestamp: null
  name: abc-secret
  namespace: default
spec:
  encryptedData:
    password: AgDTg3zb3pvunZ0lqppm3xbM8Vdd5mSBgNajXJr2wfbSv6Gi9VZl0VyRrdxoPFgeOwCNeOAoW5Ti5EX+0z05WMVpeYfPSn0HcLxe09wr3vn7HHnTPPV2Gng7T5pLzL8AAsHu2wtkafs3EQBu90VUHHHZVYTJyyez13KF6m4V/WcjIpRRyBdYIcf+SwhOWezDeImrzIp9XLsn8DfOfVIji2xofyYkJSZr93DNMfwjOC4noAcMpLq50BR1GQlcbWydG+VDNKY0nsCn3q9+ZCNFN+dDMccvE6Y2uC69TABwZpNWEV83k6NqDIKnsqv5L3NQGgbhRG6SGjn6UfdP6c42vymCR9i/jOi2t5UfEpZTQIANnuPY5fjTic9r7q0OOC+kKpsX185OWkkMIC9qycWaeJ9YhLiX1pphmG9F4ZaH8+fyIY07wJFBgG+N4mIPargKxOTzW2OsSSTsWK+9P1nYtBI173+dDLC4wHjKwD0KMYs0x2C3OevaxRJMNAR4G/rl3mG0c8LMI7PnWIBuTnlFAEhJkxbN/1THjlDKeATEfZWZQnpziy2KjH0MMiGmYNY2aE+RpdZXIh9gS6M2rLw+SYdNf/MJyGAZz1vUKmisEE7In6AOwWjlb0hqRVt4mvRjUUHoNL3QuyJokcmVrBGk6Qsj7eIYZl9s2cTAA7tecF9LJonzQ52bYTy+vWyXBh93F5O2fEhAbuVH5BI=
    username: AgB5Gn7lQEizepvv8QoyR6pRxUpgsjqW/KaXXq9c3Wcuk1sS4YZIRQJLiM9KbLdCSLabpQDyxFcSa4YmISs4v3gYr2e++dp2gF+0uCMvLWUeStrVAMtNKg1BUjoNGgalHHIY6ptO5v1hMg6oBbzeYsabXANg9IHlUaTLZMqoUbsopTyMtoQLo/2Nc4xr6rRkA+8z3EZqzKSC00KRAlPgcPgROVsfIGVcN6nNgcHSpSxdciuOXBXAbiTD7aE8R4Wvm6rx+pcveHgSKeu5PNqGnK9bWlnitSVnvHpLUbwqfUGXfoUzr9ElVayE74bSqoJqchJXgL/J1beytCq5lm4rSABt7lB9S6g1It7ic8hM6HkKozZfPaxc73s2DA9JkrfgI/3nurYyqDp/+ZyMou3thLBLadiVY4gXi68kBu9f+wjYbkO5b3Y6BzP4LEVlolnGj7obQKSayR7OzRjy0/4Mu+jMHCpxUf3H+ycRXJBAg0Uyl01FM36fyriuW5vMW2r5sfO7t1PbVW2hOvPNvNWGKRDcI8JYVp0jGyAx4U1oPtx9qqqCFZzZXdsGtCnpsMwAUOw8OPmpLg7gil0Wgb2ETlyaIkwcjMD0lZ0zLpLM42Bo6LEpa7m/sIAc8EtiyRMHy4YUHvR9uMdDvyvvDO9cVlut0DGnqBGSdB8PV/NvQynbEkkPgwbOtB2LRaN+FAmaxNyyMo9ds+goSh8=
  template:
    metadata:
      annotations:
        sealedsecrets.bitnami.com/cluster-wide: "true"
      creationTimestamp: null
      name: abc-secret
      namespace: default
```