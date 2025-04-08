---
sidebar_position: 3
---

# Scheduling
Scheduler 負責決定 Pod 要被放在哪一台 Node 上執行。

當你建立一個 Pod（或 Deployment/Job），Pod 物件會先被放入 "pending" 狀態，這時還沒分配在哪一台 Node 上，
Kubernetes 的 scheduler 就會負責「找一台最適合的 Node」來執行這個 Pod。

## Label and Selector


## 污點和容忍度 (Taints and Tolerations)

Taints 是設置在 **Node** 上的屬性，而 Tolerations 是設置在 **Pod** 上的屬性。當一個 Node 被設置了 Taint 時，只有具備相應 Tolerations 的 Pod 才能被調度到該 Node 上。

### 設置 Taint - Node

使用以下指令在 Node 上設置 Taint：

```bash
kubectl taint nodes <node-name> <key>=<value>:<taint-effect>
```

範例：

```bash
kubectl taint nodes node1 app=blue:NoSchedule
```

### 設置Tolerations - PODs
```yml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: nginx-container
    image: nginx

  tolerations:
  - key: app
    operator: Equal
    value: blue
    effect: NoSchedule
```
operator可以設定為Equal或Exists
Equal: 表示 key 和 value 都要 match 才能容忍 taint
Exists: 只要 key 存在就可以容忍，不管 value 是什麼


### Taint Effect 解釋

`taint-effect` 定義了當 Pod 無法忍受 Taint 時會發生的行為：

- **NoSchedule**: 不排 Pod 到該 Node，除非 Pod 有對應 Toleration
- **PreferNoSchedule**: 盡量 不排 Pod 到該 Node（但不是硬性限制）
- **NoExecute**: 不僅不排新 Pod，還會將不具備 Toleration 的舊 Pod 驅逐出 Node


## Node Selectors
- NodeSelector是K8S的排程條件
- 可用來指定 Pod 只能被排到有特定 label 的 Node 上運行。
- 適合用在簡單環境中，不需要太多排程邏輯時使用。
- 對Node加上label，當Pod需要放到指定的Node上時，可以使用NodeSelector去找到有指定label的Node

對 Node 加上 Label
```
kubectl label nodes <node-name> <label-key>=<label-value>
```
範例：
```
kubectl label node node01 size=Large
```

pod:
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      iamge: data-processor
  nodeSelector:
    size: Large
```

缺點和限制:
- 只支援單一條件: nodeSelector 不支援 複數條件的邏輯運算
- 無法指定「不含某 label」的條件: 僅支援精確 match，不能做 range 或排除
- label需要人工維護: 若 Node 有變化（auto scale），需手動管理 label

## 節點關聯性 (Node Affinity)
Node Affinity 是 Kubernetes 用來控制 Pod 要被排在哪些 Node 上的條件，是 nodeSelector 的進階版。

- Scheduling Constraint（排程限制）
- 允許使用複數條件、AND/OR邏輯


## NodeSelector vs NodeAffinity
| 特性 | nodeSelector | nodeAffinity |
| --- | ------------ | ------------ |
|條件複雜度 |
|彈性 |
|功能性 |
|實務上使用 |
