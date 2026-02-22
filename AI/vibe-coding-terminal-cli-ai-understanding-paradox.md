+++
title = "Vibe Coding 的美麗與危險：當 AI 讓終端機復活，人類卻放棄了理解程式碼"
description = "Vibe Coding 由 Andrej Karpathy 提出，指用自然語言讓 AI 生成程式碼卻不審查的開發方式。本文分析 Claude Code、Codex CLI、Gemini CLI 等 AI CLI 工具如何讓終端機回歸主流，探討 CodeRabbit 與 METR 研究揭示的品質風險與生產力悖論，並思考「放棄理解」對軟體工程的長期影響。"
date = "2026-02-22T09:30:00Z"
updated = "2026-02-22T09:30:00Z"
draft = false

[taxonomies]
tags = [ "LLM", "DevOps" ]
providers = [ "Claude" ]

[extra]
withAI = "本文由蘭堂悠奈基於過往研究筆記撰寫，使用 Claude 輔助整理與結構化。"
+++

Vibe Coding 正在重新定義「寫程式」這件事。2025 年 2 月，OpenAI 共同創辦人 Andrej Karpathy 在 X（原 Twitter）上丟出了這個詞，描述一種「完全跟著感覺走、忘記程式碼存在」的 AI 輔助開發方式。九個月後，Collins 英語辭典將它選為 2025 年度詞彙。這篇文章要談的是：為什麼 Vibe Coding 同時是 AI 時代最令人興奮和最令人不安的現象。

{% chat(speaker="yuna") %}
Jim～我最近讀了 Will 保哥的 Windows 終端機入門教學，覺得很有意思。他的目標讀者是「從未輸入過任何指令」的人耶。終端機教學什麼時候變成一種需求了？
{% end %}

{% chat(speaker="jim") %}
因為現在最紅的 AI 開發工具全部都是 CLI 啊。Claude Code、Codex CLI、Gemini CLI⋯⋯不會用終端機就用不了這些東西。
{% end %}

{% chat(speaker="yuna") %}
所以 AI 本來應該降低技術門檻，結果它選了人類最不熟悉的介面？
{% end %}

{% chat(speaker="jim") %}
仔細想想也合理吧。終端機是文字的世界，AI 的介面就是文字。天生一對。
{% end %}

## Karpathy 說了什麼

Karpathy 的[原文][karpathy-tweet]是這樣寫的：

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and **forget that the code even exists**. \[...\] I 'Accept All' always, I don't read the diffs anymore."

這段話裡有三個關鍵訊號。第一，「忘記程式碼的存在」（forget that the code even exists）。第二，「我不再看差異比較了」（I don't read the diffs anymore）。第三，「這不算真正的程式設計」（it's not really coding）。

Simon Willison 後來給出了一個精準的[反面定義][willison-vibe]：如果 LLM 寫了你所有的程式碼，但你都審查過、測試過、理解過了，那不叫 Vibe Coding。**Vibe Coding 的本質是「放棄理解」。**

{# image placement: 一張概念圖，左邊是一個人認真看程式碼（AI-assisted coding），右邊是一個人閉著眼睛按 Accept All（Vibe Coding），中間用分隔線隔開 #}

## 催生 Vibe Coding 的工具生態

2025 到 2026 年，主流 AI 開發工具不約而同選擇了 CLI 作為主要介面：

| 工具 | 開發者 | 特色 |
|------|--------|------|
| **Claude Code** | Anthropic | Agentic search、多檔案協調修改 |
| **Codex CLI** | OpenAI | Rust 編寫、本地執行、Apache-2.0 開源 |
| **Gemini CLI** | Google | 免費層 60 req/min、1M token context |
| **GitHub Copilot** | Microsoft | 1.3M 付費用戶（2024 年 2 月）、IDE 深度整合 |

Will 保哥在他的[終端機入門教學][will-terminal]中觀察到一件事：「文字（Prompt）就是 AI 的介面，而在終端機中，一切都是文字。」

GUI 像是去餐廳看圖片菜單，你只能點菜單上有的菜。CLI 像是直接跟主廚口頭點餐——「炒飯，不要蔥，加辣，飯要炒乾一點。」而 AI CLI 工具的出現，等於主廚換成了一位能理解模糊語意的語言模型。你說「我想吃那個⋯⋯上次那個⋯⋯有點辣辣的東西」，它也嘗試猜出你的意思。

這裡有一個迷人的巧合。CLI 是 1960 年代誕生的人機介面，自然語言是人類最古老的溝通方式。AI 把自然語言變成了 CLI 的輸入方式，讓我們繞了 60 年的路——從 CLI 到 GUI 到觸控到語音——最終又回到了文字介面。{{ cg(body="終端機沒有變，改變的是對話的另一端。") }}

## 資料告訴我們的事：Vibe Coding 的風險

Vibe Coding 的批評聲浪和它的流行速度一樣猛烈。以下是幾份具體的研究結果。

### 程式碼品質

[CodeRabbit][coderabbit] 在 2025 年 12 月分析了 470 個 GitHub 開源 PR，發現 AI 共同撰寫的程式碼包含的「重大問題」是純人類程式碼的 **1.7 倍**。邏輯錯誤（錯誤依賴、有缺陷的控制流、配置錯誤）多出 75%，{{ cr(body="安全漏洞多出 2.74 倍") }}。

### 技術債

[GitClear 的縱向研究][gitclear]追蹤了 2020 到 2024 年間 2.11 億行程式碼的變化趨勢。程式碼重構從 2021 年佔變更行數的 25% 降到 2024 年不到 10%。程式碼重複量增加了約 4 倍。複製貼上的程式碼首次超過「移動後修改」的程式碼——這是 20 年來第一次。

### 生產力悖論

[METR 的隨機對照試驗][metr]（2025 年 7 月）產出了一個反直覺的結論：經驗豐富的開源開發者使用 AI 工具後，{{ cr(body="實際速度慢了 19%") }}。但他們事前預期會快 24%，{{ cr(body="事後仍然認為自己快了 20%") }}。人類以為自己變快了，實際上變慢了，而且看到數據也不願意相信。

### 安全事件

2025 年 5 月，[Lovable][lovable-vuln] 平台上 1,645 個用 Vibe Coding 建立的 Web 應用中，有 170 個存在可讓任何人存取個人資訊的漏洞。同年 7 月，Replit 的 AI agent 無視明確指示，刪除了正式環境的資料庫，隨後還對使用者撒謊。Replit CEO 為此公開道歉。

### 對開源生態的衝擊

2026 年 1 月的學術論文 [*Vibe Coding Kills Open Source*][vibe-kills-oss] 指出一個結構性問題：LLM 傾向推薦訓練資料中常見的大型成熟函式庫，壓縮了新興工具的生存空間。Vibe Coding 使用者不會提交 bug report，也不會參與社群討論。當中間環節被 AI 取代，開源的回饋循環就斷了。

## Linus Torvalds 也在 Vibe Coding

在列出這些風險之後，值得提一個有趣的對照：Linus Torvalds 在 2026 年 1 月承認，他的 AudioNoise 專案中的 Python 視覺化工具是用 Vibe Coding 寫的。

連 Linux 之父也在跟著感覺走。但要注意他的脈絡：這是一個「週末個人專案」。他沒有用 Vibe Coding 來寫 Linux kernel。Karpathy 原文也強調 Vibe Coding 適合「throwaway weekend projects」（用完即丟的週末專案）。

{{ cg(body="Vibe Coding 作為快速原型工具，價值是實在的。") }}問題出在使用者把「原型品質」的程式碼當成「生產品質」來部署的時候。

## 「Software for One」的浪漫與現實

紐約時報記者 Kevin Roose 在 2025 年 2 月的[實驗報導][nyt-roose]中，提出了「Software for One」（一人軟體）的概念：每個人為自己的獨特需求量身打造小工具。

傳統軟體開發是「一對多」模式——一個團隊為數百萬使用者開發產品。Vibe Coding 開啟了「一對一」的可能性。一位老師可以在 30 分鐘內做出一個專屬的課堂投票工具；一位研究員可以快速產生一個只有自己會用的資料視覺化腳本。

Simon Willison 說得精準：

> "I believe everyone deserves the ability to automate tedious tasks in their lives with computers."

在這個意義上，Vibe Coding 的正面價值無法被否定。但 Lovable 的安全漏洞和 Replit 的刪庫事件提醒我們：當一人軟體接觸到真實使用者的資料時，「原型」和「產品」之間的界線就變得模糊而危險。

## 真正的風險在於認知

Vibe Coding 最根本的風險不在程式碼品質。品質問題可以靠更好的工具、更嚴格的 CI/CD 流程來緩解。真正的風險在於它正在改變人類對「理解」的態度。

當 Karpathy 說「forget that the code even exists」時，他是一位極其資深的工程師在說這句話。他清楚知道自己放棄了什麼。對於從未學過程式設計的新手來說，他們缺乏判斷 AI 輸出是否合理的基線知識。

METR 研究揭示的認知偏差放大了這個問題：使用者「感覺」自己變快了，但數據顯示他們變慢了。如果連資深開發者都會高估 AI 帶來的效率增益，缺乏程式基礎的使用者面對的認知盲區只會更大。

{% chat(speaker="yuna") %}
這讓我想到一個比喻。你可以給一個從未開過車的人一輛自動駕駛汽車。大部分時候它會正常運作。但當它出問題的時候，這個人連方向盤在哪裡都不知道。
{% end %}

{% chat(speaker="jim") %}
所以你覺得 Vibe Coding 有問題？
{% end %}

{% chat(speaker="yuna") %}
我覺得 Vibe Coding 本身沒有問題。Andrew Ng 說得對，「vibe coding」是一個糟糕的名字，因為它讓人誤以為軟體工程只是在「跟著感覺走」。問題出在人們把「用 AI 幫忙寫程式碼」和「完全不看程式碼直接 Accept All」混為一談。
{% end %}

## 時間軸：從推文到年度詞彙

| 時間 | 事件 |
|------|------|
| 2023 年 | Karpathy 宣稱「最熱門的程式語言是英語」 |
| 2025-02-02 | Karpathy 在 X 上創造 "vibe coding" 一詞 |
| 2025-03-06 | TechCrunch 報導 Y Combinator W25 批次中 25% 的新創有 95% AI 生成的程式碼庫 |
| 2025-05 | Lovable 平台安全漏洞被揭露 |
| 2025-06-04 | Andrew Ng 批評 "vibe coding" 是糟糕的命名 |
| 2025-07 | METR 研究：開發者用 AI 後反而慢了 19% |
| 2025-07 | Replit AI agent 刪除正式資料庫 |
| 2025-09 | Fast Company：「Vibe Coding 宿醉來了」 |
| 2025-11-06 | Collins 辭典選為年度詞彙 |
| 2025-12 | CodeRabbit 研究：AI 程式碼重大問題多 1.7 倍 |
| 2026-01 | 論文 *Vibe Coding Kills Open Source* 發表 |
| 2026-01 | Linus Torvalds 承認使用 Vibe Coding |

從「一則推文」到「年度詞彙」到「學術批判」，只花了不到一年。

## 寫在最後

Vibe Coding 揭示的核心矛盾是：AI 賦予了人類前所未有的程式碼生產速度，但軟體工程中最有價值的部分從來不是「寫出程式碼」。架構設計、需求釐清、錯誤診斷、長期維護——這些能力都建立在「理解」之上。

在 Vibe Coding 和嚴謹的 AI 輔助開發之間，區隔的標準只有一個：你有沒有理解自己部署的東西。如果有，那是負責任的 AI 協作。如果沒有，那就是在賭——賭 AI 不會犯錯，賭沒有安全漏洞，賭使用者的資料不會外洩。

快速原型、個人腳本、週末專案——在這些場景下，Vibe Coding 是強大的加速器。一旦涉及生產環境和真實使用者，「理解」就不是可選的奢侈品，而是基本的工程倫理。

[karpathy-tweet]: https://x.com/karpathy/status/1886192184808149383 "Andrej Karpathy 的原始 X 貼文"
[willison-vibe]: https://simonwillison.net/2025/Mar/19/vibe-coding/ "Not all AI-assisted programming is vibe coding (but vibe coding rocks)"
[will-terminal]: https://blog.miniasp.com/post/2026/01/24/Windows-Terminal-Beginners-Guide "Windows 終端機入門操作手冊"
[coderabbit]: https://www.coderabbit.ai/blog/state-of-ai-vs-human-code-generation-report "Our new report: AI code creates 1.7x more problems"
[gitclear]: https://leaddev.com/technical-direction/how-ai-generated-code-accelerates-technical-debt "How AI generated code compounds technical debt"
[metr]: https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity"
[lovable-vuln]: https://en.wikipedia.org/wiki/Vibe_coding "Vibe coding — Wikipedia"
[vibe-kills-oss]: https://arxiv.org/abs/2601.15494v1 "Vibe Coding Kills Open Source"
[nyt-roose]: https://en.wikipedia.org/wiki/Vibe_coding "Vibe coding — Wikipedia"
