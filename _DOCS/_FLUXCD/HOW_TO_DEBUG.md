# Flux get helm satus, releaseses etc.
```
flux get helmreleases

NAMESPACE       NAME                    REVISION        SUSPENDED       READY   MESSAGE                                                                                                    
cnpg-system     cnpg                                    False           False   HelmChart 'cnpg-system/cnpg-system-cnpg' is not ready: latest generation of object has not been reconciled 
kube-system     cilium                  1.17.2          False           False   Helm upgrade failed for release kube-system/cilium with chart cilium@1.17.2: context deadline exceeded     
monitoring      kube-prometheus-stack   70.4.2          False           True    Helm upgrade succeeded for release monitoring/kube-prometheus-stack.v4 with chart kube-prometheus-stack@70.4.2
```

```
flux get sources helm --all-namespaces

NAMESPACE       NAME                    REVISION        SUSPENDED       READY   MESSAGE
cnpg-system     cnpg                    sha256:851b3165 False           True    stored artifact: revision 'sha256:851b3165'
kube-system     cilium                  sha256:b70419cb False           True    stored artifact: revision 'sha256:b70419cb'
monitoring      kube-prometheus-stack   sha256:a1b6987b False           True    stored artifact: revision 'sha256:a1b6987b'
```

```
flux get sources chart --all-namespaces


NAMESPACE       NAME                                    REVISION        SUSPENDED       READY   MESSAGE                                                                                    
cnpg-system     cnpg-system-cnpg                                        False           False   invalid chart reference: failed to get chart version for remote reference: no 'cloudnative-pg' chart with version matching '1.25.x' found
kube-system     kube-system-cilium                      1.17.2          False           True    pulled 'cilium' chart with version '1.17.2'                                                
monitoring      monitoring-kube-prometheus-stack        70.4.2          False           True    pulled 'kube-prometheus-stack' chart with version '70.4.2'  
```