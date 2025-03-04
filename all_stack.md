# Currently all of the stuff running on clouster looks like
```
kubectl get all -o wide -A | tee all-stack.txt
user@k8smaster:~$ cat all-stack.txt
NAMESPACE          NAME                                                         READY   STATUS    RESTARTS        AGE   IP              NODE        NOMINATED NODE   READINESS GATES
kube-system        pod/cilium-envoy-22fqt                                       1/1     Running   3 (5m36s ago)   33h   192.168.1.249   k8smaster   <none>           <none>
kube-system        pod/cilium-envoy-cm9qr                                       1/1     Running   1 (5m23s ago)   33h   192.168.1.233   k8snode1    <none>           <none>
kube-system        pod/cilium-envoy-klkps                                       1/1     Running   1 (5m29s ago)   33h   192.168.1.233   k8snode0    <none>           <none>
kube-system        pod/cilium-operator-f99b55df8-69lwj                          1/1     Running   4 (5m23s ago)   33h   192.168.1.233   k8snode1    <none>           <none>
kube-system        pod/cilium-operator-f99b55df8-d9zrw                          1/1     Running   3 (5m29s ago)   33h   192.168.1.233   k8snode0    <none>           <none>
kube-system        pod/cilium-qnmzj                                             1/1     Running   1 (5m29s ago)   33h   192.168.1.202   k8snode0    <none>           <none>
kube-system        pod/cilium-vfx9t                                             1/1     Running   3 (5m36s ago)   33h   192.168.1.201   k8smaster   <none>           <none>
kube-system        pod/cilium-wkvjb                                             1/1     Running   1 (5m23s ago)   33h   192.168.1.233   k8snode1    <none>           <none>
kube-system        pod/coredns-668d6bf9bc-7xw6g                                 1/1     Running   1 (5m29s ago)   33h   10.0.0.100      k8snode0    <none>           <none>
kube-system        pod/coredns-668d6bf9bc-ghm4m                                 1/1     Running   1 (5m29s ago)   33h   10.0.0.34       k8snode0    <none>           <none>
kube-system        pod/etcd-k8smaster                                           1/1     Running   3 (5m36s ago)   33h   192.168.1.201   k8smaster   <none>           <none>
kube-system        pod/kube-apiserver-k8smaster                                 1/1     Running   3 (5m36s ago)   33h   192.168.1.201   k8smaster   <none>           <none>
kube-system        pod/kube-controller-manager-k8smaster                        1/1     Running   3 (5m36s ago)   33h   192.168.1.201   k8smaster   <none>           <none>
kube-system        pod/kube-proxy-5xrhb                                         1/1     Running   3 (5m36s ago)   33h   192.168.1.249   k8smaster   <none>           <none>
kube-system        pod/kube-proxy-hbhgp                                         1/1     Running   1 (5m23s ago)   33h   192.168.1.233   k8snode1    <none>           <none>
kube-system        pod/kube-proxy-lmfpb                                         1/1     Running   1 (5m29s ago)   33h   192.168.1.233   k8snode0    <none>           <none>
kube-system        pod/kube-scheduler-k8smaster                                 1/1     Running   3 (5m36s ago)   33h   192.168.1.201   k8smaster   <none>           <none>
prometheus-stack   pod/alertmanager-prometheus-stack-kube-prom-alertmanager-0   2/2     Running   2 (5m23s ago)   29h   10.0.2.212      k8snode1    <none>           <none>
prometheus-stack   pod/prometheus-prometheus-stack-kube-prom-prometheus-0       2/2     Running   2 (5m23s ago)   29h   10.0.2.207      k8snode1    <none>           <none>
prometheus-stack   pod/prometheus-stack-grafana-5d5785df6b-wjlzz                3/3     Running   3 (5m29s ago)   29h   10.0.0.172      k8snode0    <none>           <none>
prometheus-stack   pod/prometheus-stack-kube-prom-operator-86c4976cb7-p7vn8     1/1     Running   1 (5m23s ago)   29h   10.0.2.128      k8snode1    <none>           <none>
prometheus-stack   pod/prometheus-stack-kube-state-metrics-6dd6f774db-nngqx     1/1     Running   1 (5m23s ago)   29h   10.0.2.156      k8snode1    <none>           <none>
prometheus-stack   pod/prometheus-stack-prometheus-node-exporter-6xpk9          1/1     Running   2 (5m2s ago)    29h   192.168.1.201   k8smaster   <none>           <none>
prometheus-stack   pod/prometheus-stack-prometheus-node-exporter-8wrp8          1/1     Running   1 (5m29s ago)   29h   192.168.1.233   k8snode0    <none>           <none>
prometheus-stack   pod/prometheus-stack-prometheus-node-exporter-ddmk9          1/1     Running   1 (5m23s ago)   29h   192.168.1.233   k8snode1    <none>           <none>

NAMESPACE          NAME                                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                        AGE   SELECTOR
default            service/kubernetes                                           ClusterIP   10.96.0.1        <none>        443/TCP                        33h   <none>
kube-system        service/cilium-envoy                                         ClusterIP   None             <none>        9964/TCP                       33h   k8s-app=cilium-envoy
kube-system        service/hubble-peer                                          ClusterIP   10.103.147.170   <none>        443/TCP                        33h   k8s-app=cilium
kube-system        service/kube-dns                                             ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP         33h   k8s-app=kube-dns
kube-system        service/prometheus-stack-kube-prom-coredns                   ClusterIP   None             <none>        9153/TCP                       29h   k8s-app=kube-dns
kube-system        service/prometheus-stack-kube-prom-kube-controller-manager   ClusterIP   None             <none>        10257/TCP                      29h   component=kube-controller-manager
kube-system        service/prometheus-stack-kube-prom-kube-etcd                 ClusterIP   None             <none>        2381/TCP                       29h   component=etcd
kube-system        service/prometheus-stack-kube-prom-kube-proxy                ClusterIP   None             <none>        10249/TCP                      29h   k8s-app=kube-proxy
kube-system        service/prometheus-stack-kube-prom-kube-scheduler            ClusterIP   None             <none>        10259/TCP                      29h   component=kube-scheduler
kube-system        service/prometheus-stack-kube-prom-kubelet                   ClusterIP   None             <none>        10250/TCP,10255/TCP,4194/TCP   29h   <none>
prometheus-stack   service/alertmanager-operated                                ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP     29h   app.kubernetes.io/name=alertmanager
prometheus-stack   service/prometheus-operated                                  ClusterIP   None             <none>        9090/TCP                       29h   app.kubernetes.io/name=prometheus
prometheus-stack   service/prometheus-stack-grafana                             ClusterIP   10.102.16.204    <none>        80/TCP                         29h   app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=grafana
prometheus-stack   service/prometheus-stack-kube-prom-alertmanager              ClusterIP   10.104.36.79     <none>        9093/TCP,8080/TCP              29h   alertmanager=prometheus-stack-kube-prom-alertmanager,app.kubernetes.io/name=alertmanager
prometheus-stack   service/prometheus-stack-kube-prom-operator                  ClusterIP   10.103.43.163    <none>        443/TCP                        29h   app=kube-prometheus-stack-operator,release=prometheus-stack
prometheus-stack   service/prometheus-stack-kube-prom-prometheus                ClusterIP   10.107.87.128    <none>        9090/TCP,8080/TCP              29h   app.kubernetes.io/name=prometheus,operator.prometheus.io/name=prometheus-stack-kube-prom-prometheus
prometheus-stack   service/prometheus-stack-kube-state-metrics                  ClusterIP   10.104.159.52    <none>        8080/TCP                       29h   app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=kube-state-metrics
prometheus-stack   service/prometheus-stack-prometheus-node-exporter            ClusterIP   10.101.199.65    <none>        9100/TCP                       29h   app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=prometheus-node-exporter

NAMESPACE          NAME                                                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE   CONTAINERS      IMAGES                                                                                                                                                            SELECTOR
kube-system        daemonset.apps/cilium                                      3         3         3       3            3           kubernetes.io/os=linux   33h   cilium-agent    quay.io/cilium/cilium:v1.16.6@sha256:1e0896b1c4c188b4812c7e0bed7ec3f5631388ca88325c1391a0ef9172c448da                                                             k8s-app=cilium
kube-system        daemonset.apps/cilium-envoy                                3         3         3       3            3           kubernetes.io/os=linux   33h   cilium-envoy    quay.io/cilium/cilium-envoy:v1.30.9-1737073743-40a016d11c0d863b772961ed0168eea6fe6b10a5@sha256:a69dfe0e54b24b0ff747385c8feeae0612cfbcae97bfcc8ee42a773bb3f69c88   k8s-app=cilium-envoy
kube-system        daemonset.apps/kube-proxy                                  3         3         3       3            3           kubernetes.io/os=linux   33h   kube-proxy      registry.k8s.io/kube-proxy:v1.32.2                                                                                                                                k8s-app=kube-proxy
prometheus-stack   daemonset.apps/prometheus-stack-prometheus-node-exporter   3         3         3       3            3           kubernetes.io/os=linux   29h   node-exporter   quay.io/prometheus/node-exporter:v1.9.0                                                                                                                           app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=prometheus-node-exporter

NAMESPACE          NAME                                                  READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS                                            IMAGES                                                                                                            SELECTOR
kube-system        deployment.apps/cilium-operator                       2/2     2            2           33h   cilium-operator                                       quay.io/cilium/operator-generic:v1.16.6@sha256:13d32071d5a52c069fb7c35959a56009c6914439adc73e99e098917646d154fc   io.cilium/app=operator,name=cilium-operator
kube-system        deployment.apps/coredns                               2/2     2            2           33h   coredns                                               registry.k8s.io/coredns/coredns:v1.11.3                                                                           k8s-app=kube-dns
prometheus-stack   deployment.apps/prometheus-stack-grafana              1/1     1            1           29h   grafana-sc-dashboard,grafana-sc-datasources,grafana   quay.io/kiwigrid/k8s-sidecar:1.30.0,quay.io/kiwigrid/k8s-sidecar:1.30.0,docker.io/grafana/grafana:11.5.2          app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=grafana
prometheus-stack   deployment.apps/prometheus-stack-kube-prom-operator   1/1     1            1           29h   kube-prometheus-stack                                 quay.io/prometheus-operator/prometheus-operator:v0.80.1                                                           app=kube-prometheus-stack-operator,release=prometheus-stack
prometheus-stack   deployment.apps/prometheus-stack-kube-state-metrics   1/1     1            1           29h   kube-state-metrics                                    registry.k8s.io/kube-state-metrics/kube-state-metrics:v2.15.0                                                     app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=kube-state-metrics

NAMESPACE          NAME                                                             DESIRED   CURRENT   READY   AGE   CONTAINERS                                            IMAGES                                                                                                            SELECTOR
kube-system        replicaset.apps/cilium-operator-f99b55df8                        2         2         2       33h   cilium-operator                                       quay.io/cilium/operator-generic:v1.16.6@sha256:13d32071d5a52c069fb7c35959a56009c6914439adc73e99e098917646d154fc   io.cilium/app=operator,name=cilium-operator,pod-template-hash=f99b55df8
kube-system        replicaset.apps/coredns-668d6bf9bc                               2         2         2       33h   coredns                                               registry.k8s.io/coredns/coredns:v1.11.3                                                                           k8s-app=kube-dns,pod-template-hash=668d6bf9bc
prometheus-stack   replicaset.apps/prometheus-stack-grafana-5d5785df6b              1         1         1       29h   grafana-sc-dashboard,grafana-sc-datasources,grafana   quay.io/kiwigrid/k8s-sidecar:1.30.0,quay.io/kiwigrid/k8s-sidecar:1.30.0,docker.io/grafana/grafana:11.5.2          app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=grafana,pod-template-hash=5d5785df6b
prometheus-stack   replicaset.apps/prometheus-stack-kube-prom-operator-86c4976cb7   1         1         1       29h   kube-prometheus-stack                                 quay.io/prometheus-operator/prometheus-operator:v0.80.1                                                           app=kube-prometheus-stack-operator,pod-template-hash=86c4976cb7,release=prometheus-stack
prometheus-stack   replicaset.apps/prometheus-stack-kube-state-metrics-6dd6f774db   1         1         1       29h   kube-state-metrics                                    registry.k8s.io/kube-state-metrics/kube-state-metrics:v2.15.0                                                     app.kubernetes.io/instance=prometheus-stack,app.kubernetes.io/name=kube-state-metrics,pod-template-hash=6dd6f774db

NAMESPACE          NAME                                                                    READY   AGE   CONTAINERS                     IMAGES
prometheus-stack   statefulset.apps/alertmanager-prometheus-stack-kube-prom-alertmanager   1/1     29h   alertmanager,config-reloader   quay.io/prometheus/alertmanager:v0.28.0,quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
prometheus-stack   statefulset.apps/prometheus-prometheus-stack-kube-prom-prometheus       1/1     29h   prometheus,config-reloader     quay.io/prometheus/prometheus:v3.2.1,quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
```


### config maps
```
user@k8smaster:~$ kubectl get configmaps -A
NAMESPACE          NAME                                                           DATA   AGE
default            kube-root-ca.crt                                               1      33h
kube-node-lease    kube-root-ca.crt                                               1      33h
kube-public        cluster-info                                                   1      33h
kube-public        kube-root-ca.crt                                               1      33h
kube-system        cilium-config                                                  130    33h
kube-system        cilium-envoy-config                                            1      33h
kube-system        coredns                                                        1      33h
kube-system        extension-apiserver-authentication                             6      33h
kube-system        kube-apiserver-legacy-service-account-token-tracking           1      33h
kube-system        kube-proxy                                                     2      33h
kube-system        kube-root-ca.crt                                               1      33h
kube-system        kubeadm-config                                                 1      33h
kube-system        kubelet-config                                                 1      33h
prometheus-stack   kube-root-ca.crt                                               1      29h
prometheus-stack   prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0   35     29h
prometheus-stack   prometheus-stack-grafana                                       1      29h
prometheus-stack   prometheus-stack-grafana-config-dashboards                     1      29h
prometheus-stack   prometheus-stack-kube-prom-alertmanager-overview               1      29h
prometheus-stack   prometheus-stack-kube-prom-apiserver                           1      29h
prometheus-stack   prometheus-stack-kube-prom-cluster-total                       1      29h
prometheus-stack   prometheus-stack-kube-prom-controller-manager                  1      29h
prometheus-stack   prometheus-stack-kube-prom-etcd                                1      29h
prometheus-stack   prometheus-stack-kube-prom-grafana-datasource                  1      29h
prometheus-stack   prometheus-stack-kube-prom-grafana-overview                    1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-coredns                         1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-cluster               1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-multicluster          1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-namespace             1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-node                  1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-pod                   1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-workload              1      29h
prometheus-stack   prometheus-stack-kube-prom-k8s-resources-workloads-namespace   1      29h
prometheus-stack   prometheus-stack-kube-prom-kubelet                             1      29h
prometheus-stack   prometheus-stack-kube-prom-namespace-by-pod                    1      29h
prometheus-stack   prometheus-stack-kube-prom-namespace-by-workload               1      29h
prometheus-stack   prometheus-stack-kube-prom-node-cluster-rsrc-use               1      29h
prometheus-stack   prometheus-stack-kube-prom-node-rsrc-use                       1      29h
prometheus-stack   prometheus-stack-kube-prom-nodes                               1      29h
prometheus-stack   prometheus-stack-kube-prom-nodes-aix                           1      29h
prometheus-stack   prometheus-stack-kube-prom-nodes-darwin                        1      29h
prometheus-stack   prometheus-stack-kube-prom-persistentvolumesusage              1      29h
prometheus-stack   prometheus-stack-kube-prom-pod-total                           1      29h
prometheus-stack   prometheus-stack-kube-prom-prometheus                          1      29h
prometheus-stack   prometheus-stack-kube-prom-proxy                               1      29h
prometheus-stack   prometheus-stack-kube-prom-scheduler                           1      29h
prometheus-stack   prometheus-stack-kube-prom-workload-total                      1      29h
```

### statefulset
```
user@k8smaster:~$ kubectl get statefulset -A
NAMESPACE          NAME                                                   READY   AGE
prometheus-stack   alertmanager-prometheus-stack-kube-prom-alertmanager   1/1     29h
prometheus-stack   prometheus-prometheus-stack-kube-prom-prometheus       1/1     29h
```
