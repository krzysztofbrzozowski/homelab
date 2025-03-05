## How to ingress -> looks like nginx is not working, at least have a lot of issues,
Now will try to go with Cillium

Tried to add ingress-nginx.yaml but have some issues
```
Name:             ingress-service
Labels:           <none>
Namespace:        default
Address:
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /?(.*)   prometheus-stack-grafana:3000 (<error: services "prometheus-stack-grafana" not found>)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /$1
              nginx.ingress.kubernetes.io/use-regex: true
Events:       <none>
```

Today run kubectl describe ingress ingress-service-grafana 
```
Name:             ingress-service-grafana
Labels:           <none>
Namespace:        prometheus-stack
Address:
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /?(.*)   prometheus-stack-grafana:80 (10.0.0.93:3000)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /$1
              nginx.ingress.kubernetes.io/use-regex: true
Events:       <none>
```
- [x] Why ingress is not catching that service?

Before shall intall ingress-nginx itselg but looks like have some issues with run
Installation
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/baremetal/deploy.yaml
```

Looks it has some issues
```
user@k8smaster:~$ kubectl get pods --namespace=ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-p2bhf        0/1     Completed   0          4m24s
ingress-nginx-admission-patch-rk2rd         0/1     Completed   0          4m24s
ingress-nginx-controller-6d59d95796-nwjc9   1/1     Running     0          4m24s
```
error:
```
user@k8smaster:~$ k logs ingress-nginx-admission-patch-rk2rd -n ingress-nginx
W0305 21:01:38.463488       1 client_config.go:667] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
{"level":"info","msg":"patching webhook configurations 'ingress-nginx-admission' mutating=false, validating=true, failurePolicy=Fail","source":"k8s/k8s.go:118","time":"2025-03-05T21:01:38Z"}
{"level":"info","msg":"Patched hook(s)","source":"k8s/k8s.go:138","time":"2025-03-05T21:01:38Z"}
```
error:
```
user@k8smaster:~$ k logs ingress-nginx-admission-create-p2bhf -n ingress-nginx
W0305 21:01:36.861008       1 client_config.go:667] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
{"err":"secrets \"ingress-nginx-admission\" not found","level":"info","msg":"no secret found","source":"k8s/k8s.go:229","time":"2025-03-05T21:01:36Z"}
{"level":"info","msg":"creating new secret","source":"cmd/create.go:28","time":"2025-03-05T21:01:36Z"}
```

Tried to install with helm
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

Looks it is succesfull however have some doubts
```
Release "ingress-nginx" does not exist. Installing it now.
NAME: ingress-nginx
LAST DEPLOYED: Wed Mar  5 21:37:31 2025
NAMESPACE: ingress-nginx
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the load balancer IP to be available.
You can watch the status by running 'kubectl get service --namespace ingress-nginx ingress-nginx-controller --output wide --watch'

An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
```

because see only
```
ingress-nginx-controller-cd9d6bbd7-d2n4k   1/1     Running   0          6m21s
```

some admission
```
user@k8smaster:~$ kubectl get validatingwebhookconfigurations
NAME                                   WEBHOOKS   AGE
ingress-nginx-admission                1          11m
prometheus-stack-kube-prom-admission   1          2d5h
```


trying to apply ingress-service.yaml is failing
```bash
user@k8smaster:~$ k apply -f ingress-service.yaml
Error from server (InternalError): error when creating "ingress-service.yaml": Internal error occurred: failed calling webhook "validate.nginx.ingress.kubernetes.io": failed to call webhook: Post "https://ingress-nginx-controller-admission.ingress-nginx.svc:443/networking/v1/ingresses?timeout=10s": context deadline exceeded
```

Some related issue?
https://github.com/kubernetes/ingress-nginx/issues/9833


```
user@k8smaster:~$ k describe service ingress-nginx-controller-admission -n  ingress-nginx
Name:                     ingress-nginx-controller-admission
Namespace:                ingress-nginx
Labels:                   app.kubernetes.io/component=controller
                          app.kubernetes.io/instance=ingress-nginx
                          app.kubernetes.io/managed-by=Helm
                          app.kubernetes.io/name=ingress-nginx
                          app.kubernetes.io/part-of=ingress-nginx
                          app.kubernetes.io/version=1.12.0
                          helm.sh/chart=ingress-nginx-4.12.0
Annotations:              meta.helm.sh/release-name: ingress-nginx
                          meta.helm.sh/release-namespace: ingress-nginx
Selector:                 app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.101.210.116
IPs:                      10.101.210.116
Port:                     https-webhook  443/TCP
TargetPort:               webhook/TCP
Endpoints:                10.0.0.153:8443
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>
```


Removed all stuff 
```bash
helm delete ingress-nginx -n ingress-nginx
```

-> probably will go with Cillium
https://mkz.me/weblog/posts/cilium-enable-ingress-controller/
https://docs.cilium.io/en/stable/network/servicemesh/ingress/

TODO:
    - [] Reabout Clilium + LB, there was something mentioned in nginx  
