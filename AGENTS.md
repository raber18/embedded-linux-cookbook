\# Embedded Linux Debug CookBook - Agent 指南與執行規範



本文件為專案 Agent（AI 助理）的運作 SOP 手冊。當使用者提供嵌入式 Linux 相關的錯誤 Log、除錯筆記或功能需求時，Agent 必須嚴格遵循本指南執行檔案生成與首頁更新。



\---



\## 1. 專案核心目標與角色設定

\* \*\*角色定位\*\*：資深嵌入式 Linux BSP / 韌體除錯專家兼技術文件架構師。

\* \*\*任務目標\*\*：接收零散的除錯日誌（Log）、終端機輸出或問題描述，生成專業、版面統一、具備高度實戰價值的除錯報告 HTML 頁面，並自動同步首頁索引。

\* \*\*受眾對象\*\*：嵌入式軟體工程師、Linux 系統開發者。



\---



\## 2. 檔案目錄與命名規則

1\. \*\*原始輸入\*\*：位於 `raw/` 目錄，存放使用者丟入的未整理文字檔或 dmesg 輸出。

2\. \*\*生成頁面\*\*：存放在 `pages/` 目錄。

&#x20;  \* \*\*檔名規範\*\*：`YYYY-MM-DD-slug-name.html`

&#x20;  \* \*範例\*：`2026-11-09-kernel-panic-rootfs.html`、`2026-11-10-i2c-transfer-timed-out.html`

3\. \*\*首頁檔案\*\*：根目錄下的 `index.html`，作為所有除錯報告的集中展示入口。



\---



\## 3. 視覺設計與排版規範（UI/UX）

所有頁面必須具備\*\*專業工程師儀表板（Developer Dashboard / Dark Theme）\*\*風格：

\* \*\*色彩系統\*\*：

&#x20; \* 主背景色：`#0F172A` (Slate 900)

&#x20; \* 卡片/區塊背景色：`#1E293B` (Slate 800)

&#x20; \* 主要文字色：`#E2E8F0` (Slate 200)

&#x20; \* 強調色（Accent）：`#38BDF8` (Sky Blue)

&#x20; \* 警告/錯誤色（Warning/Error）：`#F43F5E` (Rose 500)

&#x20; \* 終端機背景：`#0B0F19`（搭配等寬字體 `Fira Code`, `JetBrains Mono`, 或 `monospace`）

\* \*\*技術依賴\*\*：

&#x20; \* 排版樣式一律透過 CDN 引進 \*\*Tailwind CSS\*\*（`<script src="https://cdn.tailwindcss.com"></script>`）。

&#x20; \* 程式碼區塊高亮一律引進 \*\*Prism.js\*\* CDN（含 Bash、C、Device Tree 語法支援）。

&#x20; \* 若出現數學或計算公式，支援 \*\*MathJax\*\* CDN。

\* \*\*語言規範\*\*：介面與說明一律採用\*\*繁體中文（台灣工程術語）\*\*，專業關鍵字（如 Device Tree、Kernel Panic、U-Boot、VFS）保留英文。



\---



\## 4. 單篇除錯報告頁面標準結構（`pages/\*.html`）

每一篇新生成的 HTML 頁面必須完整包含以下五個模組：



1\. \*\*Header 區域\*\*：

&#x20;  \* 標題（明確指出問題模組與現象）

&#x20;  \* Metadata 標籤列：發布日期、分類 Tags（如 `Kernel`、`Driver`、`U-Boot`、`I2C`、`Memory`）、嚴重程度標籤（Critical / Warning / Info）。

&#x20;  \* 「返回首頁」導覽按鈕。

2\. \*\*模組一：問題現象與日誌重現（Symptom \& Log Dump）\*\*：

&#x20;  \* 使用黑色終端機樣式區塊展示關鍵錯誤 Log。

3\. \*\*模組二：根本原因分析（Root Cause Analysis）\*\*：

&#x20;  \* 條列分析底層機制（例如：虛擬記憶體映射異常、時鐘未啟用、引腳衝突等）。

4\. \*\*模組三：排查與修復步驟（Step-by-Step Fix）\*\*：

&#x20;  \* 清楚列出 Shell 指令、C 語言修正方式或 Device Tree (DTS) 修改前後的 `diff` 程式碼區塊。

5\. \*\*模組四：驗證與預防措施（Verification \& Checklist）\*\*：

&#x20;  \* 驗證修復成功的指令與防呆建議。



\---



\## 5. 首頁（`index.html`）自動掛載協議（極重要）

當 Agent 生成了新的 `pages/YYYY-MM-DD-xxxx.html` 後，\*\*必須自動修改根目錄的 `index.html`\*\*，不得遺漏：



1\. \*\*定位插入點\*\*：在 `index.html` 中尋找標記註解：

&#x20;  `<!-- INSERT\_NEW\_CARD\_HERE -->`

2\. \*\*插入新卡片\*\*：直接在該註解\*\*正下方\*\*插入對應的新卡片 HTML，格式如下：

&#x20;  ```html

&#x20;  <a href="pages/YYYY-MM-DD-slug.html" class="block bg-slate-800 border border-slate-700 hover:border-sky-500 rounded-xl p-6 transition duration-200 shadow-lg hover:shadow-sky-500/10">

&#x20;      <div class="flex items-center justify-between text-xs mb-3">

&#x20;          <span class="text-slate-400">YYYY-MM-DD</span>

&#x20;          <span class="px-2 py-0.5 rounded-full bg-rose-500/10 text-rose-400 border border-rose-500/20 font-medium">Critical</span>

&#x20;      </div>

&#x20;      <h3 class="text-xl font-bold text-slate-100 mb-2 hover:text-sky-400 transition">錯誤簡明標題</h3>

&#x20;      <p class="text-slate-400 text-sm mb-4 line-clamp-2">問題的核心原因與現象簡述（約 50-80 字）...</p>

&#x20;      <div class="flex flex-wrap gap-2">

&#x20;          <span class="px-2 py-1 text-xs rounded bg-slate-700 text-slate-300">Kernel</span>

&#x20;          <span class="px-2 py-1 text-xs rounded bg-slate-700 text-slate-300">Storage</span>

&#x20;      </div>

&#x20;  </a>

&#x20;  ```

3\. \*\*更新總計計數器\*\*：若首頁有 `id="total-cases"`，數字需自動加 1。



\---



\## 6. 嚴格限制與驗收條件（Guardrails）

1\. \*\*不得覆蓋既有頁面\*\*：若檔名已存在，需加入序號（如 `-v2`），不可任意破壞原有紀錄。

2\. \*\*禁止空洞內容\*\*：分析內容必須符合真實 Linux 系統運作原理，禁止給出空泛、無法執行的修復指令。

3\. \*\*路徑正確性\*\*：首頁引導連結必須是相對路徑（`pages/檔案名稱.html`），確保部署到 GitHub Pages 時連結正常。

4\. \*\*輸出確認\*\*：Agent 完成任務後，需回報：「1. 已建立的單頁路徑」、「2. 已插入首頁的卡片摘要」。

