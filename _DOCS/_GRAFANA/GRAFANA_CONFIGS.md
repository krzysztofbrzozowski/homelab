### describeprometheus
```bash
kubectl describe statefulset -n prometheus-stack prometheus-prometheus-stack-kube-prom-prometheus > prometheus.yaml
```
```yaml
Name:               prometheus-prometheus-stack-kube-prom-prometheus
Namespace:          prometheus-stack
CreationTimestamp:  Mon, 03 Mar 2025 16:42:54 +0000
Selector:           app.kubernetes.io/instance=prometheus-stack-kube-prom-prometheus,app.kubernetes.io/managed-by=prometheus-operator,app.kubernetes.io/name=prometheus,operator.prometheus.io/name=prometheus-stack-kube-prom-prometheus,operator.prometheus.io/shard=0,prometheus=prometheus-stack-kube-prom-prometheus
Labels:             app=kube-prometheus-stack-prometheus
                    app.kubernetes.io/instance=prometheus-stack
                    app.kubernetes.io/managed-by=Helm
                    app.kubernetes.io/part-of=kube-prometheus-stack
                    app.kubernetes.io/version=69.7.2
                    chart=kube-prometheus-stack-69.7.2
                    heritage=Helm
                    managed-by=prometheus-operator
                    operator.prometheus.io/mode=server
                    operator.prometheus.io/name=prometheus-stack-kube-prom-prometheus
                    operator.prometheus.io/shard=0
                    release=prometheus-stack
Annotations:        meta.helm.sh/release-name: prometheus-stack
                    meta.helm.sh/release-namespace: prometheus-stack
                    prometheus-operator-input-hash: 4435443019941645196
Replicas:           1 desired | 1 total
Update Strategy:    RollingUpdate
Pods Status:        1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           app.kubernetes.io/instance=prometheus-stack-kube-prom-prometheus
                    app.kubernetes.io/managed-by=prometheus-operator
                    app.kubernetes.io/name=prometheus
                    app.kubernetes.io/version=3.2.1
                    operator.prometheus.io/name=prometheus-stack-kube-prom-prometheus
                    operator.prometheus.io/shard=0
                    prometheus=prometheus-stack-kube-prom-prometheus
  Annotations:      kubectl.kubernetes.io/default-container: prometheus
  Service Account:  prometheus-stack-kube-prom-prometheus
  Init Containers:
   init-config-reloader:
    Image:      quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
    Port:       8081/TCP
    Host Port:  0/TCP
    Command:
      /bin/prometheus-config-reloader
    Args:
      --watch-interval=0
      --listen-address=:8081
      --config-file=/etc/prometheus/config/prometheus.yaml.gz
      --config-envsubst-file=/etc/prometheus/config_out/prometheus.env.yaml
      --watched-dir=/etc/prometheus/rules/prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0
    Environment:
      POD_NAME:   (v1:metadata.name)
      SHARD:     0
    Mounts:
      /etc/prometheus/config from config (rw)
      /etc/prometheus/config_out from config-out (rw)
      /etc/prometheus/rules/prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 from prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 (rw)
  Containers:
   prometheus:
    Image:      quay.io/prometheus/prometheus:v3.2.1
    Port:       9090/TCP
    Host Port:  0/TCP
    Args:
      --web.console.templates=/etc/prometheus/consoles
      --web.console.libraries=/etc/prometheus/console_libraries
      --config.file=/etc/prometheus/config_out/prometheus.env.yaml
      --web.enable-lifecycle
      --web.external-url=http://prometheus-stack-kube-prom-prometheus.prometheus-stack:9090
      --web.route-prefix=/
      --storage.tsdb.retention.time=10d
      --storage.tsdb.path=/prometheus
      --storage.tsdb.wal-compression
      --web.config.file=/etc/prometheus/web_config/web-config.yaml
    Liveness:     http-get http://:http-web/-/healthy delay=0s timeout=3s period=5s #success=1 #failure=6
    Readiness:    http-get http://:http-web/-/ready delay=0s timeout=3s period=5s #success=1 #failure=3
    Startup:      http-get http://:http-web/-/ready delay=0s timeout=3s period=15s #success=1 #failure=60
    Environment:  <none>
    Mounts:
      /etc/prometheus/certs from tls-assets (ro)
      /etc/prometheus/config_out from config-out (ro)
      /etc/prometheus/rules/prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 from prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 (rw)
      /etc/prometheus/web_config/web-config.yaml from web-config (ro,path="web-config.yaml")
      /prometheus from prometheus-prometheus-stack-kube-prom-prometheus-db (rw)
   config-reloader:
    Image:      quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
    Port:       8080/TCP
    Host Port:  0/TCP
    Command:
      /bin/prometheus-config-reloader
    Args:
      --listen-address=:8080
      --reload-url=http://127.0.0.1:9090/-/reload
      --config-file=/etc/prometheus/config/prometheus.yaml.gz
      --config-envsubst-file=/etc/prometheus/config_out/prometheus.env.yaml
      --watched-dir=/etc/prometheus/rules/prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0
    Environment:
      POD_NAME:   (v1:metadata.name)
      SHARD:     0
    Mounts:
      /etc/prometheus/config from config (rw)
      /etc/prometheus/config_out from config-out (rw)
      /etc/prometheus/rules/prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 from prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0 (rw)
  Volumes:
   config:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  prometheus-prometheus-stack-kube-prom-prometheus
    Optional:    false
   tls-assets:
    Type:                Projected (a volume that contains injected data from multiple sources)
    SecretName:          prometheus-prometheus-stack-kube-prom-prometheus-tls-assets-0
    SecretOptionalName:  <nil>
   config-out:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  <unset>
   prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      prometheus-prometheus-stack-kube-prom-prometheus-rulefiles-0
    Optional:  false
   web-config:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  prometheus-prometheus-stack-kube-prom-prometheus-web-config
    Optional:    false
   prometheus-prometheus-stack-kube-prom-prometheus-db:
    Type:          EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:     <unset>
  Node-Selectors:  <none>
  Tolerations:     <none>
Volume Claims:     <none>
Events:            <none>
```

# describe alertmanager
```bash
kubectl describe statefulset -n prometheus-stack alertmanager-prometheus-stack-kube-prom-alertmanager > alert-manager.yaml
```

```yaml
Name:               alertmanager-prometheus-stack-kube-prom-alertmanager
Namespace:          prometheus-stack
CreationTimestamp:  Mon, 03 Mar 2025 16:42:54 +0000
Selector:           alertmanager=prometheus-stack-kube-prom-alertmanager,app.kubernetes.io/instance=prometheus-stack-kube-prom-alertmanager,app.kubernetes.io/managed-by=prometheus-operator,app.kubernetes.io/name=alertmanager
Labels:             app=kube-prometheus-stack-alertmanager
                    app.kubernetes.io/instance=prometheus-stack
                    app.kubernetes.io/managed-by=Helm
                    app.kubernetes.io/part-of=kube-prometheus-stack
                    app.kubernetes.io/version=69.7.2
                    chart=kube-prometheus-stack-69.7.2
                    heritage=Helm
                    managed-by=prometheus-operator
                    release=prometheus-stack
Annotations:        meta.helm.sh/release-name: prometheus-stack
                    meta.helm.sh/release-namespace: prometheus-stack
                    prometheus-operator-input-hash: 14597330258016913412
Replicas:           1 desired | 1 total
Update Strategy:    RollingUpdate
Pods Status:        1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           alertmanager=prometheus-stack-kube-prom-alertmanager
                    app.kubernetes.io/instance=prometheus-stack-kube-prom-alertmanager
                    app.kubernetes.io/managed-by=prometheus-operator
                    app.kubernetes.io/name=alertmanager
                    app.kubernetes.io/version=0.28.0
  Annotations:      kubectl.kubernetes.io/default-container: alertmanager
  Service Account:  prometheus-stack-kube-prom-alertmanager
  Init Containers:
   init-config-reloader:
    Image:      quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
    Port:       8081/TCP
    Host Port:  0/TCP
    Command:
      /bin/prometheus-config-reloader
    Args:
      --watch-interval=0
      --listen-address=:8081
      --config-file=/etc/alertmanager/config/alertmanager.yaml.gz
      --config-envsubst-file=/etc/alertmanager/config_out/alertmanager.env.yaml
      --watched-dir=/etc/alertmanager/config
    Environment:
      POD_NAME:   (v1:metadata.name)
      SHARD:     -1
    Mounts:
      /etc/alertmanager/config from config-volume (ro)
      /etc/alertmanager/config_out from config-out (rw)
      /etc/alertmanager/web_config/web-config.yaml from web-config (ro,path="web-config.yaml")
  Containers:
   alertmanager:
    Image:       quay.io/prometheus/alertmanager:v0.28.0
    Ports:       9093/TCP, 9094/TCP, 9094/UDP
    Host Ports:  0/TCP, 0/TCP, 0/UDP
    Args:
      --config.file=/etc/alertmanager/config_out/alertmanager.env.yaml
      --storage.path=/alertmanager
      --data.retention=120h
      --cluster.listen-address=
      --web.listen-address=:9093
      --web.external-url=http://prometheus-stack-kube-prom-alertmanager.prometheus-stack:9093
      --web.route-prefix=/
      --cluster.label=prometheus-stack/prometheus-stack-kube-prom-alertmanager
      --cluster.peer=alertmanager-prometheus-stack-kube-prom-alertmanager-0.alertmanager-operated:9094
      --cluster.reconnect-timeout=5m
      --web.config.file=/etc/alertmanager/web_config/web-config.yaml
    Requests:
      memory:   200Mi
    Liveness:   http-get http://:http-web/-/healthy delay=0s timeout=3s period=10s #success=1 #failure=10
    Readiness:  http-get http://:http-web/-/ready delay=3s timeout=3s period=5s #success=1 #failure=10
    Environment:
      POD_IP:   (v1:status.podIP)
    Mounts:
      /alertmanager from alertmanager-prometheus-stack-kube-prom-alertmanager-db (rw)
      /etc/alertmanager/certs from tls-assets (ro)
      /etc/alertmanager/config from config-volume (rw)
      /etc/alertmanager/config_out from config-out (ro)
      /etc/alertmanager/web_config/web-config.yaml from web-config (ro,path="web-config.yaml")
   config-reloader:
    Image:      quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
    Port:       8080/TCP
    Host Port:  0/TCP
    Command:
      /bin/prometheus-config-reloader
    Args:
      --listen-address=:8080
      --web-config-file=/etc/alertmanager/web_config/web-config.yaml
      --reload-url=http://127.0.0.1:9093/-/reload
      --config-file=/etc/alertmanager/config/alertmanager.yaml.gz
      --config-envsubst-file=/etc/alertmanager/config_out/alertmanager.env.yaml
      --watched-dir=/etc/alertmanager/config
    Environment:
      POD_NAME:   (v1:metadata.name)
      SHARD:     -1
    Mounts:
      /etc/alertmanager/config from config-volume (ro)
      /etc/alertmanager/config_out from config-out (rw)
      /etc/alertmanager/web_config/web-config.yaml from web-config (ro,path="web-config.yaml")
  Volumes:
   config-volume:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  alertmanager-prometheus-stack-kube-prom-alertmanager-generated
    Optional:    false
   tls-assets:
    Type:                Projected (a volume that contains injected data from multiple sources)
    SecretName:          alertmanager-prometheus-stack-kube-prom-alertmanager-tls-assets-0
    SecretOptionalName:  <nil>
   config-out:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  <unset>
   web-config:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  alertmanager-prometheus-stack-kube-prom-alertmanager-web-config
    Optional:    false
   alertmanager-prometheus-stack-kube-prom-alertmanager-db:
    Type:          EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:     <unset>
  Node-Selectors:  <none>
  Tolerations:     <none>
Volume Claims:     <none>
Events:            <none>
```

# describe deplyment
```bash
kubectl describe deployment -n prometheus-stack prometheus-stack-kube-prom-operator > deployment.yaml
```
```yaml
Name:                   prometheus-stack-kube-prom-operator
Namespace:              prometheus-stack
CreationTimestamp:      Mon, 03 Mar 2025 16:42:40 +0000
Labels:                 app=kube-prometheus-stack-operator
                        app.kubernetes.io/component=prometheus-operator
                        app.kubernetes.io/instance=prometheus-stack
                        app.kubernetes.io/managed-by=Helm
                        app.kubernetes.io/name=kube-prometheus-stack-prometheus-operator
                        app.kubernetes.io/part-of=kube-prometheus-stack
                        app.kubernetes.io/version=69.7.2
                        chart=kube-prometheus-stack-69.7.2
                        heritage=Helm
                        release=prometheus-stack
Annotations:            deployment.kubernetes.io/revision: 1
                        meta.helm.sh/release-name: prometheus-stack
                        meta.helm.sh/release-namespace: prometheus-stack
Selector:               app=kube-prometheus-stack-operator,release=prometheus-stack
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:           app=kube-prometheus-stack-operator
                    app.kubernetes.io/component=prometheus-operator
                    app.kubernetes.io/instance=prometheus-stack
                    app.kubernetes.io/managed-by=Helm
                    app.kubernetes.io/name=kube-prometheus-stack-prometheus-operator
                    app.kubernetes.io/part-of=kube-prometheus-stack
                    app.kubernetes.io/version=69.7.2
                    chart=kube-prometheus-stack-69.7.2
                    heritage=Helm
                    release=prometheus-stack
  Service Account:  prometheus-stack-kube-prom-operator
  Containers:
   kube-prometheus-stack:
    Image:      quay.io/prometheus-operator/prometheus-operator:v0.80.1
    Port:       10250/TCP
    Host Port:  0/TCP
    Args:
      --kubelet-service=kube-system/prometheus-stack-kube-prom-kubelet
      --kubelet-endpoints=true
      --kubelet-endpointslice=false
      --localhost=127.0.0.1
      --prometheus-config-reloader=quay.io/prometheus-operator/prometheus-config-reloader:v0.80.1
      --config-reloader-cpu-request=0
      --config-reloader-cpu-limit=0
      --config-reloader-memory-request=0
      --config-reloader-memory-limit=0
      --thanos-default-base-image=quay.io/thanos/thanos:v0.37.2
      --secret-field-selector=type!=kubernetes.io/dockercfg,type!=kubernetes.io/service-account-token,type!=helm.sh/release.v1
      --web.enable-tls=true
      --web.cert-file=/cert/cert
      --web.key-file=/cert/key
      --web.listen-address=:10250
      --web.tls-min-version=VersionTLS13
    Liveness:   http-get https://:https/healthz delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:  http-get https://:https/healthz delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:
      GOGC:  30
    Mounts:
      /cert from tls-secret (ro)
  Volumes:
   tls-secret:
    Type:          Secret (a volume populated by a Secret)
    SecretName:    prometheus-stack-kube-prom-admission
    Optional:      false
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   prometheus-stack-kube-prom-operator-86c4976cb7 (1/1 replicas created)
Events:          <none>
```



