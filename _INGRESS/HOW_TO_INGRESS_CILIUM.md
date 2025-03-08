# How to Ingress -> Cilium

- If cilium is installed upgrade
```bash
cilium upgrade --version 1.17.1 --set kubeProxyReplacement=true --set ingressController.enabled=true --set ingressController.loadbalancerMode=dedicated
```

Expected result:
```
ℹ️  Using Cilium version 1.17.1
🔮 Auto-detected cluster name: kubernetes
🔮 Auto-detected kube-proxy has been installed
```

```bash
k get svc -A
```

```
kube-system        cilium-envoy                                         ClusterIP      None             <none>        9964/TCP                       5d7h
kube-system        cilium-ingress                                       LoadBalancer   10.109.172.180   <pending>     80:31216/TCP,443:30700/TCP     2m19s
kube-system        hubble-peer                                          ClusterIP      10.103.147.170   <none>        443/TCP                        5d7h
```


Restart the Cilium DaemonSet and Cilium operator
```
k -n kube-system rollout restart deployment/cilium-operator
k -n kube-system rollout restart ds/cilium
```

- Create ingress for some basic test app
```
wget https://raw.githubusercontent.com/istio/istio/release-1.11/samples/bookinfo/platform/kube/bookinfo.yaml
k apply -f bookinfo.yaml
```

- Create CiliumLoadBalancer IPPool
```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumLoadBalancerIPPool
metadata:
  name: "basic-pool"
spec:
  spec:
  blocks:
    - start: "192.168.1.100"
      stop: "192.168.1.200"
```

```bash
k get svc
NAME                           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                      AGE
cilium-ingress-basic-ingress   LoadBalancer   10.107.8.87      192.168.1.101   80:31821/TCP,443:32519/TCP   59m
```

```bash
k apply -f basic-pool.yaml
kubectl get ippools
NAME         DISABLED   CONFLICTING   IPS AVAILABLE   AGE
basic-pool   false      False         99              32m
```

- Enable propagating to LAN IP of LoadBalancer
```
cilium upgrade --reuse-values --version 1.17.1 --set l2announcements.enabled=true --set externalIPs.enabled=true --set devices=ens18
```
Restart the Cilium DaemonSet and Cilium operator
```
k -n kube-system rollout restart deployment/cilium-operator
k -n kube-system rollout restart ds/cilium
```



- Apply policy
```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumL2AnnouncementPolicy
metadata:
  name: basic-policy
spec:
  interfaces:
  - ens18
  externalIPs: true
  loadBalancerIPs: true
```

```bash
kubectl apply -f basic-policy.yaml
```

- Verify connections status
```
curl http://192.168.1.101/
```
[!error]
> ```
> Access denied
> ```

- [] need to fix that error

