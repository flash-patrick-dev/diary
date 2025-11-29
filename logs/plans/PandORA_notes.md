# PandORA 論文閱讀紀錄

> Maria Tsampazi et al.,  
> *PandORA: Automated Design and Comprehensive Evaluation of Deep Reinforcement Learning Agents for Open RAN*  
> IEEE Transactions on Mobile Computing, 2025

- 原文 PDF：[`papers/pandora_open_ran_drl.pdf`](papers/pandora_open_ran_drl.pdf)  
- 對應統整投影片：[`slides/應用 AI 進行網路流量與通道效能估測實現資源配置最佳化.pdf`](slides/應用%20AI%20進行網路流量與通道效能估測實現資源配置最佳化.pdf)

---

## 1. 要解決的問題與重要性

- NextG / 5G+ 的 RAN 要同時服務多種 slice（eMBB、mMTC…），  
  流量與通道狀況高度變動，需要「**可即時調整的智慧控制**」。
- O-RAN 把原本封閉的 RAN 打開，讓 **Near-RT RIC + xApps** 可以做智慧排程與資源分配。
- 目前很多 DRL for RAN 的研究：
  - 只提出「一個」agent 設計，環境與指標不統一；
  - 很難比較不同 state / action / reward / 控制頻率的好壞。
- **PandORA 的核心問題**：  
  > 如何在 O-RAN 架構下，系統性地「設計、訓練、部署、比較」很多種 DRL xApp，  
  > 找出在不同服務需求下較合理的設計準則？

---

## 2. 系統環境與 I/O 設定（我理解的系統模型）

> 對應簡報：PandORA 的「系統架構」與「實驗平台」頁面

- **實驗平台**
  - Colosseum 大型無線測試床 + O-RAN 相容 gNB/UE stack
  - Near-RT RIC 上跑各種 PandORA 產生的 DRL xApps
- **控制目標**
  - 不同 slice（eMBB、mMTC…）的：
    - 吞吐量、封包遺失率、delay、queue length、fairness
- **狀態（state，大致上）**
  - 每個 slice 的 queue/Traffic load
  - PRB 使用情況、SINR / CQI 等通道品質
  - 可能再加上歷史平均 throughput 等統計量
- **動作（action）**
  - 每個 slice 的：
    - PRB 配給比例
    - 或 scheduler weight / priority
  - 有的 agent 是「joint action」（一次決定所有 slice）
  - 有的 agent 是「per-slice action」（獨立決策）
- **回饋（reward）**
  - 基於 slice throughput、delay、QoS 滿足率與 fairness 的組合
  - 不同設計會用不同權重或公式，形成多種 agent 變體

---

## 3. PandORA 架構與方法

> 對應簡報：PandORA framework / DRL agent design 流程圖

PandORA 比較像是一個「**DRL xApp 產生器 + 評測平台**」，不是只提一個單一 agent：

1. **設計空間定義**
   - 可選擇：
     - DRL 演算法：例如 DQN、PPO …
     - state 組合（有哪些 KPI）
     - action 類型（joint / per-slice、控制 slicing 或 scheduling）
     - reward 設計與控制時間間隔（每幾個 slot 做一次決策）
2. **自動化訓練流程**
   - 在 Colosseum / O-RAN Gym 的模擬環境中跑互動
   - 收集 experience，離線訓練多種 DRL agent
3. **封裝成 xApp 並部署**
   - 訓練完的 model 被包成 xApp
   - 在 Near-RT RIC 上線，接收真實 RAN 的 KPI 做即時決策
4. **統一的評測框架**
   - 用同一套 traffic / channel 場景與指標
   - 比較 20+ 種不同設計（不同 reward、action space、time scale…）
   - 看哪些設計在多 slice 情境下「穩定又公平」

---

## 4. 實驗設計與主要觀察

> 對應簡報：PandORA 結果與分析頁面

- **場景**
  - 多個 cell、混合 eMBB / mMTC slice
  - traffic 有 bursty / 平均型，各種 SINR 條件
- **比較對象**
  - 傳統 heuristic（例如預設 PRB 分配、固定 scheduler）
  - 多種 DRL xApp 設計選項（不同 state/reward 等）

**我整理出來的幾個 take-away：**

1. **reward 設計很關鍵**  
   - 只看總 throughput 的 reward → 容易犧牲某些 slice 的 QoS  
   - 納入 fairness / delay 項的 reward → 整體指標稍降，但 QoS 滿足度比較穩。
2. **決策時間尺度要和系統反應時間匹配**  
   - 太頻繁控制 → agent 容易 overreact，訓練也較不穩定  
   - 太慢控制 → 無法跟上 traffic 波動。
3. **joint vs per-slice action 有取捨**  
   - joint action 比較有機會學到「全局最適」，但 action 空間大、訓練難；  
   - per-slice 比較好訓練，但可能需要搭配額外機制避免互搶資源。

---

## 5. 我自己的心得與可能延伸方向

> 對應簡報最後 PandORA 的「心得 / 研究方向」頁面

- PandORA 把 **「如何設計 DRL xApp」這件事系統化**，  
  讓之後要做 RAN 智慧控制的人有一個「設計空間 + baseline」可以參考。
- 對我來說，它給的不是一個 final model，而是一套 **比較框架**：
  - 以後要做新的 state/action/reward，可以直接放進這個 pipeline 比較。
- 可以想的延伸題目（for 未來研究）：
  - 把 InterfO-RAN 這類「干擾偵測結果」當成 PandORA 的 state，  
    做 interference-aware 的 slicing / scheduling。
  - 把「設計空間搜尋」再往 AutoML 方向延伸（例如自動搜尋 network architecture）。
  - 思考在真實部署時，如何做 online fine-tuning 或 safety 機制。

---

## 6. 閱讀方式備註

- 主要是搭配統整簡報閱讀：先看簡報的問題與架構，再回論文找細節。  
- 目前第二輪閱讀時，會把「還沒在簡報畫出來的圖／流程」補上，  
  避免出現只看文字沒圖的頁面。
