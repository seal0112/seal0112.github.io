---
sidebar_position: 2
---

# Core Concept

K8S的目的在於能以容易（Container）的方式託管各種服務，容器化的服務易於部署及管理

## Node
    - Control Node: **負責紀錄Work Node的設定，狀態，規劃container要放到哪一個Node去執行**
    - Worker Node: **用來存放container**

## ETCD
    - Key-Value Database
    - 用來儲存cluster的資訊, 如 Node, PODs, Configs, Secret, Account, ...
    - 對cluster的所有修改（增加Node, 部署POD or Replica Set）, 都會儲存到ETCD, **ETCD完成修改後，對cluster的變更才算完成**

## Kube-API Server
    - 是 Kubernetes（K8S）的核心通信元件，負責處理所有對 Kubernetes 叢集的請求，所有內部與外部的請求（如 kubectl、Kubernetes Dashboard、Controller Manager、Scheduler 等）都必須透過它來存取叢集資源。
    - 主要負責的任務
        - 使用者驗證（Authenticate User）
        - 請求驗證（Validate Request）
        - 檢索數據（Retrieve data）
        - 更新 ETCD（Update ETCD）
        - 調度（Scheduler）
        - 與 Kubelet 溝通

## Kube Controller Manager
    - 管理K8S內的各種controller
        - Controller可以想像成K8S內的辦公室，每個辦公室負責不同工作
        - Controller目的在於持續監測K8S特定服務的狀態
        - 當監控的服務出現錯誤，Controller會在第一時間做錯誤處理

## Kube Scheduler
    - 負責 **決定哪個Pod會在哪個Node上運行**
    - 不會直接啟動 Pod（這是 Kubelet 的工作）
    - 調度（Scheduling）流程
        1. 偵測到新的 Pending Pod: 如果一個 Pod 還沒被分配到 Node，Kube Scheduler 會將它放入候選清單。
        2. 篩選適合的 Node: 是否有足夠的 CPU / 記憶體資源？是否符合 Pod 的 nodeSelector 或 affinity 條件？
        3. 評分並選擇最佳 Node: 為每個符合條件的 Node 計算一個分數
        4. 分配 Pod 到最佳 Node: Scheduler 會選擇得分最高的 Node，並更新 Pod 的 spec.nodeName。

## Kubelet
    - Kubelet 是 Worker Node 上運行的代理程式（Agent），負責與 Master Node（Kubernetes 控制平面）溝通
    - 它確保節點上的 Pod 按預期運行，並回報狀態給 Kube-API Server。


## Kube Proxy
    - Pod之間用來溝通的組件。
    - 運行在每個 Worker Node 上，確保不同的 Pod 和 Service 之間能夠順利互相訪問。
    - Kubernetes 中的 Service 提供了一個 固定的虛擬 IP（ClusterIP），Kube Proxy 會負責將請求從 Service 轉發到 對應的 Pod。
    - 當一個 Service 連接到多個 Pod 時，Kube Proxy 會將流量均勻地分配給這些 Pod，確保請求不會集中到某一個 Pod 上。

## Pod
    - Worker Node不直接管理container，而是管理Pod
    - 能建立在K8S內最小的obejct，Pod裡面包含一個或多個Container
K8S的yaml file格式如下
```
apiVersion: v1 # api版本
kind: Pod # 資源的種類
metadata: # 關於資源的資料, metadata只能指定name和labels
  name:
  labels:
    app: myapp-pod
    app2: app2
spec: # 資源的內容
  containers:
    - name: nginx-container
      image: nginx
```

# K8S Controller
## ReplicaSets
    - 舊版叫做: Replication Controller
    - 高可用性（High Availability）: 如果一個應用程式（application）壞了，Replication Controller可以幫我們重啟一個或多個新的Pod
    - 負載平衡（Load Balance）:
    - 縮放性（Scaling）:
## Deployments
## Services
## Namespace
