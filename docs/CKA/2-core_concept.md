---
sidebar_position: 2
---

# Core Concept

K8S的目的在於能以容易(Container)的方式託管各種服務，容器化的服務 亦於部署及管理

- Node
    - Control Node: **負責紀錄Work Node的設定，狀態，規劃container要放到哪一個Node去執行**
    - Worker Node: **用來存放container**
- ETCD
    - Key-Value Database
    - 用來儲存cluster的資訊, 如 Node, PODs, Configs, Secret, Account, ...
    - 對cluster的所有修改(增加Node, 部署POD or Replica Set), 都會儲存到ETCD, ETCD完成修改會，對cluster的變更才算完成
- Kube-API Server
- Kube Controller Manager
- Kube Scheduler
- Kubelet
    - Worker Node與來跟Master Node溝通的工具
- Kube Proxy
    - Pod之間用來溝通

- Pod
    - Worker Node不直接管理container，而是管理Pod
    - 能建立在K8S內最小的obejct，Pod裡面包含一個或多個Container
```
apiVersion:
kind:
metadata:
spec:
```

- ReplicaSets
    - 舊版叫做: Replication Controller
    - 高可用性(High Availability): 如果一個應用程式(application)壞了，Replication Controller可以幫我們重啟一個或多個新的Pod
    - 負載平衡(Load Balance):
    - 縮放性(Scaling):
- Deployments
- Services
- Namespace
