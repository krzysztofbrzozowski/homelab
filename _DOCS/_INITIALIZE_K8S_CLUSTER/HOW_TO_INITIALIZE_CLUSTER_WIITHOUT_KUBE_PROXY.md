Here is basic example of custom cluster creation based on VMs

## Bare metal intallation (2 PCs with Ubuntu server):
```
- k8s_master        <- 192.168.1.50
- k8s_node_0        <- 192.168.1.51
```

### Install few supporting packages
```
sudo apt-get install apt-transport-https gnupg ca-certificates curl software-properties-common inetutils-traceroute neovim
```

## Dockere related stuff
### Install docker ->  https://docs.docker.com/engine/install/ubuntu/
Clear everything
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Install docker
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Add $USER to docker group
```
sudo usermod -aG docker $USER
```

Test if docker running fine
```
docker run hello-world
```

## Kubernete related stuff
### Disable swap
```
sudo swapoff -a
sudo sed -i '/\/swap.img/s/^/#/' /etc/fstab
```


## Enable IP forward and apply that
```
sudo bash -c 'cat <<EOF > /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward=1
EOF'
sudo sysctl --system
```

## Configure containerd
```
sudo containerd config default | sudo tee /etc/containerd/config.toml > /dev/null 2>&1 
cat /etc/containerd/config.toml
```

Change "SystemdCgroup" to true
...
ShimGroup = ""
SystemdCgroup = true
...

Restart and verify the containerd status
```
sudo systemctl status containerd
sudo systemctl restart containerd
```

### Install k8s kubelet kubeadm kubectl (1.32)
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/k8s.gpg
sudo chmod 644 /etc/apt/keyrings/k8s.gpg # allow unprivileged APT programs to read this keyring
echo 'deb [signed-by=/etc/apt/keyrings/k8s.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/k8s.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### Init the cluster
```
kubeadm init --skip-phases=addon/kube-proxy
```

Output:
```
...
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes running the following command on each as root:

  kubeadm join 192.168.1.50:6443 --token yfmdjc.xyz \
        --discovery-token-ca-cert-hash sha256:xyz \
        --control-plane --certificate-key xyz

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.232.140:6443 --token yfmdjc.xyz \
        --discovery-token-ca-cert-hash sha256:xyz 
```

### Apply kubeconfig
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Verification
### 1st verification
```
user@k8smaster:~$ kubectl get nodes
NAME        STATUS     ROLES           AGE   VERSION
k8smaster   NotReady   control-plane   11m   v1.32.3
```

Join nodes
```
kubeadm join 192.168.232.140:6443 --token yfmdjc.xyz \
        --discovery-token-ca-cert-hash sha256:xyz 
```

### 2nd verification after connecting to control-node -> kubeadm join 192.168.232.140:6443...
```
user@k8smaster:~$ kubectl get nodes
NAME        STATUS     ROLES           AGE   VERSION
k8smaster   NotReady   control-plane   15m   v1.32.3
k8snode0    NotReady   <none>          39s   v1.32.3
```

> [!TIP]
> Probable here you can start running FluxCD

### Install Helm
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

which helm
```

Add cilium repo
```
helm repo add cilium https://helm.cilium.io/
```

Install cilium
```
helm install cilium cilium/cilium --version 1.17.2 \
    --namespace kube-system \
    --set kubeProxyReplacement=true \
    --set k8sServiceHost=192.168.1.50 \
    --set k8sServicePort=6443
```

### Verify if it is ok
```
kubectl -n kube-system exec ds/cilium -- cilium-dbg status | grep KubeProxyReplacement


user@k8smaster:~$ kubectl -n kube-system exec ds/cilium -- cilium-dbg status | grep KubeProxyReplacement
Defaulted container "cilium-agent" out of: cilium-agent, config (init), mount-cgroup (init), apply-sysctl-overwrites (init), mount-bpf-fs (init), clean-cilium-state (init), install-cni-binaries (init)
KubeProxyReplacement:    True   [enp0s31f6       192.168.1.51 2a02:a31d:80ed:450::3cf fd6d:4757:edfd::3cf fd6d:4757:edfd:0:ba85:84ff:feb0:5ab8 2a02:a31d:80ed:450:ba85:84ff:feb0:5ab8 fe80::ba85:84ff:feb0:5ab8 (Direct Routing)]
```

Now go to fluxCD and reinitalize this repo
