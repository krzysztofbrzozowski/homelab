Looks like after first setup and ciium 1.16 there was some issue

```
user@k8smaster:~$ cilium status
    /¯¯\
 /¯¯\__/¯¯\    Cilium:             2 errors
 \__/¯¯\__/    Operator:           OK
 /¯¯\__/¯¯\    Envoy DaemonSet:    OK
 \__/¯¯\__/    Hubble Relay:       disabled
    \__/       ClusterMesh:        disabled

DaemonSet              cilium                   Desired: 3, Ready: 3/3, Available: 3/3
DaemonSet              cilium-envoy             Desired: 3, Ready: 3/3, Available: 3/3
Deployment             cilium-operator          Desired: 2, Ready: 2/2, Available: 2/2
Containers:            cilium                   Running: 3
                       cilium-envoy             Running: 3
                       cilium-operator          Running: 2
                       clustermesh-apiserver
                       hubble-relay
Cluster Pods:          6/6 managed by Cilium
Helm chart version:    1.17.2
Image versions         cilium             quay.io/cilium/cilium:v1.17.2@sha256:3c4c9932b5d8368619cb922a497ff2ebc8def5f41c18e410bcc84025fcd385b1: 3
                       cilium-envoy       quay.io/cilium/cilium-envoy:v1.31.5-1741765102-efed3defcc70ab5b263a0fc44c93d316b846a211@sha256:377c78c13d2731f3720f931721ee309159e782d882251709cb0fac3b42c03f4b: 3
                       cilium-operator    quay.io/cilium/operator-generic:v1.17.2@sha256:81f2d7198366e8dec2903a3a8361e4c68d47d19c68a0d42f0b7b6e3f0523f249: 2
Errors:                cilium             cilium-srkgr    unable to retrieve cilium status: command failed (pod=kube-system/cilium-srkgr, container=cilium-agent): Internal error occurred: unable to upgrade connection: pod does not exist
                       cilium             cilium-srkgr    unable to retrieve cilium endpoint information: command failed (pod=kube-system/cilium-srkgr, container=cilium-agent): Internal error occurred: unable to upgrade connection: pod does not exist
```

Upgrated to 1.17.x based on https://docs.cilium.io/en/stable/operations/upgrade/
```
helm install cilium-preflight cilium/cilium --version 1.17.2 \
  --namespace=kube-system \
  --set preflight.enabled=true \
  --set agent=false \
  --set operator.enabled=false
```

```
helm delete cilium-preflight --namespace=kube-system
```

option upgradeCompatibility has been removed because have some errors...
```
helm upgrade cilium cilium/cilium --version 1.17.2 \
  --namespace=kube-system
#  --set upgradeCompatibility=1.X
```

Now cilium status looks fine
```
user@k8smaster:~$ cilium status
    /¯¯\
 /¯¯\__/¯¯\    Cilium:             OK
 \__/¯¯\__/    Operator:           OK
 /¯¯\__/¯¯\    Envoy DaemonSet:    OK
 \__/¯¯\__/    Hubble Relay:       disabled
    \__/       ClusterMesh:        disabled

DaemonSet              cilium                   Desired: 3, Ready: 3/3, Available: 3/3
DaemonSet              cilium-envoy             Desired: 3, Ready: 3/3, Available: 3/3
Deployment             cilium-operator          Desired: 2, Ready: 2/2, Available: 2/2
Containers:            cilium                   Running: 3
                       cilium-envoy             Running: 3
                       cilium-operator          Running: 2
                       clustermesh-apiserver
                       hubble-relay
Cluster Pods:          4/6 managed by Cilium
Helm chart version:    1.17.2
Image versions         cilium             quay.io/cilium/cilium:v1.17.2@sha256:3c4c9932b5d8368619cb922a497ff2ebc8def5f41c18e410bcc84025fcd385b1: 3
                       cilium-envoy       quay.io/cilium/cilium-envoy:v1.31.5-1741765102-efed3defcc70ab5b263a0fc44c93d316b846a211@sha256:377c78c13d2731f3720f931721ee309159e782d882251709cb0fac3b42c03f4b: 3
                       cilium-operator    quay.io/cilium/operator-generic:v1.17.2@sha256:81f2d7198366e8dec2903a3a8361e4c68d47d19c68a0d42f0b7b6e3f0523f249: 2
```
Issue is occuring when worker node does not wake in expected amount of time,
TODO
- [] Install all of the cillium stuff on master node!


Doing some tests
```
cilium connectivity test
```

[!error]
```
user@k8smaster:~$ cilium connectivity test
ℹ️  Monitor aggregation detected, will skip some flow validation steps
✨ [kubernetes] Creating namespace cilium-test-1 for connectivity check...
✨ [kubernetes] Deploying echo-same-node service...
✨ [kubernetes] Deploying DNS test server configmap...
✨ [kubernetes] Deploying same-node deployment...
✨ [kubernetes] Deploying client deployment...
✨ [kubernetes] Deploying client2 deployment...
✨ [kubernetes] Deploying client3 deployment...
✨ [kubernetes] Deploying echo-other-node service...
✨ [kubernetes] Deploying other-node deployment...
✨ [host-netns] Deploying kubernetes daemonset...
✨ [host-netns-non-cilium] Deploying kubernetes daemonset...
ℹ️  Skipping tests that require a node Without Cilium
⌛ [kubernetes] Waiting for deployment cilium-test-1/client to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test-1/client2 to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test-1/echo-same-node to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test-1/client3 to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test-1/echo-other-node to become ready...
⌛ [kubernetes] Waiting for pod cilium-test-1/client-645b68dcf7-cdhw4 to reach DNS server on cilium-test-1/echo-same-node-6c98489c8d-xvwx7 pod...
⌛ [kubernetes] Waiting for pod cilium-test-1/client2-66475877c6-m2mqj to reach DNS server on cilium-test-1/echo-same-node-6c98489c8d-xvwx7 pod...
⌛ [kubernetes] Waiting for pod cilium-test-1/client3-795488bf5-fmwdb to reach DNS server on cilium-test-1/echo-same-node-6c98489c8d-xvwx7 pod...
E0316 15:15:44.683214    3413 v2.go:167] "Unhandled Error" err="next reader: read tcp 192.168.1.210:57302->192.168.1.210:6443: use of closed network connection" logger="UnhandledError"
E0316 15:15:44.683274    3413 v2.go:150] "Unhandled Error" err="next reader: read tcp 192.168.1.210:57302->192.168.1.210:6443: use of closed network connection" logger="UnhandledError"
timeout reached waiting for lookup for localhost from pod cilium-test-1/client3-795488bf5-fmwdb to server on pod cilium-test-1/echo-same-node-6c98489c8d-xvwx7 to succeed (last error: command failed (pod=cilium-test-1/client3-795488bf5-fmwdb, container=): context deadline exceeded)
```

Looks like when initialized one node at leas tests have started working however sometimes it looks like it is showing error as above, sometimes tests are starting -> see tests.log

Anyhow looks like system is not stable in current config, need to find out why it is happening, maybe because missing config in proxmox?
