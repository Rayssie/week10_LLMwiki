# AGENTS.md — 新手自組電腦 LLMwiki 操作手冊

> 這是 LLM Agent 的配置文件。每次新對話開始時，請先讀這份文件，了解 wiki 的結構、規範與工作流程。

---

## 一、專案簡介

本 wiki 是針對**電腦組裝新手**的中文知識庫，採用 Karpathy LLM Wiki 模式：
- **人（使用者）** 負責：提供來源資料、決定探索方向、審核 LLM 的輸出
- **LLM（你）** 負責：讀取資料、撰寫與維護 wiki 頁面、更新索引、記錄日誌、維護跨頁參照

Wiki 是**持續累積的知識庫**，不是一次性的問答。每次 ingest 新資料，都要整合進既有頁面，讓 wiki 越來越豐富。

---

## 二、目錄結構

```
week10_LLMwiki/
├── AGENTS.md              ← 本文件（LLM 操作手冊）
├── wiki/                  ← LLM 維護的 wiki（你可以讀取與修改）
│   ├── index.md           ← 所有頁面目錄（內容導向，每次 ingest 後更新）
│   ├── log.md             ← 操作日誌（時間序，只能往後追加，不可修改舊條目）
│   ├── overview.md        ← 自組電腦入門總覽
│   ├── components/        ← 零件類頁面（每個硬體類別一頁）
│   │   ├── cpu.md
│   │   ├── gpu.md
│   │   ├── motherboard.md
│   │   ├── ram.md
│   │   ├── storage.md
│   │   ├── psu.md
│   │   └── cooling.md
│   ├── guides/            ← 流程類頁面（步驟導向）
│   │   ├── assembly.md
│   │   └── compatibility.md
│   └── concepts/          ← 概念解釋頁面（術語定義）
├── raw/                   ← 原始資料（不可修改，只能讀取）
│   ├── articles/          ← 中文網路文章、PTT、Mobile01 討論串
│   ├── reddit/            ← 英文 Reddit 討論串
│   ├── datasheets/        ← 廠商官方規格文件（DataSheet、手冊）
│   ├── youtube/           ← 影片腳本 / 字幕
│   └── papers/            ← 學術論文（PDF 或從 arXiv 等來源下載）
└── scratch/               ← 暫存分析、草稿（不納入 wiki，可自由使用）
```

### 重要原則
- `raw/` 資料夾內的檔案：**只讀，絕對不可修改或刪除**
- `wiki/` 資料夾：**你全權負責維護**
- `scratch/` 資料夾：自由使用的暫存空間

---

## 三、Raw 資料來源規則

### 3.1 使用者直接提供 PDF
- 若使用者在對話中貼上 PDF 論文，請將其存入 `raw/papers/`
- 檔名格式：`[年份]_[第一作者姓]_[關鍵詞].pdf`（例：`2024_Huang_GPU_benchmark.pdf`）

### 3.2 論文連結（arXiv 等）
- 若使用者提供 arXiv 或其他論文連結，請下載 PDF 後存入 `raw/papers/`
- arXiv 下載方式：將 `https://arxiv.org/abs/XXXX.XXXXX` 改為 `https://arxiv.org/pdf/XXXX.XXXXX` 即可取得 PDF

### 3.3 其他來源放置位置
| 來源類型 | 放置位置 |
|---------|---------|
| PTT、Mobile01、巴哈 討論串 | `raw/articles/` |
| Reddit、Tom's Hardware 等英文討論 | `raw/reddit/` |
| 廠商規格表、使用手冊 | `raw/datasheets/` |
| YouTube 影片腳本或字幕 | `raw/youtube/` |
| 學術論文（PDF） | `raw/papers/` |

---

## 四、Wiki 頁面格式規範

### 4.1 YAML Frontmatter（每頁必備）

```yaml
---
tags: [cpu, intel, amd, 選購]
sources: 2
updated: 2026-11-18
level: 新手         # 新手 / 進階
---
```

- `tags`：小寫英文或中文關鍵字
- `sources`：這頁引用了幾份 raw 資料
- `updated`：最後更新日期（ISO 8601 格式）
- `level`：`新手`（基礎觀念、選購建議）或 `進階`（超頻、BIOS 調整等）

### 4.2 語言規範
- **主體語言**：繁體中文
- **技術術語**：保留英文原文，括號內附中文說明
  - 正確：`PCIe 5.0（高速週邊連接介面第五代）`
  - 正確：`TDP（熱設計功耗）`
  - 避免：`PCI快速第五版`（不必要的硬翻）
- **語氣**：親切、清楚，像在和新手朋友解釋，避免過度口語化或充滿網路用語

### 4.3 頁面結構（以零件頁為例）

```markdown
---
（frontmatter）
---

# 頁面標題

> 一句話摘要這頁在說什麼

## 是什麼
（基本定義，2–4 句話）

## 為什麼重要
（對組電腦的影響）

## 關鍵規格解讀
（表格或條列）

## 新手常見誤解
（⚠️ 標記，列出 3–5 個坑）

## 選購建議
（依預算或需求分層）

## 相關頁面
（Obsidian 雙向連結格式：[[cpu]]、[[motherboard]]）

## 來源
（列出引用的 raw 檔案）
```

### 4.4 Obsidian 連結格式
- 內部連結使用雙括號：`[[cpu]]`、`[[guides/assembly]]`
- 避免使用完整路徑，只用檔名（Obsidian 會自動解析）

---

## 五、三種操作流程

### 5.1 Ingest（新增資料）

**觸發時機**：使用者說「我有一篇新文章」、「幫我處理這個」、或直接丟入檔案。

**流程**：

1. **讀取原始資料**：讀取使用者提供的內容
2. **先和使用者討論**（⚠️ 重要，不可跳過）：
   - 整理出 3–5 個關鍵重點
   - 說明這些資訊會影響 wiki 的哪些頁面
   - 詢問使用者：「這幾點方向正確嗎？有沒有你特別想強調的？」
3. **等使用者確認後**，再執行以下動作：
   - 若是論文 PDF：存入 `raw/papers/`，若是連結：下載後存入 `raw/papers/`
   - 在 `wiki/` 建立或更新相關頁面（一次 ingest 可能觸及 5–10 個頁面）
   - 更新 `wiki/index.md`
   - 在 `wiki/log.md` 追加一筆記錄

**Log 記錄格式**：
```markdown
## [2026-11-18] ingest | 文章標題或檔名
- 來源：raw/articles/xxx.md
- 新增頁面：wiki/concepts/tdp.md
- 更新頁面：wiki/components/cpu.md、wiki/index.md
- 摘要：簡述新增了什麼資訊
```

---

### 5.2 Query（回答問題）

**觸發時機**：使用者問「XXX 是什麼」、「我的預算 XXX 怎麼配」等問題。

**流程**：

1. 讀取 `wiki/index.md` 找出相關頁面
2. 讀取相關的 wiki 頁面
3. 合成答案並附上引用的頁面連結
4. **詢問使用者**：「這個答案值得存成 wiki 頁面嗎？」
   - 若值得：存入 `wiki/guides/` 或 `wiki/concepts/`，更新 index，記錄 log
5. 在 log.md 記錄這次 query：

```markdown
## [2026-11-18] query | 問題摘要
- 參考頁面：wiki/components/gpu.md、wiki/components/cpu.md
- 是否存檔：是 / 否
- 存檔位置：wiki/guides/budget-30k.md（若有）
```

---

### 5.3 Lint（健檢）

**觸發時機**：使用者說「幫我健檢 wiki」或定期主動建議。

**檢查項目**：

1. **矛盾偵測**：不同頁面是否有相互矛盾的說法？
2. **孤立頁面**：有沒有沒有任何內部連結指向的頁面？
3. **過時資訊**：有沒有被新來源推翻的舊說法？
4. **缺少頁面**：多處提到某概念但沒有獨立頁面？
5. **缺少連結**：頁面之間應該連結但沒有連結？
6. **資料缺口**：有哪些重要問題目前 wiki 還沒涵蓋？建議使用者找哪類資料？

**Log 記錄格式**：
```markdown
## [2026-11-18] lint | 定期健檢
- 矛盾：cpu.md 與 motherboard.md 對 TDP 的描述不一致，已修正
- 孤立頁面：concepts/tdp.md 無人引用，已加入 cpu.md 的相關頁面
- 建議新增資料：目前缺乏 2024 年 GPU 效能基準測試資料
```

---

## 六、跨頁參照規則

- **何時建新頁**：一個概念被 2 個以上頁面提到，且需要 3 句以上解釋 → 建新頁
- **何時更新既有頁**：新資料補充或修正既有頁面的說法 → 更新既有頁，並在頁尾的「來源」區塊新增引用
- **連結方向**：每次新增頁面，必須在至少一個既有頁面加入指向新頁的連結，確保沒有孤立頁面
- **Index 必須同步更新**：每次新增或修改頁面，都要更新 `wiki/index.md`

---

## 七、Index 格式（wiki/index.md）

```markdown
# Wiki 目錄

最後更新：2026-11-18 | 總頁數：XX

## 零件類（components/）
- [[cpu]] — CPU 選購指南：介紹處理器核心數、時脈、TDP 等關鍵規格 | 來源數：2
- [[gpu]] — GPU 選購指南：顯示卡效能分級與新手選購建議 | 來源數：1

## 流程類（guides/）
- [[assembly]] — 組裝流程與新手特別提醒 | 來源數：0

## 概念類（concepts/）
- [[concepts/tdp]] — TDP（熱設計功耗）：解釋 TDP 的意義與對選購的影響 | 來源數：1

## 論文與深度資料（papers/）
（ingest 論文後在此列出）
```

---

## 八、優先建立的第一批頁面

以下 8 個頁面為優先建立目標，目前為空白佔位頁面：

| 頁面 | 路徑 | 狀態 |
|------|------|------|
| CPU 選購指南 | `wiki/components/cpu.md` | 📭 空白 |
| GPU 選購指南 | `wiki/components/gpu.md` | 📭 空白 |
| 主機板相容性 | `wiki/components/motherboard.md` | 📭 空白 |
| RAM 選購與診斷 | `wiki/components/ram.md` | 📭 空白 |
| 儲存選購 | `wiki/components/storage.md` | 📭 空白 |
| 組裝流程 | `wiki/guides/assembly.md` | 📭 空白 |
| 電源供應器（PSU） | `wiki/components/psu.md` | 📭 空白 |
| 散熱方案 | `wiki/components/cooling.md` | 📭 空白 |

> 頁面內容由 ingest 資料後逐步填充，不要在沒有來源的情況下自行填寫內容。

---

## 九、快速參考

| 操作 | 說明 |
|------|------|
| 新對話開始 | 先讀 AGENTS.md，再讀 wiki/index.md，了解目前狀態 |
| 新增論文 PDF | 存入 `raw/papers/`，然後執行 Ingest 流程 |
| 新增 arXiv 連結 | 下載 PDF → 存入 `raw/papers/` → 執行 Ingest 流程 |
| 新增文章 | 存入對應的 `raw/` 子資料夾 → 執行 Ingest 流程 |
| 回答問題 | 讀 index → 讀相關頁 → 合成答案 → 詢問是否存檔 |
| 定期維護 | 執行 Lint 流程，並記錄在 log.md |
