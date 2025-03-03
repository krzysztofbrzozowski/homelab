# Installing grafana
## Installing details
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

[!TIP]
> Looks like sometimes in my master node I am not able to update 
> ```
> ...Unable to get an update from the "prometheus-community" chart repository (https://prometheus-community.github.io/helm-charts):
>        read tcp [2a02:a31d:80ed:450::175]:41766->[2606:50c0:8003::153]:443: read: connection reset by peer
> Update Complete. ⎈Happy Helming!⎈
> ```
> I simply run the command few times

```bash
helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace=prometheus-stack --create-namespace
```

## Verify installation
```bash
kubectl get pods -A
```
```
prometheus-stack   alertmanager-prometheus-stack-kube-prom-alertmanager-0   2/2     Running   0               66s
prometheus-stack   prometheus-prometheus-stack-kube-prom-prometheus-0       2/2     Running   0               66s
prometheus-stack   prometheus-stack-grafana-5d5785df6b-wjlzz                3/3     Running   0               80s
prometheus-stack   prometheus-stack-kube-prom-operator-86c4976cb7-p7vn8     1/1     Running   0               80s
prometheus-stack   prometheus-stack-kube-state-metrics-6dd6f774db-nngqx     1/1     Running   0               80s
prometheus-stack   prometheus-stack-prometheus-node-exporter-6xpk9          1/1     Running   0               80s
prometheus-stack   prometheus-stack-prometheus-node-exporter-8wrp8          1/1     Running   0               80s
prometheus-stack   prometheus-stack-prometheus-node-exporter-ddmk9          1/1     Running   0               80s
```


```bash
kubectl get services -A
```
```
prometheus-stack   alertmanager-operated                                ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP     4m39s
prometheus-stack   prometheus-operated                                  ClusterIP   None             <none>        9090/TCP                       4m39s
prometheus-stack   prometheus-stack-grafana                             ClusterIP   10.102.16.204    <none>        80/TCP                         4m53s
prometheus-stack   prometheus-stack-kube-prom-alertmanager              ClusterIP   10.104.36.79     <none>        9093/TCP,8080/TCP              4m53s
prometheus-stack   prometheus-stack-kube-prom-operator                  ClusterIP   10.103.43.163    <none>        443/TCP                        4m53s
prometheus-stack   prometheus-stack-kube-prom-prometheus                ClusterIP   10.107.87.128    <none>        9090/TCP,8080/TCP              4m53s
prometheus-stack   prometheus-stack-kube-state-metrics                  ClusterIP   10.104.159.52    <none>        8080/TCP                       4m53s
prometheus-stack   prometheus-stack-prometheus-node-exporter            ClusterIP   10.101.199.65    <none>        9100/TCP                       4m53s
```

```bash
kubectl get pods -n prometheus-stack -o wide
```
```
alertmanager-prometheus-stack-kube-prom-alertmanager-0   2/2     Running   0          21m   10.0.2.232      k8snode1    <none>           <none>
prometheus-prometheus-stack-kube-prom-prometheus-0       2/2     Running   0          21m   10.0.2.73       k8snode1    <none>           <none>
prometheus-stack-grafana-5d5785df6b-wjlzz                3/3     Running   0          21m   10.0.0.198      k8snode0    <none>           <none>
prometheus-stack-kube-prom-operator-86c4976cb7-p7vn8     1/1     Running   0          21m   10.0.2.211      k8snode1    <none>           <none>
prometheus-stack-kube-state-metrics-6dd6f774db-nngqx     1/1     Running   0          21m   10.0.2.212      k8snode1    <none>           <none>
prometheus-stack-prometheus-node-exporter-6xpk9          1/1     Running   0          21m   192.168.1.249   k8smaster   <none>           <none>
prometheus-stack-prometheus-node-exporter-8wrp8          1/1     Running   0          21m   192.168.1.202   k8snode0    <none>           <none>
prometheus-stack-prometheus-node-exporter-ddmk9          1/1     Running   0          21m   192.168.1.203   k8snode1    <none>           <none>
```


## Do a port forwarding to the service prometheus-stack-grafana port 80 from port 3000
```bash
kubectl port-forward -n prometheus-stack svc/prometheus-stack-grafana 3000:80
```

[!error]
> I am not able to get grafana from localnetwork
> ```curl: (7) Failed to connect to 192.168.1.201 port 3000 after 4 ms: Couldn't connect to server

[!TIP]
> Maybe add Ingress service?
