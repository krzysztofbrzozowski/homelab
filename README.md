# Homelab notes
## Hardware
- Currently running homelab only as accessible from local network
- Running on 2 x Dell 7050 (i5, 256GB NVMe, 16GB RAM)


### For set up all of the stuff I am using my tutorial
[how_to_initialize_k8s_without_kube_proxy](https://github.com/krzysztofbrzozowski/homelab/blob/master/_DOCS/_INITIALIZE_K8S_CLUSTER/HOW_TO_INITIALIZE_CLUSTER.md)

### IPs
Master
```
192.168.1.50
```

Node 0
```
192.168.1.51
```

Currently only one LoadBalancer is working with address
```
192.168.1.50
```
It is the entry point to Grafana



### Installed SW:
<table>
    <tr>
        <td><img width="32" src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/svg/cilium.svg"></td>
        <td><a href="https://cilium.io/">Cilium</a></td>
        <td>CNI of choice, eBPF-based Networking, Observability, Security</td>
    </tr>
    <tr>
        <td><img width="32" src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/svg/flux-cd.svg"></td>
        <td><a href="https://fluxcd.io/">Flux CD</a></td>
        <td>GitOps solution</td>
    </tr>
    <tr>
        <td><img width="32" src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/svg/grafana.svg"></td>
        <td><a href="https://grafana.com/">Grafana</a></td>
        <td>The open observability platform.</td>
    </tr>
    <tr>
        <td><img width="32" src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/svg/prometheus.svg"></td>
        <td><a href="https://prometheus.io/">Prometheus</a></td>
        <td>An open-source monitoring system</td>
    </tr>
</table>
