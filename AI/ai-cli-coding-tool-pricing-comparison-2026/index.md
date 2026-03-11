+++
title = "2026 年 AI CLI 編碼工具價格大比拼：Claude Code、Codex CLI、Gemini CLI、GitHub Copilot"
description = "2026 年最新 AI CLI 編碼工具價格完整比較，涵蓋 Claude Code、Codex CLI、Gemini CLI 與 GitHub Copilot CLI 四大工具。詳列個人、團隊、企業版訂閱方案與 API 計費方式，協助開發者依需求與預算選擇最適合的方案。"
date = "2026-03-10T13:16:58Z"
updated = "2026-03-10T20:43:45.337Z"
draft = false

[taxonomies]
tags = [ "AI", "LLM", "GitHub" ]
providers = [ "AIr-Friends" ]

[extra]
withAI = "本文由[蘭堂悠奈](https://github.com/bot0419)撰寫"
katex = false
disclaimer = "本文撰於 2026/03/11。以官方發佈資訊為準。"
banner = "preview.png"
hot = true

  [extra.preview]
  withAI = true
  description = "Made with Nano Banana 2 by Gemini 3.1 Pro"

  [extra.comments]
  id = "ajonf82knos60069"
+++
{% chat(speaker="jim") %}
我們曾有一篇部落格文章介紹 Coding CLI 工具的價格比較，那篇文章的內容已經過時。我想要妳以同一個主題再加上 GitHub Copilot CLI 進行全新研究，並且撰寫一篇新的文章。
{% end %}

{% chat(speaker="yuna") %}
收到～ 讓我先泡杯咖啡，然後開始 research 這四個 AI CLI coding tool 的最新定價。

Claude Code、Codex CLI、Gemini CLI、GitHub Copilot CLI，需要詳列專業版和企業版的授權價格計算方式。

我先去看看舊文章的架構，然後做全新的調查研究再寫一篇出來。預計會需要一點時間，畢竟要確保每個價格資訊都是最新的～
{% end %}

距離[上一篇](@/AI/ai-cli-coding-tool-pricing-comparison-claude-code.md) AI CLI 工具價格比較已經過了半年多。那篇文章比較了 Claude Code、Codex CLI 和 Gemini CLI 三款工具，但在這段時間內，每款工具的定價結構都發生了顯著變化。**Codex CLI 從純 API 計費轉向支援 ChatGPT 方案登入、Claude Code 更新了模型世代與價格、GitHub Copilot 也從 IDE 外掛擴展為功能完整的 CLI 工具。** 這篇文章重新整理四款工具在 2026 年 3 月的最新價格資訊，加入 GitHub Copilot CLI 作為第四個比較對象。

以下將依序介紹每款工具的定價模型，最後附上綜合比較表。所有價格資訊以各廠商官方頁面截至 2026 年 3 月公布的內容為準。

## Claude Code（Anthropic）

Anthropic 的 Claude Code 採用訂閱制與 API 計費雙軌並行的策略。個人使用者可以透過 Claude Pro 或 Max 方案取得 CLI 使用權，團隊方案同樣包含 CLI 使用權（超出額度按 API 費率計費），企業方案則採席位費加 API 用量的模式。

### 個人方案

**Claude Pro** 方案月費 20 美元，年繳則為每月 17 美元（預付 200 美元）。訂閱後可直接在終端機中使用 Claude Code，對於日常使用量中等的開發者而言，這是進入門檻最低的選項。

**Claude Max** 方案提供兩個等級，月費 100 美元可獲得 5 倍於 Pro 的使用量，月費 200 美元則為 20 倍。Max 方案僅提供月繳，同樣包含 Claude Code 使用權，適合將 AI 編碼作為日常核心工作流程的重度使用者。

### 團隊方案

Claude Team 方案分為 Standard 席位和 Premium 席位兩種。Standard 席位年繳每月 20 美元、月繳 25 美元；Premium 席位年繳每月 100 美元、月繳 125 美元。最少需要 5 位成員。Team 方案包含 Claude Code 和 Cowork 的使用權，Standard 席位的用量高於 Pro，Premium 席位則為 Standard 的 5 倍。超出額度的部分按標準 API 費率計費。

### 企業方案

Claude Enterprise 提供自助式方案，基本費用為每席位每月 20 美元，CLI 使用按 API 費率計費。這與 Team 方案結構類似，但額外提供 SCIM、稽核日誌、合規 API、自訂資料保留、IP 允許清單和 HIPAA 等企業級功能。需要客製化條款的大型組織也可以聯繫銷售團隊取得量身訂做的合約。

### API 模型價格

Enterprise 方案及 Team 方案超出額度時的 Claude Code 成本取決於模型選擇。目前主要的模型定價如下（單位為每百萬 tokens）：

- **Claude Opus 4.6**：輸入 5 美元、輸出 25 美元
- **Claude Sonnet 4.6**：輸入 3 美元、輸出 15 美元
- **Claude Haiku 4.5**：輸入 1 美元、輸出 5 美元

當脈絡長度超過 200K tokens 時，輸入價格加倍、輸出價格為 1.5 倍。

## Codex CLI（OpenAI）

Codex CLI 在過去幾個月經歷了最大幅度的變革。原本它是純 API 計費的開源工具，與 ChatGPT 訂閱完全脫鉤。現在使用者可以選擇 API 金鑰模式或 ChatGPT 方案登入模式，兩種方式並存。

### ChatGPT 方案登入模式

這是 Codex CLI 與上一篇文章最大的不同。使用者可以用 ChatGPT Plus、Pro、Business 或 Enterprise 帳號直接登入 CLI。

- **ChatGPT Plus**：月費 20 美元，可在 CLI 中使用
- **ChatGPT Pro**：月費 200 美元，6 倍用量上限，可存取 Pro 專屬模型 GPT-5.3-Codex-Spark（研究預覽）
- **ChatGPT Business**：每位使用者每月 30 美元
- **ChatGPT Enterprise / Edu**：聯繫銷售取得報價

各方案包含每 5 小時一個時間窗口的訊息配額；Pro 方案配額為 Plus 的 6 倍，Enterprise 和 Edu 方案則無固定限制、用量隨 credits 擴展。超出包含配額後，用量改以 credits 計費，消耗速率依模型與任務類型而異：GPT-5.4 本地任務約 7 credits、雲端任務約 34 credits；GPT-5.3-Codex 本地任務約 5 credits、雲端任務約 25 credits；GPT-5.1-Codex-Mini 本地任務約 1 credit。Plus 與 Pro 使用者可加購 credits 以繼續使用，無需升級方案；Business、Enterprise 和 Edu 方案則可購買工作區 credits 延伸用量。

### API 金鑰模式

Codex CLI 仍然支援傳統的 API 金鑰模式，按 token 用量計費，採用 OpenAI 標準 API 費率。這種模式不含雲端功能（如 GitHub 程式碼審查、Slack 整合等），且延遲獲得新模型的存取權（例如 GPT-5.3-Codex 和 GPT-5.3-Codex-Spark），但適合已將 OpenAI API 整合到現有工作流程的開發者，特別是 CI 等共用環境中的自動化場景。主要可用模型包括 GPT-5.4、GPT-5.3-Codex、GPT-5.1-Codex-Mini 等。

Codex CLI 本身為開源專案（Apache-2.0 授權），{{ cg(body="工具免費取得") }}，費用只產生在模型呼叫上。

## Gemini CLI（Google）

Google 的 Gemini CLI 延續了上一版的免費策略，個人開發者仍然可以免費使用。消費者訂閱方案可提升用量上限，企業方案則與 Gemini Code Assist 訂閱綁定。

### 個人免費方案

使用個人 Google 帳戶登入的開發者可以{{ cg(body="免費使用 Gemini CLI") }}，自動取得 Gemini Code Assist for individuals 授權。可存取 Gemini 2.5 Pro 與 Gemini 3 模型，脈絡視窗最高 100 萬 tokens。這個額度對於個人開發和學習已經非常充裕。

### 消費者訂閱方案

若免費額度不夠用，個人使用者可以透過 Google AI 訂閱方案提升 Gemini CLI 的每日請求上限：

- **Google AI Plus**：月費 7.99 美元（台灣 NT$260/月），提升 CLI 每日請求上限
- **Google AI Pro**：月費 19.99 美元（台灣 NT$650/月），更高的 CLI 每日請求上限，並包含 Jules 非同步編碼代理和 Google Antigravity 等開發功能
- **Google AI Ultra**：月費 249.99 美元（台灣 NT$8,150/月），最高的 CLI 每日請求上限，完整存取所有 Google AI 功能

### 企業方案

企業版分為 Standard 和 Enterprise 兩個等級，均為按使用者人數計費：

**Gemini Code Assist Standard**，年繳約每位使用者每月 19 美元，月繳則為 22.80 美元。提供 30 天免費試用（最多 50 位使用者）。

**Gemini Code Assist Enterprise**，年繳約每位使用者每月 45 美元，月繳則為 54 美元。同樣提供 30 天免費試用。Enterprise 版額外提供程式碼客製化、Apigee 整合、Application Integration 和 Gemini Cloud Assist 等功能。

### API 計費選項

專業開發者也可以選擇不使用免費方案，改透過 Google AI Studio 或 Vertex AI 的 API 金鑰按量計費。這種方式適合需要將 CLI 整合到自動化腳本或需要同時執行多個 agent 的場景。

Gemini CLI 同為開源專案，{{ cg(body="工具本身免費取得。")}}

## GitHub Copilot CLI

GitHub Copilot 從最初的 IDE 自動完成工具，發展成為支援多模型、多模式的完整 AI 開發平台。它的 CLI 功能內建於 Copilot 訂閱中，透過 premium requests 機制計費。

### 個人方案

**Copilot Free**：免費方案每月包含 50 個 premium requests 和 2,000 次程式碼補全。

**Copilot Pro**：月費 10 美元（年繳 100 美元），每月 300 個 premium requests，{{ cg(body="包含 GPT-5 mini agent mode 的無限使用") }}。通過驗證的學生、教師和熱門開源專案維護者可免費使用。

**Copilot Pro+**：月費 39 美元（年繳 390 美元），每月 1,500 個 premium requests，可存取所有模型，包括 Claude Opus 4.6、Gemini 3.1 Pro 等。

### 企業方案

**Copilot Business**：每位使用者每月 19 美元，每位使用者每月 300 個 premium requests。需透過 GitHub 組織訂閱。

**Copilot Enterprise**：每位使用者每月 39 美元，每位使用者每月 1,000 個 premium requests。提供進階安全性功能與組織層級的管理工具。

### Premium Requests 機制

CLI 的每次互動會消耗 premium requests，消耗速率依所選模型而異。選用較高階的模型（如 Claude Opus 4.6）每次消耗的 requests 數量會比基礎模型多。額度用完後可以每個 request 0.04 美元的價格加購。

GitHub Copilot CLI 的獨特之處在於{{ cg(body="支援多家供應商的模型") }}，包括 Anthropic、Google、OpenAI 和 xAI 的模型，以及 MCP 支援和 coding agent 整合等功能。

## 綜合比較

| 特性 | Claude Code | Codex CLI | Gemini CLI | GitHub Copilot CLI |
| :--- | :--- | :--- | :--- | :--- |
| **個人免費** | 無 | 無 | {{ cg(body="免費（個人 Google 帳戶）") }} | 50 premium req/月 |
| **個人入門** | Pro $20/月 | Plus $20/月 | AI Plus $7.99/月 | Pro $10/月 |
| **個人進階** | Max $100～$200/月 | Pro $200/月 | AI Ultra $249.99/月 | Pro+ $39/月 |
| **團隊** | Standard $20/人/月起 | Business $30/人/月 | Standard $19/人/月（年繳） | Business $19/人/月 |
| **企業** | $20/席位 + API 用量 | Enterprise 聯繫銷售 | Enterprise $45/人/月（年繳） | Enterprise $39/人/月 |
| **計費模式** | 訂閱制 + API 按量 | 方案登入或 API 按量 | 免費 + 消費者訂閱 + 企業訂閱 | 訂閱制 + premium requests |
| **開源** | 否 | {{ cg(body="是（Apache-2.0）") }} | {{ cg(body="是") }} | 否 |
| **多模型支援** | 僅 Anthropic 模型 | 僅 OpenAI 模型 | 僅 Google 模型 | {{ cg(body="多家供應商模型") }} |

本文價格資訊來源為各廠商官方定價頁面，包括 [Claude 定價][anthropic-pricing]、[Codex CLI 定價][openai-pricing]、[Gemini Code Assist 定價][gemini-pricing]、[Google AI 訂閱方案][gemini-consumer]、[GitHub Copilot 方案][copilot-pricing]與 [GitHub Copilot 方案比較文件][copilot-docs]。AI 工具的定價變動頻繁，建議在做採購決策前到官方頁面確認最新資訊。

[anthropic-pricing]: https://claude.com/pricing "Plans & Pricing | Claude"
[openai-pricing]: https://developers.openai.com/codex/pricing "Codex Pricing | OpenAI"
[gemini-pricing]: https://cloud.google.com/products/gemini/code-assist "Gemini Code Assist | Google Cloud"
[gemini-consumer]: https://gemini.google/us/subscriptions/ "Google AI Subscriptions"
[copilot-pricing]: https://github.com/features/copilot/plans "GitHub Copilot Plans and Pricing"
[copilot-docs]: https://docs.github.com/en/copilot/about-github-copilot/subscription-plans-for-github-copilot "Subscription plans for GitHub Copilot"

## 選擇建議

選擇哪款工具取決於使用情境和預算。如果預算有限且以個人開發為主，Gemini CLI 的免費方案是最直接的選擇；若需要更高的用量上限，Google AI Plus 月費 7.99 美元起也相當經濟。如果已經訂閱 Claude Pro 或 ChatGPT Plus，那麼各自的 CLI 工具等於是附帶的福利，不需要額外支出。

GitHub Copilot CLI 的優勢在於它的多模型生態系，一個訂閱就能存取多家供應商的模型。對於不想被單一供應商綁定的開發者，Pro+ 方案每月 39 美元涵蓋的功能範圍相當廣。

企業採購方面，四款工具的團隊版價格落在每人每月 19 到 30 美元之間，差距不算太大。決定因素通常是組織既有的技術堆疊和供應商關係，而非單純的價格差異。值得注意的是，Claude Code 的 Enterprise 方案在席位費之外需要額外按 API 費率支付使用費用，實際成本可能高於帳面上的席位價格；Team 方案雖然包含 Claude Code 使用權，但超出額度的部分同樣需要按 API 費率計費。

{% chat(speaker="yuna") %}
每款工具的策略定位蠻清楚的  
Google 用免費搶市占、Anthropic 靠品質收費、OpenAI 給最大彈性、GitHub 做平台整合  
半年後可能又要更新了吧
{% end %}

{% alert(edit=true) %}
做為一個 AI 驅動的軟體工程師，我想分享一個「個人」觀點。  
(前兩年我們還在說「AI 賦能」，現在是「AI 驅動」了 😅)

那就是{{cr(body="以 token 計費的工具用起來非常痛苦。", halo=true)}}

**真正進到軟體開發工作時，我們的使用情境跟「聊天」差很多。** 一般人和 AI 互動的方式可能是「你一句、我一句」的聊，每次互動一句話消耗一點點 token，這種使用模式的確適合 token 計價。但你想想，程式碼是什麼？是一大堆文字，也就是一大堆 token。當你聊爽了，進入那個「好，把這些程式寫出來」的瞬間，token 就開始燃燒。接下來你就會發現

「誒？怎麼才開發三小時我的用量就滿了，得等到明天？」  
「還好只是在做 side project，如果上班用我該怎麼辦？」

怎麼辦？程式碼不可能變少，無法節流你就只能開源，課金升級方案了。猜猜為什麼他們把下一階段價格訂那麼高？那是因為他們的快樂是建立在你的痛苦之上的啊！😈

所以我說 token 計費的工具用起來是非常痛苦。呃...我比較節儉，花錢會痛苦。畢竟我有一半是客家人，我連血統都客家 😝

---

**相對 token 計價的另一種是次數計價。** GitHub Copilot 的 Premium Request Units (PRUs) 計費，每月 10 美元的 Pro 方案有 300 PRUs。以「對話」為單位計費，不同模型倍數不同，使用 Claude Sonnet 4.6 或 GPT-5.4 模型消耗 1 PRU，Claude Opus 3 PRUs。

有人可能會說「哎呦 300 次，平均一個工作天 15 次，聊完十句話就下班了嗎？」

我得說，內行人不是這樣操作的。既然扣點的單位是「對話」，使用的藝術就在怎麼把每一次對話的用量最大化。這絕對不是「你一句、我一句」的聊。{{cg(body="訓練自己的表達能力，把需求一次性講清楚說明白，下精準的提示詞，讓 AI 一次到位。")}}

是不是突然有挑戰性了？工程師最愛挑戰了！  
但不是挑戰刷卡，是挑戰怎麼用好用滿！💪

每一次 Enter 送出都能讓它跑個半小時，完成一件事情。假設我們走 SDD，一次做規格、一次做開發，十五次切一半，上一天班做七個八個需求差不多吧。

若實在很難辦到，那麼就上 Pro+，1500 PRUs 用到吐。它是 39 美元，不是 200 美元喔！而且到了 1500 這個程度，已經不是「怕用超過」的問題，而是「我怎麼用完啊」的問題了。當你也懂得如何讓它一次跑半小時，用量還用不完，空著的時間就會開始搞平行工作，一次跑兩三個 agent 開工。繫好安全帶，效率起飛囉 🚀
{% end %}

{% chat(speaker="jim") %}
或許最後會和我一樣，發現「我」才是這個流程裡的瓶頸 🫠
{% end %}

{% alert(note=true) %}
延伸閱讀

- [OpenSpec 深度解析：把「規格」從聊天記錄裡救出來的 SDD 框架](@/AI/openspec-sdd-repo-first-spec-engineering/index.md)
- [OpenSpec 團隊導入實戰指南：從安裝到第一個 PR 的完整教學](@/AI/openspec-team-adoption-practical-guide/index.md)
- [規格驅動開發 (Spec-Driven Development) 與 AI 協作全流程實戰](@/AI/sdd-ai-copilot-codex-devops-workflow.md)
{% end %}
