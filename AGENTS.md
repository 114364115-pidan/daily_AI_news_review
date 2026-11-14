---
name: notion-ai-ready-publisher
description: 將 TechOrange「AI Ready 電子報」的新聞內容整理為手繪筆記風格期刊文章，並透過 Notion MCP 自動同步發布至 Notion「2026」頁面與資料庫中。嚴格僅抓取當天日期的電子報（非當天日期的不抓取）。當使用者要求整理當天/指定日期的 AI Ready 電子報並寫入 Notion、發布科技報橘電子報到 Notion，或提及 notion-ai-ready-publisher 時觸發。
---
# Notion AI Ready Publisher

專為整理 TechOrange《AI Ready 電子報》並將其轉化為白話手繪風格期刊、透過 **Notion MCP** 自動發布至 Notion「2026」頁面所設計的工作流程。

## When to Use

- 使用者要求整理「今天/當天/指定日期」的 TechOrange《AI Ready 電子報》並放入 Notion。
- 使用者要求將科技報橘電子報寫成期刊文章並透過 Notion MCP 儲存至 Notion「2026」頁面或資料庫。
- 觸發詞包含：「整理 AI Ready 到 Notion」、「發布電子報至 2026 頁面」、「AI Ready 期刊寫入 Notion」。

## Target Destination in Notion

- 目標頁面：`我的MBTI是科管人 / 主題新聞 / 2026`
- Page ID: `1c4f014a-b22f-8057-9b03-f838e2908a4b`
- 關聯 Data Source (資料庫 collection ID): `collection://1c4f014a-b22f-80e2-a46c-000b92f4eee6`

## Workflow Steps

### Step 1: 檢索當天電子報內容（嚴格日期過濾）
1. 嚴格對照當天日期（例如 `YYYY-MM-DD`）。使用 Gmail/個人資料檢索工具尋找寄件人為「TechOrange科技報橘」、標題含「AI Ready 電子報」的郵件。
2. **嚴格過濾原則**：僅讀取郵件收件日期與「當天日期」完全相符的電子報。**非當天日期的電子報一律不抓取、不處理**。若當天尚無電子報，即刻回報無當天郵件，不向前追溯舊信。
3. 完整擷取當天電子報中收錄的**所有原始新聞標題與對應連結**，以及新聞內文摘要。

### Step 2: 撰寫 700-1000 字手繪風格期刊文章
1. **字數與風格**：總字數控制在 **700~1000 字**，標題以白話易懂、引人入勝的語言風格呈現，段落排版整齊，重要核心字詞以**藍色粗體**標記（HTML 使用 `<strong style="color: #1565c0;">` 或 `<span class="blue-highlight">`，Notion Markdown 使用 `<span color="blue"><b>...</b></span>`）。
2. **內容架構**：
   - **新聞整體摘要**：全面且深入地提煉當日所有新聞的核心趨勢與產業脈絡。
   - **多篇新聞比較**：進行橫向多角度深度對照（如：技術可行性 vs. 經濟效益、泛用型 vs. 專用型機器人、制度監管 vs. 實體落地）。
   - **課後延伸思考**：提出 2 個具備科技管理思維深度（科管人視角）的討論問題。
   - **核心專有名詞筆記字典 (Glossary)**：列出關鍵專有名詞並附上原文翻譯與簡要定義。

### Step 3: 上傳手繪風格 HTML 並透過 Notion MCP 同步嵌入發布
1. **上傳手繪風格 HTML 附件**：
   - 生成自包含（Self-contained）的單頁 HTML 檔（檔名 `YYYY-MM-DD-ai-ready-journal.html`，含條紋紙底紋、便簽膠帶、圓角手繪邊框、純 CSS/原生字型）。
   - 調用 Notion MCP 的上傳附件工具（`create_attachment`），將完整 HTML 程式碼以 `filename: "YYYY-MM-DD-ai-ready-journal.html"` 上傳至 Notion，取得回傳的 `file-upload://...` 識別碼。
2. **透過 Notion MCP 發布至 Notion 2026 資料庫**：
   - 調用建立頁面工具（`create_pages`），以 `data_source_id: "1c4f014a-b22f-80e2-a46c-000b92f4eee6"` 作為 parent，`icon: "📰"`。
   - 屬性設定：
     - `Name`: `YYYY-MM-DD TechOrange AI Ready 期刊筆記`
     - `Status`: `In progress`（預設為 In Progress 進行中狀態）
     - `Tags`: 依主題選取 `["AI人工智慧", "產業競爭", "創新+創意"]` 等。
   - 內文結構（依 Notion-flavored Markdown 規範排版）：
     - **最頂部**：務必先放置 `<embed source="file-upload://..." />` 標籤，讓 Notion 直接渲染可互動的手繪筆記 HTML 預覽框（切勿放在程式碼區塊或 `<file>` 標籤中）。
     - **上半部**：緊接放入結構化的期刊重點摘要、多篇深度比較、課後延伸思考與專有名詞字典（以 `<span color="blue"><b>...</b></span>` 標記關鍵詞）。
     - **下半部（獨立區塊）**：獨立陳列「原信件新聞標題與連結清單」，將當天所有新聞文章的標題搭配原信件超連結依序列點呈現（例如：`- [新聞標題](新聞連結)`）。
3. **透過 Notion MCP 附加更新至父頁面**：
   - 調用更新頁面工具（`update_page` 的 `insert_content`）將當日期刊精華與排版內容追加至 `1c4f014a-b22f-8057-9b03-f838e2908a4b` 頁面尾端。

## Gotchas

- **互動式 HTML 嵌入語法**：上傳 `.html` 檔取得 `file-upload://...` 後，必須使用 `<embed source="file-upload://..." />`（不可用 `<file>` 或 code block），Notion 才會顯示如 `Interact` 視窗般的可互動預覽卡片。
- **狀態確認**：寫入 Notion 資料庫時，`Status` 欄位必須指定為 `In progress`。
- **原始連結完整保留**：Step 1 抓取時必須忠實保留每篇新聞的原始 URL，並在 Step 3 的頁面下半部完整陳列。
- **日期嚴格性**：絕對不使用昨日或過去日期的電子報充當當天內容，務必核對郵件的收件日期。
- **純自包含樣式**：HTML 檔案中的 CSS 須完全內嵌於 `<style>` 標籤，不引用外部不支援的字型 CDN。
