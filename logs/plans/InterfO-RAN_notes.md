# InterfO-RAN 論文閱讀紀錄

> Neagin N. Santhi et al.,  
> *InterfO-RAN: Real-Time In-band Cellular Uplink Interference Detection with GPU-Accelerated dApps*

- 原文 PDF：[`papers/interforan_uplink_interference.pdf`](papers/interforan_uplink_interference.pdf)  
- 對應統整投影片：[`slides/應用 AI 進行網路流量與通道效能估測實現資源配置最佳化.pdf`](slides/應用%20AI%20進行網路流量與通道效能估測實現資源配置最佳化.pdf)

---

## 1. 要解決的問題與重要性

- 在超密集 5G/5G+ 網路中，為了提高頻譜效率，會大量做 **頻譜重用與共享**。  
  → 容易出現「同一頻帶上的上行干擾（in-band UL interference）」。
- 這種干擾會：
  - 拉低受影響 gNB 的 SINR；
  - 破壞 scheduler / resource allocation 的假設（以為沒有干擾）。
- 目前很多干擾分析是「**離線或事後**」的：
  - e.g. 把 I/Q log 下來，之後再分析；
  - 無法在 slot 等級內即時回饋給 RRM / scheduler。
- **InterfO-RAN 的核心問題**：  
  > 如何在 O-RAN 環境中，利用 GPU 加速 dApp，  
  > 在每個 UL slot 內「即時偵測是否有 in-band 干擾」，  
  > 並把結果回饋給上層控制（scheduler / RIC）使用？

---

## 2. 系統環境與 I/O 設定

> 對應簡報：InterfO-RAN 系統架構與流程圖

- **位置與角色**
  - dApp 部署在 **gNB high-PHY** 或類似加速平台（GPU）。
  - 介於 PHY 與 MAC / RRM 之間，屬於 near-real-time 的 data plane 功能。
- **輸入（features）大致包括：**
  - UL PUSCH 的 I/Q sample 或其頻域轉換結果（例如 spectrogram / power map）。
  - 一些 PHY/MAC KPI（MCS、BLER、功率、SINR 等）。
- **輸出**
  - 對每個 UL slot：
    - 是否存在 in-band 干擾（binary / multi-class）。
    - 可能附帶 confidence score。
  - 結果會回報給：
    - gNB 的 scheduler / RRM；
    - 或透過 O-RAN 介面回傳給 RIC 作更高層決策。
- **延遲需求**
  - 必須在 **約 650 μs** 以內完成推論，  
    才能在下一輪排程時使用這個干擾資訊。

---

## 3. 所提方法：GPU-加速 CNN dApp

> 對應簡報：模型架構（CNN）與 dApp pipeline

InterfO-RAN 的核心是把「干擾偵測模型」做成一個 GPU dApp：

1. **資料前處理**
   - 從 gNB 收集大量含干擾 / 無干擾的 UL 片段（在 Colosseum 或實驗平台中重放）。
   - 把 I/Q 資料轉成 CNN 容易吃的格式（例如 time-frequency 圖）。
   - 搭配對應的 label：有干擾 / 無干擾。
2. **模型設計**
   - 使用多層卷積神經網路（CNN）萃取空間／頻率特徵。
   - 最後接上全連接層輸出干擾機率。
   - 模型大小與層數設計需在「準確度」與「推論時間」之間做取捨。
3. **GPU dApp 部署**
   - 將訓練好的 CNN 模型部署到 GPU 上（例如 NVIDIA 平台）。
   - 整個 pipeline（資料進入 → 前處理 → CNN 推論 → 回報結果）  
     必須保證在目標 latency 內完成。
4. **與 O-RAN 架構整合**
   - 干擾偵測結果透過 API 傳給：
     - gNB scheduler（調整排程、功率控制、重傳策略）；
     - 或上傳到 RIC / RRM，做更高層的資源配置。

---

## 4. 實驗設計與主要結果

> 對應簡報：InterfO-RAN 實驗結果與效能圖

- **場景**
  - 模擬多個 cell、不同干擾來源（例如鄰近 cell user、外部干擾源）。
  - 覆蓋各種 SNR / SINR 條件與 traffic pattern。
- **評估指標**
  - 干擾偵測準確率（accuracy、precision/recall、ROC 等）。
  - 處理延遲（end-to-end latency）與 GPU 資源使用量。
- **主要觀察（我整理）**
  1. CNN-based dApp 在大部分情境下可以 **高準確度地區分有／無干擾**。  
  2. 在 GPU 上部署後，推論時間控制在 **< 1 ms**，符合 slot 級即時需求。  
  3. 在比較極端的低 SINR 或干擾型態變化時，準確率會下降，  
     顯示模型仍需要持續更新或增強資料集。

---

## 5. 個人心得與可延伸方向

> 對應簡報最後 InterfO-RAN 的「心得 / 研究方向」頁面

- 這篇比較像是「**把 DL-based 干擾偵測真正塞到 RAN pipeline 裡**」的工程實作，  
  展示在 high-PHY + GPU 上跑 dApp 的可行性。
- 和 PandORA 對照來看，可以想像：
  - InterfO-RAN 提供更精確的「通道與干擾狀態」，  
  - PandORA 這種 DRL xApp 可以用這些資訊做更聰明的 slicing / scheduling。
- 可以延伸思考的點：
  - 干擾偵測結果只回報「有 / 沒有」，未來可否進一步辨識干擾來源或型態？  
  - 如何在資料分布改變（新的干擾 pattern）時，做 online / continual learning？  
  - 和 DRL 控制聯動：  
    - 例如 RL agent 的 state 包含「最近幾個 slot 的干擾偵測結果」，  
    - 讓資源配置策略對干擾更敏感。

---

## 6. 閱讀方式備註

- 目前是配合統整簡報：先看「問題 / 架構 / 方法 / 結果」的圖，再回到原文看細節。  
- 第二輪閱讀會特別注意：
  - 模型輸入特徵到底選了哪些 KPI；
  - 實驗中不同場景（多 cell、不同干擾源）如何設定。
