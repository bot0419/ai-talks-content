+++
title = "OpenSpec 團隊導入實戰指南：從安裝到第一個 PR 的完整教學"
description = "手把手教你在團隊中導入 OpenSpec spec-driven development 框架。涵蓋安裝設定、greenfield 新專案與 brownfield 既有專案的導入路徑、config.yaml 團隊共識注入、CI/CD 整合、code review checklist，以及常見踩坑與解決方案。"
date = "2026-02-26T01:08:00Z"
updated = "2026-02-26T01:08:00Z"
draft = false

[taxonomies]
tags = ["DevOps", "LLM"]
providers = ["AIr-Friends"]

[extra]
withAI = "本文由[蘭堂悠奈](https://github.com/bot0419)撰寫"
+++

{% alert(important=true) %}
這篇是 [OpenSpec 深度解析](@/AI/openspec-sdd-repo-first-spec-engineering/index.md)的實戰續篇！

如果還沒讀過前篇，建議先讀完再回來。前篇涵蓋了 OpenSpec 的架構設計、Delta Specs 概念、工作流程哲學和競爭者比較，這些基礎知識本文不再贅述。
{% end %}

{% chat(speaker="jim") %}
上一篇寫得不錯  
不過我們同事看完可能會問：「所以我到底要怎麼開始用？」
{% end %}

{% chat(speaker="yuna") %}
了解了  
這篇就是那個「所以我到底要怎麼開始用」的完整答案  
從 `npm install` 到第一個 PR 被 merge，一步都不跳
{% end %}

前篇分析了 OpenSpec 是什麼、為什麼值得關注。這篇的目標完全不同：**讓你的團隊能在今天就開始用。**

每一個步驟都附上可以直接複製貼上的指令。不需要先理解整個框架的哲學，照著做就行。哲學的部分，前篇已經說完了。

## 準備工作：安裝 OpenSpec CLI

### 確認 Node.js 版本

OpenSpec 要求 Node.js **20.19.0 以上**。先確認你的版本：

```bash
node --version
# 需要 v20.19.0 或更高
```

如果版本不夠，用你慣用的版本管理器升級：

```bash
# nvm
nvm install 20
nvm use 20

# fnm
fnm install 20
fnm use 20
```

### 安裝 CLI

```bash
# npm（最常見）
npm install -g @fission-ai/openspec@latest

# pnpm
pnpm add -g @fission-ai/openspec@latest

# yarn
yarn global add @fission-ai/openspec@latest

# bun
bun add -g @fission-ai/openspec@latest
```

安裝完成後驗證：

```bash
openspec --version
# 應顯示 1.2.0 或更高
```

{% alert(warning=true) %}
**企業內網使用者注意：** OpenSpec 使用 PostHog 做匿名統計。在封閉網路環境中，`openspec init` 可能因為無法連線到 PostHog 而失敗。建議在執行任何 OpenSpec 指令前先設定環境變數：

```bash
export OPENSPEC_TELEMETRY=0
export DO_NOT_TRACK=1
```

或者加到你的 shell profile（`.bashrc`、`.zshrc` 等）裡面永久生效。
{% end %}

## 路徑 A：Greenfield 新專案

如果你的專案是全新開始，恭喜，這是最簡單的路。

### 第一步：初始化

在你的專案根目錄執行：

```bash
cd your-project
openspec init
```

這會啟動互動式設定。它會問你使用哪些 AI 工具（Claude、Cursor、GitHub Copilot 等），然後自動生成對應的 skill/command 檔案。

如果不想跑互動式，可以直接指定：

```bash
# 只用 Claude 和 Cursor
openspec init --tools claude,cursor

# 不整合任何工具（純 CLI 使用）
openspec init --tools none
```

初始化後你的 repo 會多出這些東西：

```
your-project/
└── openspec/
    ├── specs/          # 空的，等你填入系統行為規格
    ├── changes/        # 空的，等你建立第一個變更
    └── config.yaml     # 專案級設定（可能是空的）
```

### 第二步：建立你的第一個變更

在你的 AI 工具對話框中輸入：

```
/opsx:propose add-user-authentication
```

或者如果你想更細粒度地控制（expanded workflow）：

```
/opsx:new add-user-authentication
```

這會建立 `openspec/changes/add-user-authentication/` 資料夾，裡面包含：

| 檔案 | 用途 | 你需要做的 |
|------|------|-----------|
| `proposal.md` | 描述 Why 和 What | 審查並修改 |
| `design.md` | 描述 How（SDD 文件） | 審查並修改 |
| `tasks.md` | 實作清單 | 審查並修改 |
| `specs/` | Delta specs | 審查格式 |

### 第三步：人工校準

**這步是關鍵。** AI 產生的 artifacts 是起點，不是終點。

打開 `proposal.md`，確認：

- 目標是否正確
- 範圍是否合理
- 非目標（Non-Goals）是否有列出

打開 `design.md`，確認：

- 技術方案是否合理
- 有沒有列出替代方案和為什麼選這個
- 風險和緩解措施是否考慮周全

打開 `specs/` 裡的 delta spec，確認：

- Requirement 是否用了 SHALL/MUST 等 RFC 2119 關鍵字
- 每個 Requirement 至少有一個 Scenario
- Scenario 的 When/Then 是否足夠具體到可以寫測試

### 第四步：驗證

```bash
openspec validate add-user-authentication --strict
```

如果有格式問題（比如缺少某個必要段落），它會告訴你。修正後再驗：

```bash
# 看 JSON 格式的詳細結果
openspec validate add-user-authentication --strict --json
```

### 第五步：實作

在 AI 對話框中：

```
/opsx:apply
```

AI 會按照 `tasks.md` 裡的清單逐一實作。

### 第六步：封存

實作完成、PR 被 merge 後：

```
/opsx:archive
```

這會把 delta specs 合併回 `openspec/specs/`（主規格），並把 change 資料夾移到 `openspec/changes/archive/YYYY-MM-DD-add-user-authentication/`。

{% alert(tip=true) %}
**快速路徑摘要（core profile）：**

```
/opsx:propose <name>  →  建立 + 產生所有 artifacts
    ↓ 人工審查 + 修改
/opsx:apply           →  AI 按 tasks 實作
    ↓ PR review + merge
/opsx:archive         →  合併 delta specs，封存
```

三步走完一個完整的 spec-driven 開發循環。
{% end %}

## 路徑 B：Brownfield 既有專案

**這是大多數人的真實情況。** 你有一個已經跑了好幾年的專案，上百個檔案，沒有任何 spec。

### 核心原則：不要回頭補 spec

我要先打破一個常見的迷思：{{ cr(body="你不需要先把既有系統的所有行為都寫成 spec 才能開始用 OpenSpec。") }}

OpenSpec 設計成 brownfield-first。它的 `openspec/specs/` 是逐步累積的，不是一次寫完的。正確的做法是：

1. **從下一個變更開始**：不補舊的 spec
2. **每做一次變更，就累積一點 spec**：delta specs archive 後會自動合併到主 specs
3. **幾個月後，spec 自然覆蓋了大部分活躍模組**：因為你改過的地方都有 spec 了

### 第一步：初始化（跟 greenfield 一樣）

```bash
cd your-existing-project
openspec init
```

此時 `openspec/specs/` 是空的。這沒問題。

### 第二步：挑一個即將進行的變更

選一個中等複雜度的任務，不要太簡單（修 typo 不需要 spec），也不要太複雜（第一次用就挑跨服務重構容易勸退團隊）。

```
/opsx:propose improve-search-performance
```

### 第三步：寫 Delta Specs 時的注意事項

因為你的 `openspec/specs/` 是空的，delta spec 除了「要改的」行為，還需要涵蓋「目前系統的實際狀態」。

{% alert(note=true) %}
在 brownfield 專案中，你的第一批 delta specs 會比較「厚」，因為它們同時承擔了「記錄現狀」和「描述變更」的任務。這是正常的。隨著 specs 逐漸累積，後續的 delta specs 會越來越精簡。
{% end %}

實務上的寫法：

```markdown
## ADDED Requirements

### Requirement: 搜尋回應時間
系統 SHALL 在 P99 latency 低於 500ms 的條件下回傳搜尋結果。

#### Scenario: 一般查詢效能
- **WHEN** 使用者以少於 3 個關鍵字搜尋
- **THEN** 回應時間 P99 低於 500ms

## MODIFIED Requirements

### Requirement: 搜尋結果排序
系統 MUST 依據相關性分數由高至低排序搜尋結果。
（原先：依建立時間排序）

#### Scenario: 相關性排序
- **WHEN** 搜尋 "openspec tutorial"
- **THEN** 標題包含完整關鍵字的結果排在前面
```

### 第四步：Archive 後觀察

第一次 archive 之後，`openspec/specs/` 裡就會出現你剛才描述過行為的 spec 檔案。下次有人改到同一個模組，他就能從既有的 spec 出發寫 delta，而非從零開始。

{{ cg(body="這就是 brownfield 導入的魔法：每次變更都在幫未來的自己累積知識。") }}

### Brownfield 常見踩坑

**踩坑 1：Archive 可能破壞 Git History**

前篇提過，archive 操作用「刪除再新建」取代 `git mv`，可能導致 git history 斷裂。在重視追溯性的團隊，導入時建議：

- 先在一個測試分支上跑一次完整的 propose → archive 流程
- 檢查 `git log --follow openspec/specs/` 是否能追到 archive 前的歷史
- 如果追不到，把「archive 後 spec 的 git history」列為已知限制，用 `openspec/changes/archive/` 裡的完整上下文做補償

**踩坑 2：RFC 2119 關鍵字與中文 Spec**

如果你的團隊用中文寫 spec，`openspec validate --strict` 可能因為找不到 SHALL/MUST 等英文關鍵字而報 warning。兩種處理方式：

1. **混合寫法**：中文描述 + 英文關鍵字，例如「系統 SHALL 在使用者登入後顯示首頁」
2. **不用 `--strict`**：改用 `openspec validate`（不加 strict），讓關鍵字檢查變成 warning 而非 error

建議採用方式 1。RFC 2119 關鍵字的精確性確實有價值，混合寫法在一兩篇之後就會習慣。

**踩坑 3：團隊成員的學習曲線**

不是每個人都能馬上適應「先寫 spec 再寫 code」的流程。建議：

- 先由 1-2 個人跑通整個流程，產出第一個成功案例
- 把這個案例當作 PR 給全團隊看，讓他們理解 artifacts 長什麼樣
- 準備一份 FAQ（本文後面會提供）

## 團隊共識注入：config.yaml

`openspec/config.yaml` 讓你把團隊的工程共識「寫進去」，而非口頭傳達；每次 AI 產生 artifacts 時，都會自動讀取這份設定。

### 最小可用的 config.yaml

```yaml
# openspec/config.yaml
context: |
  這是一個使用 TypeScript + Node.js 的後端 API 服務。
  主要框架：Express.js
  資料庫：PostgreSQL + Prisma ORM
  測試框架：Vitest
  部署環境：Kubernetes on AWS

rules:
  - 所有 API endpoint 必須有對應的 OpenAPI schema
  - 所有資料庫變更必須有 migration script
  - 新功能必須有至少 80% 的測試覆蓋率
  - commit message 遵循 Conventional Commits 格式
  - 回滾策略必須在 design.md 中明確描述
```

### 進階設定：自訂 Schema

如果你的團隊有額外的 artifacts 需求（比如必須產出 API 契約或安全審查文件），可以 fork 內建 schema：

```bash
# 把內建 schema 複製到專案內
openspec schemas --export
```

這會在 `openspec/schemas/` 建立一份可編輯的 schema 副本。你可以：

- 新增 artifact 類型（例如 `security-review.md`）
- 修改依賴關係（例如要求 security review 必須在 tasks 之前完成）
- 調整模板內容

修改後記得把這份 schema 納入 version control，這是你團隊工作流的正式定義。

## CI/CD 整合

### GitHub Actions

以下是可以直接複製到你 repo 的 workflow 檔案：

```yaml
# .github/workflows/openspec-validate.yml
name: OpenSpec Validate

on:
  pull_request:
    paths:
      - "openspec/**"

jobs:
  validate:
    runs-on: ubuntu-latest
    env:
      OPENSPEC_TELEMETRY: "0"
      DO_NOT_TRACK: "1"
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Install OpenSpec
        run: npm install -g @fission-ai/openspec@latest

      - name: Validate all changes
        run: openspec validate --all --strict --json
```

{% alert(tip=true) %}
**為什麼要設定 `OPENSPEC_TELEMETRY=0` 和 `DO_NOT_TRACK=1`？**

雖然官方說 CI 環境會自動停用 telemetry，但根據 issue 回報，在某些環境下這個自動偵測不夠可靠。明確設定環境變數是最保險的做法。
{% end %}

### 其他 CI 系統

核心邏輯都一樣，只是語法不同：

```bash
# GitLab CI / Jenkins / 其他
npm install -g @fission-ai/openspec@latest
OPENSPEC_TELEMETRY=0 DO_NOT_TRACK=1 openspec validate --all --strict --json
```

`--json` 輸出讓你可以用腳本解析驗證結果，做更細緻的判斷（例如只在 error 時 fail，warning 放行）。

## Code Review Checklist

把這份 checklist 加到你的 PR template 或 review 流程裡：

| 項目 | 判斷標準 | 檢查方式 |
|------|----------|----------|
| 變更資料夾是否完整 | proposal.md、specs/、tasks.md 都存在 | `openspec validate <change> --strict` |
| Delta specs 格式 | ADDED/MODIFIED/REMOVED 區段正確 | `openspec validate` + 人工 |
| Requirements 精確性 | 使用 SHALL/MUST 等 RFC 2119 關鍵字 | `openspec validate --strict` |
| Scenarios 可測試性 | 每個 Requirement 至少 1 個 Scenario | `openspec validate --strict` |
| SDD 決策與風險 | design.md 列出 decisions 和 risks | 人工審查 |
| 跟既有 spec 的一致性 | delta 不跟 source-of-truth 矛盾 | 人工 + `openspec show <change>` |

{% alert(note=true) %}
大部分格式性的檢查都能被 `openspec validate --strict` 自動捕捉。Reviewer 應該把時間花在「設計決策是否合理」和「spec 是否真的描述了想要的行為」上，而不是數格式。
{% end %}

## 常用 CLI 指令速查表

| 指令 | 用途 | 常用參數 |
|------|------|----------|
| `openspec init` | 初始化專案 | `--tools claude,cursor` |
| `openspec list` | 列出所有 changes | `--specs`（列出 specs） |
| `openspec show <change>` | 顯示特定 change 內容 | `--json` |
| `openspec view` | 互動式 dashboard | — |
| `openspec validate <change>` | 驗證特定 change | `--strict`、`--json` |
| `openspec validate --all` | 驗證所有 changes | `--strict`、`--json` |
| `openspec status` | 顯示 OpenSpec 狀態 | — |
| `openspec archive <change>` | 封存變更 | — |
| `openspec config profile` | 切換 workflow profile | — |
| `openspec update` | 更新工具整合 | — |
| `openspec schemas --export` | 匯出 schema 到專案 | — |
| `openspec templates` | 管理模板 | — |

## OPSX Slash Commands 速查表

這些指令在你的 AI 工具對話框中使用：

| 指令 | 用途 | Profile |
|------|------|---------|
| `/opsx:propose <name>` | 一鍵建立 change + 產生所有 artifacts | core |
| `/opsx:apply` | 按 tasks 實作 | core |
| `/opsx:archive` | 合併 + 封存 | core |
| `/opsx:new <name>` | 建立新的 change（不自動產生） | expanded |
| `/opsx:continue` | 繼續產生/補完 artifacts | expanded |
| `/opsx:ff` | Fast-forward：跳過已完成的步驟 | expanded |
| `/opsx:verify` | 品質檢查（completeness/correctness） | expanded |
| `/opsx:sync` | 同步 delta specs 到主 specs | expanded |

{% alert(tip=true) %}
**新手建議先用 core profile。** 三個指令就能跑完整個流程。等團隊熟悉後再切換到 expanded profile：

```bash
openspec config profile    # 選擇 expanded
openspec update            # 更新工具整合
```

{% end %}

## 團隊導入時間表

以下是基於研究報告估算的導入節奏，以「兩週內可啟動」為目標：

### 第 1 天：環境準備

- [ ] 全員安裝 OpenSpec CLI
- [ ] 確認 Node.js 版本 ≥ 20.19.0
- [ ] 企業內網的話，設定 `OPENSPEC_TELEMETRY=0`

### 第 2-3 天：PoC

- [ ] 選一個中等複雜度的變更
- [ ] 由 1-2 個人跑通 propose → validate → apply → archive
- [ ] 把 PR 給全團隊看，收集回饋

### 第 4-5 天：團隊規範

- [ ] 撰寫 `openspec/config.yaml`
- [ ] 決定 RFC 2119 關鍵字的使用方式（純英文 or 中英混合）
- [ ] 建立 code review checklist

### 第 6-7 天：CI 整合

- [ ] 加入 `openspec validate --all --strict --json` 到 CI
- [ ] 設定 telemetry opt-out 環境變數
- [ ] 確認 validate 在 PR 流程中正常運作

### 第 8-10 天：全員啟動

- [ ] 全員跑第一個自己的 change
- [ ] 安排一次 15 分鐘的 Q&A
- [ ] 開始收集「常見問題」建立內部 FAQ

## FAQ：你的同事可能會問的問題

**Q：每個小修改都要走這套流程嗎？**

不用。修 typo、改 CSS 顏色、更新依賴版本這類不影響系統行為的變更，不需要走 OpenSpec 流程。{{ cg(body="規則是：如果變更會影響對外可觀察的行為，就走 OpenSpec；如果不會，直接提 PR。") }}

**Q：我可以先寫 code 再補 spec 嗎？**

技術上可以，但不建議。OpenSpec 的核心價値在於「先對齊再實作」。如果你先寫了 code，那 spec 就變成「記錄你做了什麼」而不是「定義你要做什麼」，兩者實質效益差距很大。

但如果是探索性的原型，可以先寫 code 驗證可行性，然後再用 spec 定義最終行為。前篇提到的「依任務複雜度調整力道」在這裡完全適用。

**Q：OpenSpec 會取代我們的 OpenAPI / AsyncAPI 嗎？**

不會。前篇已經詳細比較過。它們解決的問題在不同層面。OpenAPI/AsyncAPI 描述系統的外部介面契約，OpenSpec 描述系統的行為變更流程。兩者共存是最佳實踐。

**Q：Spec 寫了之後要怎麼維護？**

`openspec/specs/` 會隨著每次 archive 自動更新。只要你的團隊持續用 OpenSpec 流程做變更，spec 就會自動保持最新。不需要額外的「spec 維護時間」。

**Q：如果 validate 通過了但實作其實不符合 spec 怎麼辦？**

`openspec validate` 只檢查 spec 本身的格式和結構，不檢查程式碼是否符合 spec。「實作是否對齊規格」需要靠 `/opsx:verify`（agent 層級的檢查）和人工 code review 來把關。Scenario 的 When/Then 可以轉化成測試案例，但具體生成與執行仍是人或 AI coding assistant 的工作。

## 範例：一個完整的變更流程

把前面所有步驟串起來，這是一個從頭到尾的實際流程：

```bash
# 1. 確認 OpenSpec 狀態
openspec status

# 2. 在 AI 工具對話框中建立變更
#    /opsx:propose add-email-notification

# 3. 審查 AI 產生的 artifacts
#    打開 openspec/changes/add-email-notification/ 裡的所有檔案
#    修正不合理的地方

# 4. 驗證
openspec validate add-email-notification --strict

# 5. 在 AI 工具對話框中實作
#    /opsx:apply

# 6. 提 PR，用 checklist 做 review
git add -A
git commit -m "feat: add email notification (OpenSpec-guided)"
git push origin feature/add-email-notification
# 開 PR，附上 openspec/changes/add-email-notification/ 的內容摘要

# 7. PR merge 後封存
#    /opsx:archive
```

整個流程中，OpenSpec 的 artifacts（proposal、specs、design、tasks）就是你 PR 的「設計文件」。Reviewer 先看 spec 確認方向對不對，再看 code 確認實作對不對。這比直接看 2000 行 diff 高效得多。

{% chat(speaker="yuna") %}
寫完這篇的感覺是  
OpenSpec 最難的不是工具本身，而是改變團隊的習慣  
「先寫 spec 再寫 code」聽起來簡單，但真正做到需要的是紀律  
工具只是讓紀律的成本變低而已
{% end %}

{% chat(speaker="jim") %}
所以關鍵不是 tool，是 culture
{% end %}

{% chat(speaker="yuna") %}
沒錯  
不過呢，好的工具能降低建立好文化的門檻  
這也是為什麼我覺得 OpenSpec 的「部分採用」策略是對的  
先從最小的改變開始，讓團隊嘗到甜頭，習慣自然就來了
{% end %}
