# Homelab notes
## Hardware
- Currently running homelab only as accessible from local network
- Running on Dell 7050 (i5, 1TB NVMe, 16GB RAM)

## Software
- Proxmox with 3 VMs
    - master node
    - 2 worker nodes

### For set up all of the stuff I am using my tutorial
[how_to_install_k8s](https://github.com/krzysztofbrzozowski/k8s_playground/blob/master/custom_cluster_config_0/README.md)

### Installed SW:
<table>
    <tr>
        <td><img width="32" src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/svg/cilium.svg"></td>
        <td><a href="https://cilium.io/">Cilium</a></td>
        <td>CNI of choice, eBPF-based Networking, Observability, Security</td>
    </tr>
</table>
