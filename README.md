# In-cluster components overview

An overview of each in-cluster component which is part of the Kubescape platform helm chart.
Follow the repository link for in-depth information on a specific component.

## High-level Architecture Diagram


```mermaid
graph TB

  client([client]) .-> dashboard
  masterGw  .- gw

  subgraph Cluster
    gw(Gateway)
    operator(Operator)
    k8sApi(Kubernetes API);
    kubevuln(Kubevuln)
    ks(Kubescape)
    gw --- operator
    operator -->|scan cluster| ks
    operator -->|scan images| kubevuln
    operator --> k8sApi
    ks --> k8sApi
  end;
  
subgraph Backend
    er(CloudEndpoint)
    dashboard(Dashboard) --> masterGw("Master Gateway") 
    ks --> er
    kubevuln --> er
  end;
  
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,operator,gw,masterGw,kollector,kubevuln,er,dashboard plain
```
  

---

## [Operator](https://github.com/kubescape/operator)

* __Resource Kind:__ `Deployment`
* __Communication:__ REST API, Websocket
* __Responsibility:__ The Operator component is at the heart of the solution as it is the triggering engine for the different actions in the cluster; It responds to REST API requests and messages received over websocket connection, and triggers the relevant action in the cluster. Such actions could be triggering a configuration scan, image vulnerability scan, defining a recurring scan (by creating CronJobs), etc.


<details>Component Diagram1

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
    gw --> operator
    recurringScanCj --> operator
    operator -->|scan cluster| ks
    operator -->|scan images| kubevuln
    operator --> k8sApi
    operator --- urlCm
    operator --- recurringTempCm
  
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,gw,masterGw,urlCm,recurringScanCj,recurringTempCm,kubevuln plain
```

</details>

---

## [Gateway](https://github.com/kubescape/gateway)

* __Resource Kind:__ `Deployment`
* __Communication:__ REST API, Websocket
* __Responsibility:__ Broadcasts a message received to its registered clients. When a client registers itself in a Gateway it must provide a set of attributes, which will serve as identification, for message routing purposes.

  In our architecture, the Gateway acts both as a server and a client, depending on its running configuration:
  * Master Gateway: Refers to the instance running in the backend. Broadcasts messages to all of its registered Gateways.
  * In-cluster Gateway: Refers to the instance running in the cluster. Registered to the Master Gateway using a websocket; Broadcasts messages to the different in-cluster components, this enables executing actions in runtime.

  A Master Gateway communicates with multiple in-cluster Gateways, hence it is able to communicate with multiple clusters.

<details>Component Diagram

```mermaid
graph TB
  subgraph Backend
   dashboard(Dashboard)
    masterGw("Gateway (Master)") 
  end
   subgraph Cluster N
    gw3("Gateway (In-cluster)")
    operator3(Operator)
  end;
  subgraph Cluster 2
    gw2("Gateway (In-cluster)")
    operator2(Operator)
  end;
   
subgraph Cluster 1
    gw1("Gateway (In-cluster)")
    operator1(Operator)
  end;
  dashboard --> masterGw
   masterGw .- gw2
   masterGw .- gw3
       gw1 .- operator1
    gw2 .- operator2
    gw3 .- operator3
   masterGw .- gw1

    
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,operator1,dashboard,operator2,operator3 plain
```

</details>

---

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


```mermaid
graph TB
  subgraph Backend
   dashboard(Dashboard)
    masterGw("Gateway (Master)") 
  end
   subgraph Cluster N
    gw3("Gateway (In-cluster)")
    operator3(Operator)
  end;
  subgraph Cluster 2
    gw2("Gateway (In-cluster)")
    operator2(Operator)
  end;
   
subgraph Cluster 1
    gw1("Gateway (In-cluster)")
    operator1(Operator)
  end;
  dashboard --> masterGw
   masterGw .- gw2
   masterGw .- gw3
       gw1 .- operator1
    gw2 .- operator2
    gw3 .- operator3
   masterGw .- gw1

    
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,operator1,dashboard,operator2,operator3 plain
```

---
---
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
    gw --> operator
    recurringScanCj --> operator
    operator -->|scan cluster| ks
    operator -->|scan images| kubevuln
    operator --> k8sApi
    operator --- urlCm
    operator --- recurringTempCm
  
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
  classDef plain fill:#ddd,stroke:#fff,stroke-width:1px,color:#000;
  class k8sApi k8s
  class ks,gw,masterGw,urlCm,recurringScanCj,recurringTempCm,kubevuln plain
```
