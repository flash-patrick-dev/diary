# 應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化

以 **PandORA** 與 **InterfO-RAN** 兩篇論文為例，整理 O-RAN 中  
「AI 估測網路流量 / 通道效能 → 資源配置最佳化」的相關方法、實驗平台與未來研究方向。

> 指導老師：鄭瑞光 教授  
> 學生：賴晉頡  
> 預定面談時間：**2025-12-01**

---

## 0. 目前規劃說明（依老師要求）

1. **兩篇論文分開讀、分開整理**  
   - PandORA → 先以 PDF（`論文1.pdf`）閱讀，之後會獨立整理成 PandORA 筆記與專用投影片  
   - InterfO-RAN → 先以 PDF（`論文2.pdf`）閱讀，之後也會有自己的筆記與投影片  

2. **一邊讀論文就一邊整理投影片，並在 GitHub 紀錄進度**  
   - 目前已先完成一份「統整簡報」（兩篇一起），檔名：  
     - `應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化 (3).pdf`
   - 接下來的做法是：每完成一個章節（例如 Introduction / System Model / Method / Experiments），就：
     - 在紙本或數位筆記整理重點（之後會匯整成 Markdown 檔）  
     - 更新統整簡報內容，並重新上傳到 GitHub  
   - 隨著進度，會再把 PandORA / InterfO-RAN 各自的專用投影片和筆記搬進獨立檔案或資料夾。

3. **所有截止時間都有明確列出，並預留緩衝時間**  
   - 下方「重要查核點（含預留時間）」表格標示：  
     - 什麼時候、要完成哪些內容  
     - 如有延誤，預計如何利用 buffer 補上  
   - 會先將兩篇論文分開閱讀與做筆記，並同步在 GitHub 上更新規劃。  
   - 會在 **11/29（六）23:00 前**，把 GitHub 連結整理好並提供給老師。

---

## 1. 進度總覽與查核點

> ✅ 已完成／已上傳  ☐ 尚未完成（含預留 buffer）

### 1.1 重要查核點（含預留時間）

| 日期（預定） | 截止時間 | 項目 | 備註 |
|-------------|----------|------|------|
| 11/29 (六)  | 23:00 | ✅ 建立 GitHub repo、撰寫初版 README、上傳目前統整簡報 PDF | 依老師要求，先提供可瀏覽的規劃與簡報 |
| 11/30 (日)  | 23:00 | ☐ PandORA：完成第二輪閱讀與詳細筆記雛型，檢查統整簡報中 PandORA 部分是否對應正確 | 預留 12/01 早上 3 小時作為緩衝，用來補齊或修正 |
| 12/01 (一)  | 12:00 | ☐ InterfO-RAN：完成第二輪閱讀與詳細筆記雛型，確認實驗平台與系統架構的描述 | 面談當天中午前完成初版，上午可依情況微調 |
| 12/01 (一)  | 面談前 | ☐ 最後總整理：快速複習統整簡報與研究興趣投影片，確認 README 中描述與簡報內容一致 | 前一晚先完整跑一遍投影片，面談前預留 30 分鐘做最後確認 |

---

## 2. 目前檔案列表（Repository 現況）

> 目前 Repository 內實際存在的檔案只有三個 PDF：一份統整簡報，兩篇論文原始 PDF。  
> 後續會依進度再新增筆記與更多簡報檔。

- 📄 **統整簡報（目前使用中的簡報檔）**  
  - `應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化 (3).pdf`
- 📄 **論文 1：PandORA 原文 PDF**  
  - `論文1.pdf`
- 📄 **論文 2：InterfO-RAN 原文 PDF**  
  - `論文2.pdf`

> 後續規劃：  
> - PandORA 專用投影片（例如：`PandORA_slides_v1.pdf`）  
> - InterfO-RAN 專用投影片（例如：`InterfO-RAN_slides_v1.pdf`）  
> - 閱讀筆記（例如：`PandORA_notes.md`, `InterfO-RAN_notes.md`）  
> 這些檔案出現後會再更新 README。

---

## 3. 論文列表（兩篇分開閱讀）

### 3.1 PandORA – DRL xApp for Open RAN

- **英文題目**：  
  *PandORA: Automated Design and Comprehensive Evaluation of Deep Reinforcement Learning Agents for Open RAN*
- **中文暫譯**：  
  PandORA：用於開放無線接取網路的深度強化學習代理自動設計與綜合評估
- **主題關聯**：
  - 部署在 **Near-RT RIC** 的 DRL xApp  
  - 讀取各 slice / UE 的流量與通道效能指標  
  - 決定 PRB 比例 / scheduling weight，達成資源配置最佳化與公平性  

**目前對應檔案**

- 論文原文 PDF：`論文1.pdf`  
- 統整簡報中與 PandORA 相關的頁面：收錄於  
  - `應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化 (3).pdf`  

> 後續會將 PandORA 的重點整理成：  
> - PandORA 專用簡報檔（獨立檔名）  
> - PandORA 筆記檔（Markdown），例如 `PandORA_notes.md`

---

### 3.2 InterfO-RAN – CNN dApp for Real-Time Interference Detection

- **英文題目**：  
  *InterfO-RAN: Real-Time In-band Cellular Uplink Interference Detection with GPU-Accelerated dApps*
- **中文暫譯**：  
  InterfO-RAN：以 GPU 加速 dApp 進行蜂巢式上行頻帶即時干擾偵測
- **主題關聯**：
  - 部署在 **gNB high-PHY** 的 CNN dApp  
  - 輸入 UL PUSCH I/Q + PHY/MAC KPI  
  - 在 ~650 μs 內判斷 UL slot 是否有 in-band 干擾，回報給 RRM / Scheduler / RIC  

**目前對應檔案**

- 論文原文 PDF：`論文2.pdf`  
- 統整簡報中與 InterfO-RAN 相關的頁面：同樣收錄於  
  - `應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化 (3).pdf`  

> 後續會將 InterfO-RAN 的重點整理成：  
> - InterfO-RAN 專用簡報檔  
> - InterfO-RAN 筆記檔（Markdown），例如 `InterfO-RAN_notes.md`

---

## 4. 未來資料夾結構規劃（之後會慢慢長出來）

目前實際情況：根目錄只有 `README.md` + 三個 PDF。  
以下是**預計**長成的樣子，方便老師了解之後的整理方向：

```text
.
├── README.md                                  # 本文件
├── 應用 AI 進行網路流量與通道效能估測，實現資源配置最佳化 (3).pdf   # 統整簡報（目前已存在）
├── 論文1.pdf                                   # PandORA 原文 PDF（已存在）
├── 論文2.pdf                                   # InterfO-RAN 原文 PDF（已存在）
├── slides/                                    # 之後：個別簡報
│   ├── PandORA_slides_v1.pdf                  # PandORA 專用投影片
│   └── InterfO-RAN_slides_v1.pdf              # InterfO-RAN 專用投影片
└── notes/                                     # 之後：詳細閱讀筆記（Markdown）
    ├── PandORA_notes.md
    └── InterfO-RAN_notes.md
