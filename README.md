# operator

```mermaid
graph TB
  subgraph Cluster
    gw(Gateway)
    operator(Operator)
    k8sApi(Kubernetes API);
    kubevuln(Kubevuln)
    ks(Kubescape)
    urlCm{{ConfigMap<br>URLs}}
    recurringTempCm{{ConfigMap<br>Recur. Scan Template}}
    recurringScanCj{{CronJob<br>Recurring Scan}}
  end;
   masterGw(Master Gateway) .- gw
    gw ---> operator
    recurringScanCj ---> operator
    recurringScanCj --> recurringScanCj
    operator -->|scan cluster| ks
    operator -->|scan images| kubevuln
    operator --> k8sApi
    operator --- urlCm
    operator --- recurringTempCm
  
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,gw,masterGw,kollector,urlCm,recurringScanCj,recurringTempCm,kubevuln,er,dashboard plain
```
#Kubevuln


```mermaid
graph TB

subgraph Cluster
    kubevuln(Kubevuln)  
    k8sApi(Kubernetes API)
    operator(Operator)
    gateway(Gateway)
    urlCm{{ConfigMap<br>URLs}}
    recurringScanCj{{CronJob<br>Recurring Scan}}
    recurringScanCm{{ConfigMap<br>Recurring Scan}}
    recurringTempCm{{ConfigMap<br>Recurring Scan Template}}

end

masterGateway .- gateway
gateway .-|Scan Notification| operator 
operator -->|Collect NS, Images|k8sApi
operator -->|Start Scan| kubevuln
operator --- urlCm
urlCm --- kubevuln 
recurringTempCm --- operator
recurringScanCj -->|Periodic Run| recurringScanCj
recurringScanCj -->|Scan Notification| operator
recurringScanCm --- recurringScanCj

subgraph Backend
    er(CloudEndpoint)
    masterGateway("Master Gateway") 
    kubevuln -->|Scan Results| er
end;

classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000

class k8sApi k8s
class urlCm,recurringScanCm,operator,er,gateway,masterGateway,recurringScanCj,recurringTempCm plain


```
#kubescape
```mermaid
graph TB

subgraph Cluster
    ks(Kubescape)
    k8sApi(Kubernetes API)
    operator(Operator)
    gateway(Gateway)
    ksCm{{ConfigMap<br>Kubescape}}
    recurringScanCj{{CronJob<br>Recurring Scan}}
    recurringScanCm{{ConfigMap<br>Recurring Scan}}
    recurringTempCm{{ConfigMap<br>Recurring Scan Template}}
end

masterGateway .- gateway
gateway .-|Scan Notification| operator 
operator -->|Start Scan| ks
ks-->|Collect Cluster Info|k8sApi
ksCm --- ks 
recurringTempCm --- operator
recurringScanCj -->|Periodic Run| recurringScanCj
recurringScanCj -->|Scan Notification| operator
recurringScanCm --- recurringScanCj
subgraph Backend
    er(CloudEndpoint)
    masterGateway("Master Gateway") 
    ks -->|Scan Results| er
end;

classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000

class k8sApi k8s
class ksCm,recurringScanCm,operator,er,gateway,masterGateway,recurringScanCj,recurringTempCm plain


```

