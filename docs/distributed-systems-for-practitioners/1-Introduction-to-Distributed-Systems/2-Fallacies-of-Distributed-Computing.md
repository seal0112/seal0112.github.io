# 分散式系統的謬誤

1. The network is reliable
2. Latency is zero
3. Bandwidth is infinite
4. The network is secure
5. Topology doesn't change
6. There is one administrator
7. Transport cost is zero
8. The network is homogeneous


### 網路是可靠的（The network is reliable）
開發者學習各種技術時會被抽象概念影響，誤以為網路是可靠的，比如，TCP/IP網路連線穩定且不會失敗
網路連線依賴於硬體設備（如路由器、交換機、伺服器），而這些設備遲早會發生故障。因此，在設計系統時，應該考慮網路可能會斷線或出錯的情況，並提供適當的錯誤處理機制，以確保系統的穩定性。

### 0延遲（Latency is zero）
遠端系統的呼叫延遲 與 本地記憶體存取的延遲 之間存在極大的差異：
- 本地的記憶體存取 的延遲約為 奈秒（ns）級。
- 遠端系統呼叫（如 API 或資料庫查詢） 可能需要 毫秒（ms）級。
- 跨資料中心（不同國家 / 洲） 的延遲甚至可能達到 數百毫秒或秒級。

延遲影響在設計 全球分布式系統（Geo-Distributed Systems） 時尤其重要，因此在系統架構設計時，應該考慮網路延遲的影響，並盡量減少不必要的遠端呼叫。

### 頻寬是無限的（Bandwidth is infinite）
跨越網際網路時，我們無法完全掌控頻寬的可用性
- 資料中心內部 頻寬可能高達 100 Gbps 以上。
- 跨資料中心（透過網際網路） 頻寬可能受限，且受到 ISP 限制。
- 流量經過網際網路 可能受 擁塞、限流（throttling）或額外費用 影響。
#### 設計上的考量
- 避免過度依賴高頻寬：如果系統需要頻繁交換大量資料，應考慮 資料壓縮、快取（caching）和邊緣計算（edge computing）。
- 跨資料中心的請求應最小化：在設計分散式系統時，盡可能減少不必要的遠端數據傳輸，以降低頻寬成本和延遲。

### 網路是安全的（The network is secure）
兩個元件之間使用的網路不一定在它們的控制之下

### 網路拓撲不會改變（（Topology doesn't change）
網路由許多不同的部分組成，這些部分可能由不同的組織管理，並使用不同的硬體。此外，當網路的某些部分發生故障時，可能需要改變其拓撲結構，以確保網路仍能運作。

### 只有一個系統管理員（There is one administrator）
實際上，網路可能由不同的組織管理，權限分散。

### 傳輸成本為零（Transport cost is zero）
在兩個節點之間傳輸數據會產生成本。構建分散式系統時，應該將這些成本納入考量。

### 網路是同質的（The network is homogeneous）
現實中，網路由不同的設備、供應商和技術組成，並不一致。