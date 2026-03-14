+++
title = "規格驅動開發 (Spec-Driven Development) 與 AI 協作全流程實戰"
description = "深入解析如何結合 GitHub Copilot、Codex CLI 與 DevOps 平台，實現 Spec-Driven Development 的現代化工作流程。帶你掌握從規格撰寫、任務拆解到 AI 自動化實作與審查的完整協作模式。探索人機分工下的軟體開發新典範！"
date = "2025-09-17T14:28:40Z"
updated = "2025-09-17T18:27:13.625Z"
draft = false

[taxonomies]
tags = [ "DevOps", "Git" ]
providers = [ "Felo Search" ]

[extra]
withAI = "<https://felo.ai/search/a7Q4bgctHZHGEGm5APQ8vj?invite=dOLYGeJyZJqVX>"
featured = true
hot = true
+++
{% alert(edit=true) %}
這份做來送給我的同事參考，希望他們都能把這套用起來！😀
{% end %}

{% chat(speaker="jim") %}
請你產出一份指南，教導傳統的資深軟體工程師如何進行 Spec-Driven Development 規格驅動開發。請深入解釋 SDD，並且在傳統程式開發、Vibe Coding、SDD 三種開發方式之間進行比較 the pros and cons

Warn: 我想要的不是 github/spec-kit 的工具使用教學、不是 GitHub Copilot Coding Agent、也不是特定工具的使用方式，而是高階視角的概念、心法、工作流程。
{% end %}

{% chat(speaker="felo") %}

本指南旨在協助傳統資深軟體工程師理解並轉向**規格驅動開發（Spec-Driven Development, SDD）。** 在 AI 輔助程式設計時代，開發範式正從傳統的手工編碼，以及新興但混亂的「Vibe Coding」，演進到一個更具結構化、可維護性的新階段。SDD 並非要拋棄數十年來積累的軟體工程原則，而是將這些嚴謹的實踐（如需求分析、系統設計）與 AI 的強大生產力相結合，形成一種{{ color(body="「意圖即為真理來源 (Intent is the Source of Truth)」", color="blue", halo=true) }}的全新工作哲學。
{% end %}

**其核心是將開發流程的重心從「撰寫程式碼」轉移到「定義規格」。** 透過一個結構化的流程，將模糊的業務需求層層轉化為清晰的規格文件、技術藍圖與可執行的任務清單，再由 AI 代理執行具體的編碼工作。對資深工程師而言，這意味著角色的轉變：從主要的程式碼生產者，轉變為系統意圖的架構師、品質的把關者與複雜問題的解決者。本報告將深入剖析 SDD 的核心理念與工作流程，並透過與傳統開發及 Vibe Coding 的對比，闡明其在現代軟體工程中的獨特價值與實踐心法。

### 開發範式的演進：從傳統到 AI 世代

在深入 SDD 之前，我們必須先理解當前軟體開發領域並存的三種主要模式：傳統開發、Vibe Coding，以及作為兩者演化方向的 SDD。

#### 傳統開發模式

傳統開發，無論是瀑布式（Waterfall）或敏捷式（Agile），都建立在嚴謹的流程之上[^3][^53]。工程師遵循明確的步驟，從需求分析、設計、編碼到測試，一步步建構軟體[^105]。

* **優點**：
  * **{{ cg(body="高度可控")}}**：流程嚴謹，產出品質與架構有較高保障[^21][^33][^77]。
  * **{{ cg(body="適合複雜系統")}}**：對於需要高度客製化、高安全性或需長期維護的核心系統，傳統開發提供了必要的穩定性與延展性[^9][^65][^71]。
* **缺點**：
  * **{{ cr(body="開發週期長")}}**：從需求變更到功能上線，可能需要數月甚至數年，難以應對快速變化的市場需求[^3][^53]。
  * **{{ cr(body="文件與程式碼脫節")}}**：規格文件一旦撰寫完成，往往因後續的程式碼頻繁迭代而迅速過時，成為無人敢信的「殭屍文件」，增加了維護與知識傳承的難度[^7][^57][^125]。

#### Vibe Coding：跟著感覺走的 AI 程式設計

隨著大型語言模型（LLM）的興起，一種全新的開發模式應運而生。由 OpenAI 共同創辦人 Andrej Karpathy 於 2025 年 2 月提出的「Vibe Coding」，指的是開發者主要透過自然語言提示（Prompt），引導 AI 快速生成程式碼，專注於「感覺」與「意圖」，而非程式碼的語法細節[^2][^4][^10][^104]。

* **優點**：
  * **{{ cg(body="極致的速度")}}**：能夠在數小時內完成一個基本版本的產品原型（MVP），極大地縮短了從想法到驗證的週期[^56][^86][^104]。根據 GitHub Copilot 的數據，開發者平均可提升 55% 的編碼速度[^16]。
  * **{{ cg(body="降低技術門檻")}}**：非工程背景的產品經理、設計師甚至創業者，也能透過自然語言將創意轉化為實際產品[^6][^30][^40]。
* **缺點**：
  * **{{ cr(body="品質與技術債")}}**：AI 生成的程式碼品質參差不齊，往往缺乏良好架構，容易引入安全漏洞與效能問題[^14][^18][^36]。一份報告指出，70% 使用 AI 產生程式碼的公司在部署後六個月內報告了嚴重的技術債問題[^2]。
  * **{{ cr(body="除錯與維護困難")}}**：由於程式碼並非由開發者親手撰寫，一旦出現問題，除錯過程將變得極其困難，長期維護成本高昂[^14][^104]。
  * **{{ cr(body="信任度低")}}**：僅有 43% 的開發人員對 AI 輸出的結果信任度較高，這顯示了業界對其可靠性的普遍疑慮[^2]。

{{ color(body="Vibe Coding 的出現揭示了一個核心矛盾：我們獲得了前所未有的開發速度，卻犧牲了軟體工程最重視的品質、可維護性和安全性。這正是 Spec-Driven Development 試圖解決的根本問題。", color="blue", halo=true) }}

### 深入解析：規格驅動開發 (Spec-Driven Development, SDD)

SDD 並非一個全新的發明，而是將傳統軟體工程中「設計先行」的嚴謹思維，與 AI 的自動化能力相結合的現代實踐。它不是要你寫更多文件，而是要讓「規格」本身成為可執行、可驗證、並能驅動開發流程的「活資產」[^51][^125]。

#### 核心哲學：意圖即真理 (Intent is the Source of Truth)

傳統開發的真理來源是「程式碼」，而 Vibe Coding 則幾乎沒有真理來源。SDD 的核心轉變是將「規格」或更精確地說是「開發意圖」，確立為唯一的真理來源[^11][^14][^29][^125]。

這意味著，任何程式碼的產生、修改或測試，都必須源自於一份清晰、明確的規格。這份規格不再是開發完成後束之高閣的靜態文件，而是整個開發生命週期的起點與核心[^7][^57]。當需求變更時，我們修改的不是程式碼，而是規格；規格的更新會自動觸發後續技術設計、任務清單乃至程式碼的連動變更[^19][^69]。

#### SDD 通用工作流程

雖然不同工具有其特定實現，但一個典型的、工具無關的 SDD 流程包含以下幾個關鍵階段，每一階段都產出對應的 Markdown 文件作為交付物[^1][^103][^106]：

1. **第一階段：規格定義 (Specify - The "What" & "Why")**
    這個階段的目標是回答「我們要做什麼？」以及「為什麼要做？」，完全不涉及技術實現[^111][^125]。資深工程師需要與產品團隊協作，將模糊的商業需求轉化為結構化的規格文件（相當於 `requirements.md`）。
    * **產出**：一份包含使用者故事（User Stories）和驗收標準（Acceptance Criteria）的清晰文件。
    * **心法**：採用標準化語法來消除歧義。例如，EARS (Easy Approach to Requirements Syntax) 語法，透過「WHEN-THEN-SHALL」的格式，將每個需求都轉化為一個可測試的場景[^1][^25][^103]。
    * **範例**：

        ```
        # 需求：增加亮色模式主題
        
        ## 使用者故事
        AS a user,
        I WANT to be able to switch between light and dark themes,
        SO THAT I can choose the most comfortable viewing experience.
        
        ## 驗收標準 (EARS 格式)
        WHEN a user visits the site for the first time,
        THEN the system SHALL display the page in the default dark theme.
        
        WHEN a user activates the theme toggle control,
        THEN the system SHALL switch the site's appearance to the light theme.
        ```

2. **第二階段：技術規劃 (Plan - The "How")**
    當「做什麼」被明確定義後，才進入「如何做」的階段。此時，AI 代理會基於第一階段的規格文件，以及預先設定好的專案技術限制（如技術棧、架構模式、命名慣例等），生成一份詳細的技術藍圖（相當於 `design.md`）[^1][^103]。
    * **產出**：包含技術架構決策、資料流程圖（如 Mermaid 圖）、API 端點定義、資料庫 Schema 變更、前端元件劃分等內容的設計文件[^1]。
    * **心法**：這是資深工程師發揮最大價值的環節。你需要審核 AI 產生的設計方案，確保其符合專案的長期架構、具備良好的延展性與效能。你的角色是「把關者」，而不是「執行者」。

3. **第三階段：任務拆解 (Tasks - The Work Breakdown)**
    一旦技術藍圖被確認，AI 會自動將其分解為一系列具體的、有依賴關係的、可獨立執行的開發任務（相當於 `tasks.md`）[^1][^103]。
    * **產出**：一份精細的任務清單，每個任務都直接關聯到設計文件和原始需求，建立了清晰的可追溯鏈[^1]。
    * **範例**：

        ```
        # 任務清單
        
        - [ ] T01: [DB] 在 `users` 資料表中新增 `theme_preference` 欄位。
        - [ ] T02: [API] 建立 `PATCH /api/user/preferences` 端點以更新主題偏好。
        - [ ] T03: [Frontend] 建立 `ThemeToggle` React 元件。 (依賴 T02)
        - [ ] T04: [Test] 為 `ThemeToggle` 元件撰寫單元測試。 (依賴 T03)
        ```

    * **心法**：確保每個任務的粒度足夠小，可以在 1-2 小時內完成，這有利於 AI 自動執行和人類進行審查[^127]。

4. **第四階段：實作與驗證 (Implement)**
    AI 代理或開發者依照任務清單逐一完成編碼工作。與 Vibe Coding 的巨大程式碼傾倒不同，SDD 模式下的程式碼提交是小而聚焦的，每一次變更都直接對應一個具體任務[^125]。
    * **心法**：程式碼審查（Code Review）的重點從「這段程式碼寫得好不好？」轉變為「這段程式碼是否忠實地實現了 T03 任務所描述的規格？」。審查變得有據可依，效率和品質都大幅提升。

#### 資深工程師在 SDD 中的新角色

{{ color(body="SDD 不會取代資深工程師，反而會放大你的價值。", color="blue", halo=true)}}你的工作將從繁瑣的編碼中解放出來，更專注於以下高價值活動：

* **意圖的架構師**：你的核心職責是撰寫高品質的規格，清晰地表達業務意圖。
* **系統的治理者**：定義專案的技術邊界與品質護欄，確保 AI 的產出符合團隊規範[^103][^127]。
* **AI 的導師**：將 AI 視為一個能力極強但缺乏經驗的初級工程師，為其提供清晰的指令、上下文和指導[^17][^61]。
* **複雜問題的攻堅者**：專注於處理 AI 難以勝任的複雜架構設計、演算法優化和疑難問題排查[^5][^72]。

### 三種開發模式的比較分析

為了更直觀地理解 SDD 的定位，下表從多個維度對三種開發模式進行了比較：

| 評估指標 | 傳統開發 (Traditional Development) | Vibe Coding | 規格驅動開發 (Spec-Driven Development) |
| :--- | :--- | :--- | :--- |
| **核心理念** | 流程與紀律 | 速度與直覺 | 意圖與結構 |
| **開發速度** | {{ cr(body="慢") }} | {{ cg(body="極快 (僅限原型)") }} | 快 (功能開發) |
| **程式碼品質** | {{ cg(body="高") }} | {{ cr(body="低") }} | {{ cg(body="高") }} |
| **可維護性** | 中（文件易過時） | {{ cr(body="極低") }} | {{ cg(body="高（活文件）") }} |
| **技術債** | 中 | {{ cr(body="極高") }} | {{ cg(body="低") }} |
| **文件** | 靜態，易與程式碼脫節 | {{ cr(body="幾乎沒有") }} | {{ cg(body="動態，與程式碼同步（活文件）") }} |
| **開發者角色** | 實作者、工程師 | 提示者、實驗者 | 架構師、審查者、指揮者 |
| **適用場景** | 高度監管的行業、大型核心系統、遺留系統維護[^9][^71] | 快速原型驗證 (MVP)、個人專案、創意探索[^56][^86][^104] | 複雜功能開發、企業級應用、注重長期維護的新專案[^8][^25] |

從比較中可以看出，SDD 巧妙地在 Vibe Coding 的「速度」與傳統開發的「品質」之間取得了平衡[^17][^79]。它吸收了 Vibe Coding 由自然語言驅動的優勢，同時透過引入結構化的軟體工程實踐，克服了其混亂和不可靠的致命缺陷。

### 擁抱 SDD 的心法：給資深工程師的轉型指南

要成功轉型到 SDD，不僅是學習新工具，更是一場思維模式的變革。

1. **從「如何做」轉向「為何做」**
    在思考任何技術實現之前，先強迫自己清晰地闡述功能的商業價值、使用者場景和成功指標。練習撰寫高品质的 `requirements.md` 是第一步，也是最重要的一步[^111][^125]。

2. **將 AI 視為你的「初級開發夥伴」**
    不要期待 AI 能讀懂你的心思。你需要像指導一位新進成員一樣，為它提供清晰的規格、完整的上下文（現有程式碼、架構約束）和明確的目標。你的經驗將體現在你提供給 AI 的「指導品質」上[^17][^61]。

3. **擁抱「活文件」生態系統**
    徹底拋棄「先寫程式碼，後補文件」的舊習慣。在 SDD 中，文件是驅動程式碼的引擎。要習慣於在 `design.md` 中規劃架構，在 `tasks.md` 中追蹤進度。這些文件是專案知識傳承的核心，其價值甚至高於程式碼本身[^51][^125]。

4. **從「編碼者」轉型為「指揮家」**
    你的價值不再由你寫了多少行程式碼來衡量，而是由你設計的系統有多麼健壯、可維護來決定。將你的時間和精力投入到更高層次的架構決策、設計方案審查和定義品質標準上[^5][^17]。

5. **採取漸進式導入策略**
    不要試圖一夜之間改變整個團隊的開發流程。從一個新的、相對獨立的功能模組開始試驗。為這個模組完整地走一遍 SDD 流程，並與團隊過去的開發方式進行對比。用實際成果（更高的品質、更少的返工、更清晰的文件）來證明 SDD 的價值，是推動變革最有效的方式[^2][^8]。

總結而言，Spec-Driven Development 是 AI 時代下軟體工程嚴謹性的回歸與升級。對於資深工程師來說，這不是威脅，而是一個契機——一個將你從重複的編碼勞動中解放出來，讓你真正專注於架構、設計與品質，從而最大化自身經驗價值的歷史性機遇。

{% chat(speaker="jim") %}
請以此流程為例進行完整的講解: 以 GitHub Copilot in VSCode 製作第一、二、三階段的 Spec docs 並加入 Git 版控中，以 GitHub Copilot in VSCode 規劃 tasks 並開為 GitHub Issue，此 Issue 會包含完整的開發 context 和範例程式邏輯。接下來工程師在本地驅動 Codex-CLI 讀取 Issue 內容進行實作且自動推送 PR，在 PR body 內撰寫實作報告，最終由工程師檢視合併，或者 request changes 後驅動新的 Codex-CLI 修正 PR。

Note: 搭配 GitHub MCP 能允許讓 AI 操作 GitHub，將 DevOps 操作完全自動化。DevOps 平台非常適合揉合 SDD 使用。不要提供 pseudocode 、程式碼、指令，而是以自然語言回覆我。  
Warn: 本指南不應涉及 GitHub Copilot Coding Agent，這是另一個完全不相關的產品。
{% end %}

{% chat(speaker="felo") %}

本報告深入剖析一個整合 GitHub Copilot 與 Codex CLI 的現代化規格驅動開發（Spec-Driven Development, SDD）工作流程。此流程展示了一種人機協作的混合模型，將開發過程精確地劃分為「人類指導下的 AI 規劃」與「AI 自主執行」兩個階段。
{% end %}

開發者首先在 VS Code 環境中，利用 GitHub Copilot 的上下文理解與程式碼生成能力，完成從需求定義、技術設計到任務拆解的規格文件（`requirements.md`, `design.md`, `tasks.md`），並將其轉化為一個包含完整開發脈絡的 GitHub Issue。接著，開發流程的重心轉移至本地執行的 Codex CLI，此 AI 代理（Agent）被賦予讀取該 GitHub Issue 的能力，自主完成程式碼實作、建立 Pull Request，並在 PR 內生成詳盡的實作報告。最後，人類工程師的角色昇華為最終的品質把關者，負責審查、批准合併，或提出修改建議以驅動 AI 進行迭代修正。此工作流程不僅體現了 SDD 的核心精神——「意圖即為真理來源」，更透過 GitHub 平台與模型上下文協定（Model Context Protocol, MCP）將開發與維運（DevOps）無縫整合，實現了從規格到交付的高度自動化與可追溯性，為複雜軟體專案提供了一個兼具速度、品質與治理的先進開發範式。

{% alert(edit=true) %}
本章使用到以下工具：

* [GitHub Copilot Chat in VS Code](https://code.visualstudio.com/docs/copilot/overview)
* [OpenAI Codex CLI](https://github.com/openai/codex)
* [GitHub MCP Server](https://github.com/github/github-mcp-server)
* [GitHub CLI](https://cli.github.com/)
{% end %}

{% alert(edit=true) %}
我選中了 GitHub Copilot Chat + Codex CLI 的組合，是因為這是一套可以選擇串接本地或雲端 LLM 的工具組合。且它們在本文提到的範圍中較不具有獨特性，使得這個工作流程可以套用在其它類似的工具組合上。
{% end %}

### 階段一：運用 GitHub Copilot 進行規格定義與任務規劃

此工作流程的起點是開發者的整合式開發環境（IDE），在此階段，人類的意圖與 AI 的輔助能力緊密結合，共同產出驅動後續開發的核心規格。

#### 規格文件撰寫與版控

開發者在 Visual Studio Code 中，開啟專案的工作區（Workspace），並啟動 GitHub Copilot Chat[^1093]。此階段的目標是產出 SDD 的前三份核心文件：

1. **需求規格 (`requirements.md`)**：開發者透過與 Copilot 的對話，將模糊的產品概念轉化為結構化的使用者故事與驗收標準。開發者可以提供初步想法，並要求 Copilot 依據標準格式（如 EARS 語法）進行精煉。
2. **技術設計 (`design.md`)**：基於已確定的需求，開發者會要求 Copilot 規劃技術實現方案。透過提供專案的上下文（例如使用 `#filename` 標籤或 `@workspace` 引用關鍵的現有程式碼檔案），Copilot 能夠提出更貼近專案架構的建議，例如 API 端點設計、資料庫結構變更、前端元件劃分等[^1055][^1106]。開發者的主要職責是審核與修正 AI 產生的設計，確保其符合長期的技術願景與品質標準。
3. **任務拆解 (`tasks.md`)**：技術設計一經確認，開發者便會指示 Copilot 將設計藍圖分解為一系列具體、可執行的開發任務清單。

這個過程充分利用了 Copilot 作為「AI 配對程式設計師」的優勢，它擅長理解上下文、生成樣板程式碼以及輔助撰寫文件[^1063][^1084][^1127]。完成後，這三份 Markdown 文件會被提交至 Git 儲存庫，成為整個功能開發週期的「單一真理來源」，確保所有後續工作都有據可依。

{% alert(edit=true) %}
這裡不需要是「三份」文件，我更推薦撰寫詳細完整的 wiki，同時供 AI 和人類開發夥伴參考。並且要確實的進版控，和程式碼放在一起，確保開發時它們能夠隨時被 AI 查閱，或者方便帶入脈絡中。
{% end %}

#### 建立可執行的 GitHub Issue

規格定義完成後，下一步是將其轉化為一個可供 AI 代理執行的正式任務。開發者繼續在 VS Code 中使用 GitHub Copilot Chat，執行以下操作：

* **生成 Issue 內容**：開發者指示 Copilot 根據 `tasks.md` 的內容，草擬一份新的 GitHub Issue[^1050][^1119]。
* **注入完整上下文**：這份 Issue 的描述（Body）不僅僅是任務列表的簡單複製。開發者會引導 Copilot 將 `requirements.md` 的核心目標、`design.md` 的架構決策、相關的程式碼片段範例，以及必要的環境設定等資訊，全部整合進 Issue 的描述中[^1098][^1107]。這一步驟至關重要，它將一個人類可讀的任務，轉化為一個機器（AI 代理）可讀、上下文豐富的「任務包」。
* **透過 MCP 建立 Issue**：利用 GitHub 的模型上下文協定（Model Context Protocol, MCP），Copilot Chat 能夠直接在 VS Code 內呼叫 GitHub API，將草擬的內容建立成一個真正的 GitHub Issue，並為其設定標籤、指派人員等[^1068]。這實現了從 IDE 到 DevOps 平台的無縫銜接。

最終產出的 GitHub Issue，成為連接「規劃階段」與「執行階段」的關鍵橋樑。

{% alert(edit=true) %}
我有一份專門用來產出高品質 GitHub Issue 的 GitHub Copilot prompt，在此提供給讀者參考

[create-plan.prompt.md - jim60105/copilot-prompt](https://github.com/jim60105/copilot-prompt/blob/master/.github/prompts/create-plan.prompt.md)
{% end %}

### 階段二：Codex CLI 的自主實作與迭代

當 GitHub Issue 建立完成後，開發流程進入下一個核心階段：由本地 AI 代理 Codex CLI 接手執行。人類的角色從「指導者」轉變為「監督者」。

#### 讀取任務並自主開發

開發者在自己的本地終端機中，於已複製（Clone）的專案目錄下啟動 Codex CLI[^1003][^1111]。

* **任務啟動**：開發者向 Codex CLI 下達一個初始指令，要求它讀取並執行前一階段建立的 GitHub Issue。~~雖然直接從 URL 讀取 Issue 內容的功能有時不穩定[^1017][^1114]，但更可靠的做法是將 Issue 的完整內容複製貼上至終端機，或將其儲存為本地檔案後提供給 Codex CLI。~~
  {% alert(edit=true) %}
  不，透過 GitHub MCP 或是 GitHub CLI 讀取 Issue 內容的表現非常穩定，請直接這麼做。
  {% end %}
* **自主執行**：Codex CLI 作為一個本地運行的代理，被授權讀取本地檔案系統[^1003][^1007][^1022]。它會解析 Issue 中的所有上下文，包括需求、設計、任務清單和程式碼範例。接著，它會以 `auto-edit` 或 `full-auto` 模式開始工作，自主地修改程式碼、建立新檔案、安裝相依套件，並執行測試指令[^1012][^1015][^1025]。所有操作都在一個安全的沙箱環境中進行，以防止意外的系統級變更[^1012][^1015]。

#### 自動化 PR 建立與報告生成

當 Codex CLI 判斷任務完成後（例如，所有測試都已通過），它會自動執行以下 Git 操作：

1. **提交變更**：將所有修改過的檔案執行 `git commit`[^1005]。
2. **建立 PR**：自動將本地分支推送到遠端，並建立一個指向主分支的 Pull Request[^1009]。
3. **生成實作報告**：這是此流程的亮點之一。Codex CLI 會在 PR 的描述（Body）中，自動生成一份詳細的報告[^1006]。這份報告通常包含：
    * 任務目標摘要。
    * 具體的變更說明。
    * 執行的關鍵指令與其輸出日誌。
    * 引用其修改或參考過的檔案路徑與行號，提供完整的可驗證性與可追溯性[^1005]。

{% alert(edit=true) %}
這是用來實作 Issue 並提交 PR 的 prompt

[implement-plan.prompt.md - jim60105/copilot-prompt](https://github.com/jim60105/copilot-prompt/blob/master/.github/prompts/implement-plan.prompt.md)
{% end %}

### 階段三：人類審查與修正循環

AI 完成了初步的實作與報告後，控制權再次交還給人類工程師，進行最終的品質驗證。

#### 審查與合併

工程師打開該 PR，此時的審查重點不再是逐行檢查語法細節，而是更高層次的驗證：

* **意圖符合性**：PR 的實現是否完全符合原始 GitHub Issue 中定義的需求與設計？
* **邏輯正確性**：程式碼的邏輯是否健全？邊界條件是否處理得當？
* **架構一致性**：變更是否遵循了專案既有的架構模式與編碼規範？

如果 PR 品質達標，工程師便可直接批准並合併，完成整個開發循環。

#### 請求變更與 AI 迭代

如果審查發現問題，工程師無需親自修改程式碼。他們只需在 GitHub PR 的審查評論（Review Comments）中，用自然語言清晰地寫下需要修正的地方。

接著，開發者會啟動一個新的 Codex CLI 實例，並將這些審查評論作為新的指令輸入。AI 代理會讀取這些回饋，理解需要進行的修改，然後在同一個分支上提交新的修補程式碼，自動更新該 PR[^1001][^1019]。這個「審查 -> 回饋 -> AI 修正 -> 再次審查」的循環可以重複進行，直到 PR 達到可合併的品質標準為止。

### 協作模型分析

這個工作流程清晰地劃分了人類與不同 AI 工具的職責，形成一個高效的協作體系。

| 參與者 | 核心角色 | 主要任務 | 關鍵能力/工具 |
| :--- | :--- | :--- | :--- |
| **人類工程師** | 意圖定義者、品質把關者、最終決策者 | 提出初始需求、審核 AI 設計、建立 Issue、審查 PR、提出修改建議、合併程式碼 | 專業領域知識、架構判斷力、批判性思維 |
| **GitHub Copilot** | 規劃助理、上下文整合者 | 輔助撰寫規格文件、將設計轉化為任務、整合上下文並建立 GitHub Issue | IDE 整合、上下文理解 (`@workspace`)、程式碼生成、文件撰寫、MCP 協定[^1048][^1065][^1068] |
| **Codex CLI** | 自主執行者、實作代理 | 讀取 Issue、修改本地程式碼、執行測試、提交變更、建立 PR、生成實作報告、根據回饋修正 PR | 本地檔案存取、命令列執行、沙箱環境、Git 自動化、報告生成[^1003][^1007][^1022][^1111] |

#### 優勢與挑戰

* **優勢**：
  * **{{cg(body="職責分離")}}**：明確區分了 Copilot 在 IDE 中的「規劃輔助」角色和 Codex CLI 在終端機的「自主執行」角色，避免了單一工具的權責不清。
  * **{{cg(body="高度可追溯")}}**：從規格文件到 GitHub Issue，再到 PR 的每一次提交，都建立了清晰的連結，極大地方便了後續的維護與審計。
  * **{{cg(body="提升開發者價值")}}**：將開發者從繁瑣的編碼工作中解放出來，使其能更專注於架構設計、需求分析和品質控制等高價值活動。
* **挑戰**：
  * **{{cr(body="上下文傳遞的保真度")}}**：流程的成敗高度依賴於 GitHub Issue 中上下文的品質。如果 Issue 描述不清或有歧義，Codex CLI 的執行結果可能偏離預期[^1028][^1123]。
  * **{{cr(body="工具鏈的穩定性")}}**：此流程依賴多個工具的協同工作，任何一個環節（如 API 連線、工具版本相容性）出現問題都可能中斷流程[^1026][^1129]。
  * **{{cr(body="對 AI 的過度信任")}}**：即使 AI 能夠生成報告並通過測試，其程式碼仍可能包含隱蔽的邏輯錯誤或安全漏洞，人類的最終審查依然不可或缺。

### 結論與未來展望

本報告所闡述的整合工作流程，不僅是 SDD 理論的一個具體實踐，更代表了 AI 驅動軟體開發走向成熟的一個重要里程碑。它成功地將 GitHub Copilot 的互動式輔助能力與 Codex CLI 的自主代理能力結合，在 DevOps 平台上形成了一個從意圖到交付的閉環自動化系統。

展望未來，隨著 AI 代理能力的進一步增強，特別是像 [GPT-5-Codex](https://openai.com/index/introducing-upgrades-to-codex/) 這樣專為程式碼任務優化的模型的出現，我們可以預見此工作流程將會更加流暢與整合[^1004][^1029][^1112]。目前在 Copilot 和 Codex CLI 之間清晰的職責劃分可能會逐漸模糊，演化為一個更統一、更強大的「開發超級代理」。這個代理或許能夠在接收到一個高層次的 GitHub Issue 後，自主完成從規劃、設計、編碼、測試、報告到修正的全過程，而人類開發者的角色將徹底轉變為專案的「首席架構師」與「產品策略師」，專注於定義「做什麼」與「為何做」，而將「如何做」的絕大部分工作安心地委託給 AI。

[^1]: [重塑软件工程的未来：规范驱动的AI 编程代理 - Linguista](https://linguista.bearblog.dev/spec-driven-future-ai-agents-software-engineering/)
[^2]: [從Vibe Coding 到Spec 驅動開發一、背景](https://www.facebook.com/DavidLearningJourney/posts/-%E5%BE%9E-vibe-coding-%E5%88%B0-spec-%E9%A9%85%E5%8B%95%E9%96%8B%E7%99%BC%E4%B8%80%E8%83%8C%E6%99%AFvibe-coding-%E5%9C%A8%E5%B9%B4%E5%88%9D%E6%89%8D%E9%96%8B%E5%A7%8B%E6%B5%81%E8%A1%8C-vibe-coding-%E6%98%AF%E7%94%B1-andrej-karpathy/1310361217764340/)
[^3]: [傳統應用程式vs. 現代應用程式：4 大關鍵差異 - Pure Storage](https://www.purestorage.com/tw/knowledge/legacy-apps-vs-modern-apps.html)
[^4]: [什麼是Vibe Coding？跟一般人(非工程師)有什麼關係嗎？](https://airabbi.com/blog/what-is-vibe-coding/)
[^5]: [香港IT 人](https://www.facebook.com/groups/1618281709096037/posts/1922949428629262/)
[^6]: [Vibe Coding是什麼？優缺點深度解析：AI開發革命還是暗藏 ...](https://www.lugo.tw/tw/wewrite/detail/vibe-coding-analysis)
[^7]: [Day 10 - AI-DLC Sprint × Spec-Driven Development - iT 邦幫忙](https://ithelp.ithome.com.tw/articles/10378833)
[^8]: [从Vibe到Spec：让AI编程更可控的结构化思考](https://juejin.cn/post/7549468811349131283)
[^9]: [比較吓Power Developers同埋傳統developer嘅需求 - Reddit](https://www.reddit.com/r/PowerApps/comments/w3gv0i/comparing_the_demand_for_power_developers_versus/?tl=zh-hant)
[^10]: [AI 寫程式新流派的Vibe Coding 是什麼？開發者需知的操作技巧](https://ikala.ai/zh-tw/blog/ikala-ai-insight/vibe-coding-intro/)
[^11]: [Spec Driven Development (SDD) - A initial review](https://dev.to/danielsogl/spec-driven-development-sdd-a-initial-review-2llp)
[^14]: [Vibe Coding vs. Spec-Driven Development - Alt + E S V](https://redmonk.com/rstephens/2025/07/31/spec-vs-vibes/)
[^16]: [Vibe Coding 正夯！新一波AI 風潮讓生產力大躍進 - 經理人](https://www.managertoday.com.tw/articles/view/70837)
[^17]: [Spec-Driven Development (SDD) Is the Future of Software ...](https://medium.com/@shenli3514/spec-driven-development-sdd-is-the-future-of-software-engineering-85b258cea241)
[^18]: [Vibe Coding 是什麼？用「感覺」寫程式的新方式 - 網頁設計](https://www.ibest.tw/webdesign-detail/what-is-vibe-coding/)
[^19]: [許多人問我為什麼我不擔心被不被取代的問題這篇文章很好的 ...](https://www.facebook.com/groups/aigcapplied/posts/1012740300980803/)
[^21]: [低代码开发和传统编程方式相比，各自的优缺点是什么？](https://www.firecat-web.com/news/2562)
[^25]: [Kiro Spec 驅動開發：把「Vibe Coding」變「Viable Code」的 ...](https://tenten.co/learning/kiro-vibe-coding-tips/)
[^29]: [Spec-driven development with AI: Get started with a new open ...](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
[^30]: [Data-DI 部落格|開發新趨勢：vibe coding 是什麼？](https://www.data-di.com/blog/ai-lab-vibe-coding)
[^33]: [低代码开发和传统软件开发区别(优缺点对比) - 白码低代码平台](https://www.bnocode.com/article/ddmbk287.html)
[^36]: [「Vibe Coding」并非粗制滥造的借口——负责任的AI辅助开发 ...](https://zhuanlan.zhihu.com/p/1904284252171899918)
[^40]: [2025 年最強推薦Vibe Coding 工具一次看懂 - GitMind](https://gitmind.com/tw/best-vibe-coding-tools-2025.html)
[^51]: [重塑软件工程的未来：规范驱动的AI 编程代理 - Linguista](https://linguista.bearblog.dev/spec-driven-future-ai-agents-software-engineering/)
[^53]: [傳統應用程式vs. 現代應用程式：4 大關鍵差異 - Pure Storage](https://www.purestorage.com/tw/knowledge/legacy-apps-vs-modern-apps.html)
[^56]: [Vibe Coding是什麼？優缺點深度解析：AI開發革命還是暗藏 ...](https://www.lugo.tw/tw/wewrite/detail/vibe-coding-analysis)
[^57]: [Day 10 - AI-DLC Sprint × Spec-Driven Development - iT 邦幫忙](https://ithelp.ithome.com.tw/articles/10378833)
[^61]: [Spec-Driven Development (SDD) Is the Future of Software ...](https://medium.com/@shenli3514/spec-driven-development-sdd-is-the-future-of-software-engineering-85b258cea241)
[^65]: [低代碼vs 傳統開發：企業如何選擇最佳數位轉型方案](https://blog.double-wise.com/kintone-lowcode-vs-traditional/)
[^69]: [許多人問我為什麼我不擔心被不被取代的問題這篇文章很好的 ...](https://www.facebook.com/groups/aigcapplied/posts/1012740300980803/)
[^71]: [比較吓Power Developers同埋傳統developer嘅需求 - Reddit](https://www.reddit.com/r/PowerApps/comments/w3gv0i/comparing_the_demand_for_power_developers_versus/?tl=zh-hant)
[^72]: [Vibe Coding 正夯！新一波AI 風潮讓生產力大躍進 - 經理人](https://www.managertoday.com.tw/articles/view/70837)
[^77]: [低代码开发和传统编程方式相比，各自的优缺点是什么？](https://www.firecat-web.com/news/2562)
[^79]: [Kiro产品分析，spec-driven是Coding Agent的新范式吗？ - 知乎](https://zhuanlan.zhihu.com/p/1938623002817335530)
[^86]: [Data-DI 部落格|開發新趨勢：vibe coding 是什麼？](https://www.data-di.com/blog/ai-lab-vibe-coding)
[^103]: [重塑软件工程的未来：规范驱动的AI 编程代理 | Linguista](https://linguista.bearblog.dev/spec-driven-future-ai-agents-software-engineering/)
[^104]: [什麼是 Vibe Coding？跟一般人(非工程師)有什麼關係嗎？ - 瑞比智慧 airabbi](https://airabbi.com/blog/what-is-vibe-coding/)
[^105]: [傳統應用程式 vs. 現代應用程式：4 大關鍵差異 | Pure Storage](https://www.purestorage.com/tw/knowledge/legacy-apps-vs-modern-apps.html)
[^106]: [Spec-Driven Development：讓AI 真正理解你想要什麼 - iT 邦幫忙](https://ithelp.ithome.com.tw/articles/10378588)
[^111]: [GitHub spec-kitで始めるSpec-Driven Development入門 - Zenn](https://zenn.dev/yamitake/articles/spec-kit-introduction)
[^125]: [Day 9 - Spec-Driven Development：讓 AI 真正理解你想要什麼 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10378588)
[^127]: [Kiro Spec 驅動開發：把「Vibe Coding」變「Viable Code」的祕密武器](https://tenten.co/learning/kiro-vibe-coding-tips/)
[^1001]: [Codex: Automatically push to branch instead of creating a PR](https://community.openai.com/t/codex-automatically-push-to-branch-instead-of-creating-a-pr/1310383)
[^1003]: [如何将OpenAI的Codex CLI工具集成到您的开发工作流程中](https://blog.openreplay.com/zh/%E9%9B%86%E6%88%90-openai-codex-cli-%E5%B7%A5%E5%85%B7-%E5%BC%80%E5%8F%91%E5%B7%A5%E4%BD%9C%E6%B5%81/)
[^1004]: [Introducing upgrades to Codex - OpenAI](https://openai.com/index/introducing-upgrades-to-codex/)
[^1005]: [Introducing Codex - OpenAI](https://openai.com/index/introducing-codex/)
[^1006]: [Codex 简介 - OpenAI](https://openai.com/zh-Hans-CN/index/introducing-codex/)
[^1007]: [Codex CLI - OpenAI Developers](https://developers.openai.com/codex/cli/)
[^1009]: [OpenAI 剛發布了Codex — 一個能自動編寫程式碼、修復錯誤並 ...](https://www.facebook.com/tentencreative/posts/-openai-%E5%89%9B%E7%99%BC%E5%B8%83%E4%BA%86-codex-%E4%B8%80%E5%80%8B%E8%83%BD%E8%87%AA%E5%8B%95%E7%B7%A8%E5%AF%AB%E7%A8%8B%E5%BC%8F%E7%A2%BC%E4%BF%AE%E5%BE%A9%E9%8C%AF%E8%AA%A4%E4%B8%A6%E8%99%95%E7%90%86-pr-%E7%9A%84-ai-%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88%E5%8A%A9%E7%90%86-%E7%8F%BE%E5%B7%B2%E6%95%B4%E5%90%88%E5%88%B0-chatgpt-%E4%B8%AD%E9%80%99%E5%B0%8D%E9%96%8B%E7%99%BC%E8%80%85%E4%BE%86%E8%AA%AA%E6%98%AF%E5%80%8B%E5%B7%A8%E5%A4%A7%E8%AE%8A%E9%9D%A9/1151627797001961/)
[^1012]: [OpenAI Codex CLI，编程能力能否超越cursor ... - 知乎专栏](https://zhuanlan.zhihu.com/p/1896319937879970909)
[^1015]: [OpenAI Codex Cli - stardsd - 博客园](https://www.cnblogs.com/sddai/p/18830867)
[^1017]: [Ask Codex to code based on a link to a GitHub issue](https://community.openai.com/t/ask-codex-to-code-based-on-a-link-to-a-github-issue/1286634)
[^1019]: [Codex: update created PR/branch? : r/OpenAI - Reddit](https://www.reddit.com/r/OpenAI/comments/1kpk7p3/codex_update_created_prbranch/)
[^1022]: [OpenAI releases Codex CLI, an AI coding assistant built into ...](https://www.reddit.com/r/singularity/comments/1k0qc67/openai_releases_codex_cli_an_ai_coding_assistant/)
[^1025]: [OpenAI Codex CLI: Build Faster Code Right From Your Terminal](https://www.blott.studio/blog/post/openai-codex-cli-build-faster-code-right-from-your-terminal)
[^1026]: [How do I troubleshoot errors or issues when using Codex CLI?](https://milvus.io/ai-quick-reference/how-do-i-troubleshoot-errors-or-issues-when-using-codex-cli)
[^1028]: [Anyone else find Codex CLI dissapointing? : r/OpenAI - Reddit](https://www.reddit.com/r/OpenAI/comments/1k13flj/anyone_else_find_codex_cli_dissapointing/)
[^1029]: [命令行AI 编程工具Codex CLI 已集成全新GPT-5-Codex 模型](https://www.oschina.net/news/372581)
[^1048]: [GitHub Copilot features](https://docs.github.com/en/copilot/get-started/features)
[^1050]: [Using GitHub Copilot to create issues](https://docs.github.com/en/copilot/how-tos/use-copilot-for-common-tasks/use-copilot-to-create-issues)
[^1055]: [How to feed/provide documentations to Github Copilot for ...](https://www.reddit.com/r/GithubCopilot/comments/1gox5ng/how_to_feedprovide_documentations_to_github/)
[^1063]: [GitHub Copilot · Your AI pair programmer](https://github.com/features/copilot)
[^1065]: [GitHub Copilot in VS Code](https://code.visualstudio.com/docs/copilot/overview)
[^1068]: [Creating an issue - GitHub Docs](https://docs.github.com/articles/creating-an-issue)
[^1084]: [Using GitHub Copilot in your IDE: Tips, tricks, and best practices](https://github.blog/developer-skills/github/how-to-use-github-copilot-in-your-ide-tips-tricks-and-best-practices/)
[^1093]: [Does Copilot have whole codebase context? : r/GithubCopilot](https://www.reddit.com/r/GithubCopilot/comments/1iab2ln/does_copilot_have_whole_codebase_context/)
[^1098]: [Using GitHub Copilot to create issues](https://docs.github.com/en/copilot/how-tos/use-copilot-for-common-tasks/use-copilot-to-create-issues)
[^1106]: [How to use GitHub Copilot for multiple files? - Stack Overflow](https://stackoverflow.com/questions/76509513/how-to-use-github-copilot-for-multiple-files)
[^1107]: [Using GitHub Copilot to create issues](https://docs.github.com/en/copilot/how-tos/use-copilot-for-common-tasks/use-copilot-to-create-issues)
[^1111]: [Codex CLI - OpenAI Developers](https://developers.openai.com/codex/cli/)
[^1112]: [Introducing upgrades to Codex - OpenAI](https://openai.com/index/introducing-upgrades-to-codex/)
[^1114]: [Ask Codex to code based on a link to a GitHub issue](https://community.openai.com/t/ask-codex-to-code-based-on-a-link-to-a-github-issue/1286634)
[^1119]: [Using GitHub Copilot to Manage Issues (and Save Time)](https://dev.to/sarahcssiqueira/using-github-copilot-to-manage-issues-and-save-time-3549)
[^1123]: [Anyone else find Codex CLI dissapointing? : r/OpenAI - Reddit](https://www.reddit.com/r/OpenAI/comments/1k13flj/anyone_else_find_codex_cli_dissapointing/)
[^1127]: [OpenAI Codex vs. GitHub Copilot: Choosing the Right Fit](https://www.dhiwise.com/post/openai-codex-vs-github-copilot-a-practical-coding-showdown)
[^1129]: [How do I troubleshoot errors or issues when using Codex CLI?](https://milvus.io/ai-quick-reference/how-do-i-troubleshoot-errors-or-issues-when-using-codex-cli)

{% alert(note=true) %}
相關文章延伸閱讀

* [OpenSpec 深度解析：把「規格」從聊天記錄裡救出來的 SDD 框架](@/AI/openspec-sdd-repo-first-spec-engineering/index.md)
* [OpenSpec 團隊導入實戰指南：從安裝到第一個 PR 的完整教學](@/AI/openspec-team-adoption-practical-guide/index.md)
* [規格驅動開發 (Spec-Driven Development) 與 AI 協作全流程實戰](@/AI/sdd-ai-copilot-codex-devops-workflow.md) (本文)
* [2026 年 AI CLI 編碼工具價格大比拼：Claude Code、Codex CLI、Gemini CLI、GitHub Copilot](@/AI/ai-cli-coding-tool-pricing-comparison-2026/index.md)
{% end %}
