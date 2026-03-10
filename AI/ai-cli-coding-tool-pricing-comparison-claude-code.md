+++
title = "AI CLI 編碼工具價格大比拼：Claude Code、Codex、Gemini 全面解析"
description = "深入比較 Claude Code、Codex CLI 與 Gemini CLI 的專業與企業版授權價格計算方式。解析訂閱、API 按量與免費增值模式的差異。協助開發者選擇最適合的 AI CLI 工具！"
date = "2025-08-06T05:36:30Z"
updated = "2025-08-07T17:30:15Z"
draft = false

[taxonomies]
tags = [ "LLM" ]
providers = [ "Felo Search" ]

[extra]
withAI = "<https://felo.ai/search/afe4zqJoGk6F225Q3Qc9zt?invite=dOLYGeJyZJqVX>"
hot = true
archive = "本文已過時。請見新文章：[2026 年 AI CLI 編碼工具價格大比拼：Claude Code、Codex CLI、Gemini CLI、GitHub Copilot](@/AI/ai-cli-coding-tool-pricing-comparison-2026/index.md)"
+++
{% chat(speaker="jim") %}
請你比較 AI CLI coding tool pricing including Claude Code, Codex CLI, Gemini CLI. 需詳列專業、企業版授權價格計算方式。Skip cloud tools like GitHub coding agent.
{% end %}

{% chat(speaker="felo") %}
在終端機原生 AI 程式設計工具的競爭中，三大巨頭——Anthropic、OpenAI 與 Google——採取了截然不同的定價策略，反映了其市場目標與產品定位的差異。
{% end %}

Google 的 Gemini CLI 以其極具顛覆性的「近乎無上限」免費方案，旨在迅速佔領個人開發者市場，將高階模型能力普及化[^90][^132]。Anthropic 的 Claude Code 則鎖定專業與重度使用者，透過分級訂閱制（Pro/Max）提供整合式體驗，並對企業用戶採取付費 API 模式，強調其高品質與生產力價值[^129][^138]。OpenAI 的 Codex CLI 則維持最純粹的開發者導向模式，工具本身開源，完全依賴 API 按用量計費，與其 ChatGPT 訂閱服務完全脫鉤，為使用者提供最大的彈性，但也帶來了成本的不確定性[^88][^128]。

對於個人開發者而言，Gemini CLI 的免費增值模式無疑最具吸引力。對於追求極致生產力且預算充足的專業人士，Claude Code 的 Pro 或 Max 訂閱方案提供了強大的整合功能[^101][^156]。而對於需要將 AI 功能整合到既有工作流程或偏好按實際用量付費的開發者及企業，Codex CLI 的 API 模式則提供了最高的靈活性[^109]。企業採購方面，Google 與 Anthropic 均轉向按人頭計算的授權與 API 用量計費模式，顯示 CLI 工具已成為企業級 AI 開發解決方案的重要一環。

## Claude Code 定價模型

Anthropic 為 Claude Code 設計了多層次的定價結構，以滿足從個人生產力使用者到大型企業團隊的不同需求。其核心策略是將 CLI 工具的使用權與其高階訂閱方案綁定，或針對企業提供 API 計價模式[^129]。

### 個人與專業方案 (Pro / Max)

* Claude Pro 方案：此方案是個人開發者使用 Claude Code 的主要途徑。月費為 20 美元，若採年繳則為每月 17 美元（一次性支付 200 美元）[^129][^135][^153]。訂閱 Claude Pro 的使用者可直接在其終端機中存取和使用 Claude Code，無需額外支付 API 費用[^129]。許多使用者認為此方案物超所值，因為其實際 API 使用量價值可能遠超月費[^101][^159]。
* Claude Max 方案：針對需要更高用量的重度使用者，Anthropic 推出了 Max 方案。此方案分為兩級：每月 100 美元可獲得 5 倍於 Pro 方案的使用量，每月 200 美元則可獲得 20 倍用量[^117][^118][^129]。此方案同樣包含 Claude Code 的使用權，適合將 AI 編碼作為核心工作流程的開發者[^138][^147]。

### 團隊與企業方案

* Claude Team 方案：此方案的定價為每位使用者每月 30 美元（年繳則為 25 美元），最少需 5 位成員[^129]。然而，與 Pro/Max 方案不同，Team 方案不直接包含 Claude Code 的無限使用權。團隊成員需透過 Anthropic Console 啟用 Claude Code，其使用將完全按照 API 用量計費[^129]。
* Claude Enterprise 方案：此方案採客製化報價，需與銷售團隊聯繫[^129]。與 Team 方案類似，企業版使用者存取 Claude Code 同樣是透過獨立的 API 金鑰，並按實際消耗的 token 數量付費[^129]。這種模式讓企業能更精確地控制和審核 AI 工具的支出。

### API 按量計費 (適用於團隊與企業)

對於 Team 和 Enterprise 方案，Claude Code 的成本取決於所選模型與 token 使用量。Anthropic 提供了不同效能與成本的模型[^129]：

* **Claude Opus 4**：最強大的模型，適用於複雜任務。輸入成本為每百萬 tokens 15 美元，輸出成本為每百萬 tokens 75 美元[^129]。
* **Claude Sonnet 4**：在智慧、速度與成本之間取得平衡。輸入成本為每百萬 tokens 3 美元，輸出成本為每百萬 tokens 15 美元[^129]。

## OpenAI Codex CLI 定價模型

OpenAI 的 Codex CLI 採取了與其主要產品 ChatGPT 完全不同的定價策略。它是一個開源的命令列工具，其使用成本完全基於後端 API 的呼叫，與任何訂閱方案無關[^1][^6][^15]。

### 定價基礎：純 API 按量計費

Codex CLI 的使用沒有月費或訂閱費[^109][^121]。使用者只需擁有一個有效的 OpenAI API 金鑰，所有操作都會根據消耗的 token 數量計費[^15][^28][^88]。這種模式為開發者提供了極大的彈性，但也意味著成本與使用頻率和任務複雜度直接相關。

### 模型與成本

* Codex CLI 預設使用 `o4-mini` 模型，這是一個為輕量級代理程式優化的模型[^28][^37]。
* 其對應的 API 模型 `codex-mini-latest` 的定價為：
  * **輸入**：每百萬 tokens 1.50 美元[^100][^125][^140]。
  * **輸出**：每百萬 tokens 6.00 美元[^100][^125][^140]。
* 使用者也可以設定工具使用其他 OpenAI 模型，如 `o3` 或 `GPT-4.1` 系列，費用將依據所選模型的官方 API 定價計算[^37]。

### 與 ChatGPT 訂閱的關係

一個關鍵的區別是，**Codex CLI 的使用成本不包含在任何 ChatGPT 訂閱方案（Plus、Team 或 Enterprise）中**[^128][^146]。即使是每月支付 200 美元訂閱 ChatGPT Pro 的使用者，在使用 Codex CLI 時仍需另外支付 API 費用，這點曾引起部分使用者的不滿[^128]。雖然新使用者或訂閱者有時會獲得一次性的 API 點數贈金（例如 5 美元或 50 美元），但這並非持續性的福利[^18][^134]。

這種將 CLI 工具與訂閱服務完全分離的策略，凸顯了 OpenAI 將 Codex CLI 定位為一個純粹的開發者工具，而非其主流消費者產品的延伸。

## Google Gemini CLI 定價模型

Google 透過 Gemini CLI 採取了極具侵略性的市場滲透策略，為個人開發者提供了業界最慷慨的免費方案，同時也為企業使用者規劃了清晰的付費升級路徑[^132]。

### 個人開發者方案：近乎無上限的免費使用

* **免費存取**：對於使用個人 Google 帳戶登入的開發者，Gemini CLI **完全免費**[^90][^104][^124]。使用者會自動獲得一個免費的 Gemini Code Assist 授權[^90][^102][^114]。
* **慷慨的用量限制**：此免費方案提供對強大的 Gemini 2.5 Pro 模型的存取，包含高達 100 萬 token 的上下文視窗[^102][^112][^127]。其使用限制為：
  * 每分鐘 60 次請求[^92][^112][^132]。
  * 每日 1,000 次請求[^92][^112][^132]。
    這個額度對於絕大多數個人開發和學習用途而言綽綽有餘，使其成為目前市場上最具吸引力的選擇[^124]。

### 企業方案 (Standard / Enterprise)

對於企業或需要更高用量、更強管理功能的團隊，Google 提供了基於 Gemini Code Assist 訂閱的付費方案。Google Workspace 使用者必須擁有付費授權才能使用[^116]。

* **Gemini Code Assist Standard**：此方案為團隊提供付費授權，價格約為每位使用者每月 19 至 22 美元[^148][^154]。
* **Gemini Code Assist Enterprise**：提供更進階的功能和更高的用量，價格約為每位使用者每月 45 美元[^154]。購買此方案有最低授權數量的要求，例如至少 10 個授權[^80][^131]。
* **授權管理**：企業管理員需先購買訂閱方案，然後將授權指派給機構內的個別使用者[^131]。獲得授權的使用者即可使用 Gemini CLI 及其他 Gemini 功能。

### API 按量計費選項

對於需要將 Gemini CLI 整合到自動化腳本或需要執行多個代理的專業開發者，也可以選擇不使用免費方案，而是透過 Google AI Studio 或 Vertex AI 的 API 金鑰來按量計費[^110][^132]。

## 綜合比較與分析

下表總結了三款主流 AI CLI 編碼工具的定價模型與核心特點：

| 特性 | Claude Code (Anthropic) | OpenAI Codex CLI (OpenAI) | Google Gemini CLI (Google) |
| :--- | :--- | :--- | :--- |
| **定價基礎** | 訂閱制 (Pro/Max) 或 API 按量計費 (Team/Enterprise)[^129] | 純 API 按量計費[^88][^121] | 免費增值模式，企業版為訂閱制[^90][^131] |
| **個人方案** | **Pro**：$20/月 (或 $17/月年繳)，包含 CLI 使用權[^129][^135] | 無特定方案，按 API 用量計費[^121] | **免費**，需個人 Google 帳戶。每日 1,000 次請求，每分鐘 60 次[^92][^132] |
| **專業方案** | **Max**：$100/月 (5x 用量) 或 $200/月 (20x 用量)[^117][^129] | 無特定方案，按 API 用量計費[^121] | 可選用 Google AI Studio 金鑰按量計費[^110] |
| **團隊方案** | **Team**：$30/人/月 (最少 5 人)，CLI 使用需**額外 API 計費**[^129] | 無特定方案，按 API 用量計費[^121] | **Standard**：約 $19-$22/人/月[^148][^154] |
| **企業方案** | **Enterprise**：客製化報價，CLI 使用需**額外 API 計費**[^129] | 無特定方案，按 API 用量計費[^121] | **Enterprise**：約 $45/人/月 (最少 10 人)[^80][^154] |
| **核心優勢** | 高品質模型與生產力工具整合，Pro 方案性價比高[^101] | 模式單純、彈性最大，與平台無綁定[^109] | 極具競爭力的免費方案，大幅降低個人開發者門檻[^90][^132] |
| **主要限制** | 團隊與企業方案成本較高且計算複雜[^129] | 成本難以預估，與 ChatGPT 訂閱無關[^128] | 企業版需綁定 Google Workspace 與 Code Assist 授權[^116] |

### 策略洞察

* **Google 的市場佔領策略**：Gemini CLI 的免費模式顯然是為了快速獲取開發者用戶，建立生態系。透過提供強大的免費模型和極高的使用額度，Google 意圖在開發者心中建立「預設選擇」的地位，後續再透過企業版授權實現商業變現。
* **Anthropic 的價值導向策略**：Claude Code 將其 CLI 工具定位為高階生產力工具，是其 Pro/Max 訂閱價值的核心部分。對企業用戶採 API 計費，則反映了其對模型效能的自信，相信企業願意為高品質的結果付費。
* **OpenAI 的傳統開發者策略**：Codex CLI 堅持了傳統 API 服務的模式，工具與平台分離，使用者按需付費。這給予了開發者最高的自由度，但也使其在與 Google 的免費模式競爭時處於劣勢，主要吸引對 OpenAI 模型有特定偏好或已深度整合其 API 的使用者。

[^1]: [如何本地部署和使用OpenAI Codex CLI协助开发者 - 闪电博](https://www.wbolt.com/openai-codex-cli.html)
[^6]: [OpenAI 推出Codex CLI,這是一個新的開放原始碼代理程式,該 ...](https://www.threads.com/@power.ai/post/DJs65gJTjHU/video-openai-launches-codex-cli-a-new-open-source-coding-agent-that-runs-locally-in-yo?hl=zh-tw)
[^15]: [OpenAI Codex Cli - stardsd - 博客园](https://www.cnblogs.com/sddai/p/18830867)
[^18]: [Pro 的訂閱者更別錯過50 美金Codex CLI 現在安裝起來就能領 ...](https://www.facebook.com/groups/gaitech/posts/1394215388429339/)
[^28]: [如何将OpenAI的Codex CLI工具集成到您的开发工作流程中](https://blog.openreplay.com/zh/%E9%9B%86%E6%88%90-openai-codex-cli-%E5%B7%A5%E5%85%B7-%E5%BC%80%E5%8F%91%E5%B7%A5%E4%BD%9C%E6%B5%81/)
[^37]: [OpenAI Codex CLI 全面解析：本地AI 编码助手 - API易](https://help.apiyi.com/openai-codex-cli-apiyi-guide.html)
[^80]: [設定Gemini Code Assist Standard 和Enterprise - Google Cloud](https://cloud.google.com/gemini/docs/discover/set-up-gemini?hl=zh-tw)
[^88]: [OpenAI Codex：AI 程式開發助手的新時代](https://tenten.co/learning/openai-codex/)
[^90]: [Gemini CLI：你的開源AI 代理 - Google Blog](https://blog.google/intl/zh-tw/products/cloud/gemini-cli-your-open-source-ai-agent/)
[^92]: [Google全新開源AI代理Gemini CLI 無上限使用](https://itpromag.com/2025/06/26/google-gemini-cli/)
[^100]: [總結了OpenAI 最新SWE agent Codex的一些資訊 - Threads](https://www.threads.com/@neuro.pul/post/DJuGLGApR8a/%E7%B8%BD%E7%B5%90%E4%BA%86-openai-%E6%9C%80%E6%96%B0swe-agent-codex%E7%9A%84%E4%B8%80%E4%BA%9B%E8%B3%87%E8%A8%8A13-%E5%89%8D%E6%9C%9F%E6%9C%89%E5%85%8D%E8%B2%BB%E9%A1%8D%E5%BA%A6-%E7%A0%94%E7%A9%B6%E9%A0%90%E8%A6%BD%E6%9C%9Fcodex-%E7%9B%AE%E5%89%8D%E4%BB%A5research-preview%E5%BD%A2%E5%BC%8F%E5%90%91-ch?hl=ja)
[^101]: [用了Claude Code感覺就是天天賺！ 20 美元月費 ... - Threads](https://www.threads.com/@newmobilelife/post/DLLa-RPtJU3/%E7%94%A8%E4%BA%86-claude-code%E6%84%9F%E8%A6%BA%E5%B0%B1%E6%98%AF%E5%A4%A9%E5%A4%A9%E8%B3%BA20-%E7%BE%8E%E5%85%83%E6%9C%88%E8%B2%BB%E8%A8%82%E9%96%B1-claude-pro-%E7%9C%9F%E7%9A%84%E6%98%AF%E8%B6%85%E5%80%BC%E6%9C%80%E5%A4%9A%E7%9A%84%E4%B8%80%E5%A4%A9%E7%94%A8%E4%BA%86-35-%E7%BE%8E%E5%85%83%E9%A1%AF%E7%A4%BA%E7%94%A8%E9%87%8F%E6%96%B9%E5%BC%8F%E5%9C%A8%E7%95%99%E8%A8%80%E5%8D%80)
[^102]: [來嘗試用Gemini CLI 來撰寫Google ADK AI Agent 程式碼吧！](https://medium.com/@simon3458/%E6%96%B0-gemini-%E6%87%89%E7%94%A8-gemini-cli-%E5%B7%A5%E5%85%B7-%E4%BE%86%E5%98%97%E8%A9%A6%E7%94%A8-gemini-cli-%E4%BE%86%E6%92%B0%E5%AF%AB-google-adk-ai-agent-%E7%A8%8B%E5%BC%8F%E7%A2%BC%E5%90%A7-9c91d6e102c5)
[^104]: [Gemini CLI：你的終端機AI 神隊友，讓開發工作效率爆棚！](https://most.tw/posts/ainews/gemini-cli-your-ai-sidekick-202506/)
[^109]: [AI程式夥伴：Jules、Codex、Claude Code與Copilot的四方會戰](https://makerpro.cc/2025/06/jules-codex-claudecode-copilot/)
[^110]: [【資源分享】開發者必備！Google 推出Gemini CLI - AI郵報](https://www.aiposthub.com/kai-fa-zhe-bi-bei-google-tui-chu-gemini-cli-rang-ai-jin-zhu-ni-de-zhong-duan-ji/)
[^112]: [Google 發佈Gemini CLI 挑戰Claude Code、Codex CLI](https://today.line.me/hk/v3/article/Op2NgxB)
[^114]: [Google Gemini CLI 深度解析：瞄準開發者終端(terminal) 的AI ...](https://ikala.ai/zh-tw/blog/ikala-ai-insight/google-gemini-cli-terminal-ai-agent-ch/)
[^116]: [Firebase 專用Gemini - Google](https://firebase.google.com/docs/gemini-in-firebase?hl=zh-tw)
[^117]: [Google Jules、OpenAI Codex、Claude Code 功能深度評比](https://hackmd.io/@BASHCAT/SkNhLpNGee)
[^118]: [Anthropic也推出200美元月費的個人版Claude聊天機器人方案](https://www.ithome.com.tw/news/168375)
[^121]: [Google Gemini CLI 深度解析：瞄準開發者終端(terminal) 的AI ...](https://www.inside.com.tw/article/38924-google-gemini-cli)
[^124]: [Google 推出Gemini CLI：超高免費額度使用！佛系AI 編程的下 ...](https://hk.news.yahoo.com/google-%E6%8E%A8%E5%87%BA-gemini-cli%EF%BC%9A%E8%B6%85%E9%AB%98%E5%85%8D%E8%B2%BB%E9%A1%8D%E5%BA%A6%E4%BD%BF%E7%94%A8%EF%BC%81%E4%BD%9B%E7%B3%BB-ai-%E7%B7%A8%E7%A8%8B%E7%9A%84%E4%B8%8B%E4%B8%80%E6%AD%A5-103328851.html)
[^125]: [深夜突襲！OpenAI的AI程序員上線，人類僅需點按鈕](https://www.itiger.com/hant/news/1167495821)
[^127]: [Vibe Coding 神器Gemini CLI 釋出，Gemini 2.5 Pro 強大支援](https://technews.tw/2025/06/26/gemini-cli-brings-gemini-directly-into-developer-terminals/)
[^128]: [CodexCLI 更新: r/OpenAI - Reddit](https://www.reddit.com/r/OpenAI/comments/1lspbqx/codexcli_updates/?tl=zh-hant)
[^129]: [Pricing \ Anthropic](https://www.anthropic.com/pricing)
[^131]: [設定 Gemini Code Assist Standard 和 Enterprise  |  Gemini for Google Cloud](https://cloud.google.com/gemini/docs/discover/set-up-gemini?hl=zh-tw)
[^132]: [Gemini CLI：你的開源 AI 代理](https://blog.google/intl/zh-tw/products/cloud/gemini-cli-your-open-source-ai-agent/)
[^134]: [Pro 的訂閱者更別錯過50 美金Codex CLI 現在安裝起來就能領 ...](https://www.facebook.com/groups/gaitech/posts/1394215388429339/)
[^135]: [Pricing - Anthropic](https://www.anthropic.com/pricing)
[^138]: [Claude Code发布4个月，用户已经11.5万了，开发者 - 知乎专栏](https://zhuanlan.zhihu.com/p/1925858930858386901)
[^140]: [深夜突襲！OpenAI的AI程式設計師上線，人類僅需點按鈕 - 鉅亨號](https://hao.cnyes.com/post/170427)
[^146]: [Codex CLI只需要API Key, Codex in ChatGPT才需要PRO](https://www.threads.com/@pahud/post/DJwGvudP_6L/%E6%84%9F%E8%AC%9D%E6%8C%87%E6%AD%A3codex-cli%E5%8F%AA%E9%9C%80%E8%A6%81api-key-codex-in-chatgpt%E6%89%8D%E9%9C%80%E8%A6%81prohttpswwwperplexityaisearche10d33b7)
[^147]: [Claude code 优惠使用方式（国内可用） - 知乎专栏](https://zhuanlan.zhihu.com/p/1929241978756903207)
[^148]: [How to sign up for Paid Gemini CLI ( without API level ... - Reddit](https://www.reddit.com/r/GeminiAI/comments/1lr2ka8/how_to_sign_up_for_paid_gemini_cli_without_api/)
[^153]: [Claude Pro 的費用是多少？ | Anthropic Help Center](https://support.anthropic.com/zh-TW/articles/8325610-claude-pro-%E7%9A%84%E8%B2%BB%E7%94%A8%E6%98%AF%E5%A4%9A%E5%B0%91)
[^154]: [[Gemini Code Assist & Gemini CLI]GoogleのAI開発支援ツール ...](https://qiita.com/shioccii/items/99b0caf0fc6fc5ef944e)
[^156]: [Claude Code 這100 USD 花的真值得！（誠心推薦） - Facebook](https://www.facebook.com/eddiekao/posts/claude-code-%E9%80%99-100-usd-%E8%8A%B1%E7%9A%84%E7%9C%9F%E5%80%BC%E5%BE%97%E8%AA%A0%E5%BF%83%E6%8E%A8%E8%96%A6/10159651355632537/)
[^159]: [這已經超過我每個月付Claude Pro 的價格了，得到的價值比我 ...](https://www.threads.com/@fetalkpodcast/post/DLDy71yJiq8/%E6%A0%B9%E6%93%9A%E7%9B%AE%E5%89%8D-claude-code-%E4%BD%BF%E7%94%A8%E7%9A%84%E6%96%B9%E5%BC%8F%E9%80%99%E5%B7%B2%E7%B6%93%E8%B6%85%E9%81%8E%E6%88%91%E6%AF%8F%E5%80%8B%E6%9C%88%E4%BB%98-claude-pro-%E7%9A%84%E5%83%B9%E6%A0%BC%E4%BA%86%E5%BE%97%E5%88%B0%E7%9A%84%E5%83%B9%E5%80%BC%E6%AF%94%E6%88%91%E4%BB%98%E5%87%BA%E7%9A%84%E5%A4%9A%E4%B8%8D%E9%A6%99%E5%97%8E-%E5%B7%B2%E7%B6%93%E6%8A%8A-zed-pro-%E9%80%80%E8%A8%82%E4%BA%86)
