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
    - 高可用性（High Availability）: 確保指定數量的 Pod 始終運行，如果有 Pod 當機或被刪除，ReplicaSet 會自動補上新的 Pod。
    - 負載平衡（Load Balance）: ReplicaSet 本身不提供負載平衡，但通常與 Service（如 ClusterIP, NodePort, LoadBalancer）搭配，透過 kube-proxy 在所有 Pod 之間分發流量。
    - 縮放性（Scaling）: 可以透過 kubectl scale 或 Horizontal Pod Autoscaler (HPA) 來動態調整 Pod 數量。
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-rs
  labels:
    app: myapp
    type: front-end
spec:
  template:
    matadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  repilcas: 3
  selector:
    matchLabels:
      type: front-end
```
## Deployments
  - **Deployment 管理 ReplicaSet**，可進行 版本更新（Rolling Update）、回滾（Rollback） 等操作。
  - 在 K8s 大多數情況下，應該用 Deployment 而不是直接使用 ReplicaSet！
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  template:
    matadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  repilcas: 3
  selector:
    matchLabels:
      type: front-end
```
## Services
Kubernetes 提供 Service 來讓 Pod 之間或外部用戶端可以存取應用程式。主要有 ClusterIP、NodePort、LoadBalancer 三種類型。
### NodePort
- 讓外部可以透過 Node IP + Port 存取服務
- NodePort 會在每個 Node 上開一個高於 30000 的 Port（30000-32767），讓外部用戶可以透過 NodeIP:NodePort 存取服務。
- 適合測試環境，但不建議用於正式環境，因為 缺乏負載均衡(Load Balancer)。
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end
```
### Cluster IP
- 只允許集群內部存取（內部服務）
- ClusterIP 是 K8s 預設的 Service 類型，它會分配一個 內部 IP，只能在 Kubernetes 內部使用，外部無法直接訪問。
- 這種方式通常用於 內部微服務間的通訊。
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    type: front-end
```
內部存取方式
```
curl http://myapp-service:80
```
### Load Balancer
- 雲端環境（AWS, GCP, Azure）才支援，提供自動負載均衡
- 適用於雲端環境，當 Service 設為 LoadBalancer 時，Kubernetes 會請 雲端供應商（AWS ELB, GCP Load Balancer, Azure LB） 自動建立一個外部負載均衡器，並將流量導入對應的 Service。
- 適合正式環境的對外服務。
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    type: front-end
```
### Service 類型比較
| 類型          | 存取方式                          | 適用場景                     |
|---------------|-----------------------------------|------------------------------|
| ClusterIP     | 內部存取（只能 Kubernetes 內部）  | 內部微服務通訊               |
| NodePort      | 透過 NodeIP:NodePort 存取         | 測試環境、臨時存取           |
| LoadBalancer  | 透過雲端負載均衡器提供外部存取    | 正式環境的外部流量           |

## Namespace
- 用來在同一個 Kubernetes 叢集中分隔不同的資源，類似於「虛擬集群」的概念。
- 資源隔離：不同環境（開發/測試/正式）可以用不同的 Namespace，確保不會互相影響。
- 團隊或應用程式區隔：不同團隊或應用程式可以使用不同的 Namespace，管理起來更清楚。
- 提高管理靈活度：可以針對 Namespace 設定 RBAC 權限、資源限制（Resource Quota）。
```
apiVerision: v1
kind: Namespace
metadata:
  name: dev
```
