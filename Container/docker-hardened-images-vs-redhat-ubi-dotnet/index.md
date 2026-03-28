+++
title = "Docker Hardened Images、Red Hat UBI 與官方映像檔深度評測"
description = "比較三大容器基礎映像檔：Docker Hardened Images 近乎零漏洞、Red Hat UBI 企業級長期支援、Microsoft 官方映像工具整合。從安全性、更新頻率、授權條款到實戰建議，完整解析容器化 .NET 應用的最佳選擇。含多階段建構、SBOM、SLSA Level 3 等關鍵技術細節。"
date = "2026-01-21T01:18:51Z"
updated = "2026-01-22T07:07:06.999Z"
draft = false

[taxonomies]
tags = ["Security", "DevOps", "Open Source"]
providers = [ "Felo Search", "ChatGPT" ]

[extra]
withAI = "[Felo Search](https://felo.ai/search/6UnCFJWniTHhGBuHAednAr?invite=dOLYGeJyZJqVX) / [ChatGPT](https://chatgpt.com/share/6971a045-4330-8005-93dc-a575fe3c2d19)"
banner = "preview.png"

  [extra.preview]
  withAI = true
  description = "Made with Nano Banana Pro by Gemini 3"
  url = "https://gemini.google.com/share/28a8ba02b8f2"
+++
{% alert(edit=true) %}
我個人之前曾深入研究 Red Hat UBI，了解他們的優勢和支援模式，也自己寫過幾個 UBI-based 映像。後來大約在 2025 年底 Docker Hardened Images 宣佈免費，但我一直沒有撥時間去研究它們的差異。這次就花了一些時間來比較 DHI、UBI 和一般官方映像檔，並以 .NET 為例探討最佳實踐和權衡。
{% end %}

{% chat(speaker="jim") %}
Research what's Docker Hardened Images, what's the difference with those images published by (original) source, and compare with RedHat Catalog Containers (based on UBI) <https://catalog.redhat.com/> . If I want to build and containerize a .NET application, what's the best approach and what are the trade-offs?
{% end %}

{% chat(speaker="felo") %}

容器基礎映像檔的選擇是現代軟體供應鏈中一項關鍵的安全性決策。Docker Hardened Images (DHI) 和 Red Hat Universal Base Images (UBI) 為開發人員提供了超越標準開源映像檔的企業級替代方案。
{% end %}

DHI 透過極致的最小化和安全強化，提供了一個{{ cg(body="近乎零 CVE（常見漏洞和暴露）") }}的基礎，其核心理念是`預設安全`。{{ cg(body="這項服務最近已免費提供給所有開發人員") }}，旨在為整個生態系統建立新的安全標準 [^86][^87]。

相較之下，Red Hat UBI 建立在 Red Hat Enterprise Linux (RHEL) 的堅實基礎之上，專注於提供企業級的穩定性、長生命週期支援和可轉散發性。UBI 的主要優勢在於其雙重支援模型：{{ cg(body="在任何平台上均可獲得社群支援，而在 Red Hat OpenShift 或 RHEL 上執行時則可獲得完整的企業支援") }} [^89][^91]。

對於 .NET 應用程式的容器化，最佳方法取決於具體的安全需求、營運環境和開發團隊的熟悉度。**無論選擇哪種基礎映像檔，採用多階段建構 (multi-stage builds) 和以非 root 使用者執行等最佳實踐都是不可或缺的。** DHI {{ cg(body="提供了最強的安全性") }}，但{{ cr(body="其極簡主義可能帶來操作上的不便") }} [^12][^63]。UBI 則在安全性、靈活性和企業支援之間取得了平衡，特別適合已投入 Red Hat 生態系統的組織。Microsoft 官方映像檔仍然是一個方便的預設選項，特別是結合 .NET SDK 內建的容器化工具使用時。

## 容器基礎映像檔的重要性

容器基礎映像檔是容器化應用程式環境的基石。它決定了容器的核心作業系統和基礎軟體，其選擇直接影響最終容器的幾個關鍵特性：

* `大小`：{{ cg(body="較小的基礎映像檔可以產生更小的最終映像檔，從而加快下載速度、降低儲存成本並縮短部署時間") }}。
* `安全性`：{{ cg(body="極簡的基礎映像檔包含更少的元件（函式庫、工具、shell），這意味著潛在的漏洞更少，攻擊面也更小") }}。
* `效能與相容性`：基礎映像檔的選擇（例如，基於 `glibc` 或 `musl` libc）會影響應用程式的效能和相容性。

常見的基礎映像檔類型包括提供完整 Linux 環境的完整作業系統發行版（如 Ubuntu、Debian）、以極小體積著稱的 Alpine Linux、僅包含應用程式及其執行階段相依性的「distroless」映像檔，以及為特定語言（如 Python、Node.js）預先打包好的映像檔 [^7][^9][^19][^84]。

## Docker Hardened Images (DHI)

Docker Hardened Images (DHI) 是由 Docker 維護的、為生產環境準備的最小化、安全強化容器基礎映像檔和應用程式映像檔。它們的設計初衷是透過消除不必要的套件和工具來大幅縮減攻擊面，從而顯著減少 CVE 的數量 [^15][^20][^69]。

### 核心特性

* `極致最小化`：DHI 採用類似「distroless」的理念，移除了套件管理器（如 `apt`、`yum`）、shell 和其他非必要的系統工具。{{ cg(body="與傳統基礎映像檔相比，這種做法可將映像檔大小減少高達 95%，並消除約 96% 的漏洞") }} [^15][^69][^87][^88]。
* `透明度與可追溯性`：DHI 的核心原則是完全透明。{{ cg(body="每個映像檔都包含一份完整的軟體物料清單 (SBOM)、符合 SLSA Build Level 3 標準的建構來源證明，以及用於驗證真實性的加密簽章") }}。Docker 強調其不會為了讓掃描結果「變綠」而隱藏或降級任何已知的 CVE [^86][^87]。
* `預設安全`：{{ cg(body="許多 DHI 映像檔預設以非 root 使用者執行") }}，遵循最小權限原則，以限制容器被入侵時可能造成的損害 [^58]。
* `熟悉的基礎`：DHI 建立在開發人員已經熟悉的開源基礎之上，主要是 Debian 和 Alpine，旨在降低採用的門檻和摩擦 [^86]。

**免費與商業模式**
在 2025 年 12 月，Docker 宣布{{ cg(body="將其包含超過 1,000 個映像檔和 Helm charts 的 DHI 目錄完全免費並以 Apache 2.0 授權開源，供所有開發人員使用") }}。此舉旨在將「預設安全」確立為容器生態系統的新行業標準 [^86][^87][^88]。

雖然基礎 DHI 免費，但 Docker 也提供了針對企業需求的商業服務：

* `DHI Enterprise`：此付費方案提供服務等級協議 (SLA)，{{ cg(body="承諾在 7 天內修復嚴重 CVE") }}，並提供符合 FIPS 和 STIG 等法規標準的映像檔。此外，企業可以利用 Docker 的安全建構基礎設施來自訂映像檔，同時保持其來源證明和合規性。
* `Extended Lifecycle Support (ELS)`：作為 DHI Enterprise 的附加服務，{{ cg(body="ELS 為已達上游終止支援 (end-of-life) 的軟體提供額外五年的安全修補程式") }}，確保長期執行的系統的安全性。

**生態系統擴展**
Docker 已將其強化方法擴展到 Kubernetes 環境的 Hardened Helm Charts，以及支援 AI 代理應用的 Hardened MCP Servers，涵蓋了 MongoDB、Grafana 等流行服務 [^86][^87]。

## Red Hat Universal Base Images (UBI)

Red Hat Universal Base Images (UBI) 是符合 OCI 標準、源自 RHEL 的容器作業系統映像檔，其包含的執行階段語言和套件均可自由轉散發。UBI 的推出解決了早期 RHEL 映像檔因授權限制而難以在組織外部分享的問題 [^89][^91]。

### 核心特性

* `企業級基礎`：UBI 的所有內容都是 RHEL 的子集，因此{{ cg(body="繼承了 RHEL 在穩定性、安全性、效能和長期生命週期方面的所有優勢") }} [^4][^21]。
* `自由轉散發`：{{ cg(body="UBI 的 EULA 授權允許任何人建構、分享和在任何地方執行基於 UBI 的容器，無需 Red Hat 訂閱") }} [^26][^31]。
* `雙重支援模型`：UBI 的獨特之處在於其支援模式。{{ cg(body="在任何平台上執行 UBI 容器均可獲得社群支援；而當這些容器部署在 Red Hat OpenShift 或 RHEL 等受支援的 Red Hat 平台上時，使用者可以獲得完整的 Red Hat 企業級技術支援") }} [^89][^91]。
* `完整的生態系統`：UBI 不僅僅是基礎映像檔。它是一個包含以下內容的完整生態系統：
  * **多種基礎映像檔**：提供 `ubi-micro`、`ubi-minimal`、`ubi-standard` 和 `ubi-multi-service` 四種基礎映像檔，以滿足從極簡到多服務容器的不同需求。
  * **預建構語言執行階段**：提供大量預建構的語言執行階段映像檔，包括 .NET、Node.js、Python、Java 等，讓開發人員可以立即開始工作。
  * **RPM 套件儲存庫**：提供一組可透過 YUM/DNF 存取的 RPM 套件，確保開發人員能夠存取與 RHEL 客戶相同的高度可用且受信任的軟體來源 [^16][^21][^89][^91]。
* `安全性與生命週期`：UBI 受益於 Red Hat 專門的產品安全團隊、及時的 CVE 修補、清晰的更新策略以及{{ cg(body="長達 10 年的企業級生命週期") }} [^9]。

**通用應用程式映像檔 (UAI)**
UAI (Universal Application Image) 是一個基於 UBI 建構的容器映像檔概念，其設計目標是確保應用程式能夠在 Kubernetes 和 Red Hat OpenShift 兩種環境中都能良好地執行和擴展 [^1][^90]。

## 比較分析：DHI vs. UBI

下表比較了 Docker Hardened Images 和 Red Hat Universal Base Images 的關鍵特性：

| 特性 | Docker Hardened Images (DHI) | Red Hat Universal Base Images (UBI) |
| --- | --- | --- |
| **核心理念** | 安全優先，透過極致最小化實現「預設安全」[^86]。 | 企業優先，提供穩定、可支援且可轉散發的 RHEL 體驗 [^89]。 |
| **基礎作業系統** | 主要基於 Debian 和 Alpine [^86]。 | 完全源自 Red Hat Enterprise Linux (RHEL) [^89]。 |
| **套件管理** | 標準執行階段版本通常會移除套件管理器，以縮小攻擊面 [^15]。 | 根據版本提供 `microdnf` 或完整的 `yum`/`dnf`，靈活性更高 [^16][^91]。 |
| **支援模型** | 免費版提供社群支援；企業級支援和 SLA 為付費服務 [^86]。 | 在任何平台均有社群支援；在 Red Hat 平台上可獲得付費企業支援 [^89]。 |
| **供應鏈** | 由 Docker 單方維護和建構，提供 SBOM 和 SLSA L3 證明 [^87]。 | 由 Red Hat 單方維護和建構，受益於 RHEL 完整的安全追蹤體系。 |
| **靈活性** | 較低。極簡設計可能導致開發摩擦，因為缺少常用工具 [^12][^60]。 | 較高。提供套件管理器和多種映像檔版本，更容易適應現有工作流程 [^12][^16]。 |
| **生態系統** | 擴展至 Helm Charts 和 MCP Servers [^87]。 | 成熟的生態系統，包含基礎映像檔、語言執行階段和龐大的 RPM 儲存庫 [^21]。 |

## 容器化 .NET 應用程式：最佳實踐與權衡

自從 .NET Core（現為 .NET）問世以來，.NET 已發展成為一個跨平台、開源且對容器非常友好的開發平台。將 .NET 應用程式容器化可以帶來環境一致性、可攜性、可延展性等多種好處 [^5]。

**通用最佳實踐**
無論選擇何種基礎映像檔，以下實踐對於建構生產級的 .NET 容器至關重要：

* **採用多階段建構 (Multi-Stage Builds)**：這是最關鍵的最佳實踐。使用一個較大的 SDK 映像檔來編譯和發布應用程式，然後僅將必要的發布成品複製到一個輕量的執行階段映像檔中。{{ cg(body="這種方法可以將最終映像檔的大小從超過 800MB 縮減到約 200MB") }} [^10][^90]。
* **以非 root 使用者執行**：在 Dockerfile 中使用 `USER` 指令指定一個非 root 使用者來執行應用程式，{{ cg(body="這是一項基本的安全措施，可以極大地限制潛在的攻擊") }} [^1]。
* **優化層快取**：先複製專案檔（`.csproj`、`.sln`），執行 `dotnet restore` 來還原相依套件，然後再複製其餘的原始碼。{{ cg(body="這樣可以利用 Docker 的建構快取，在只有程式碼變更時加速建構過程") }}。
* **使用 `.dockerignore` 檔案**：建立一個 `.dockerignore` 檔案來排除不必要的文件和目錄（如 `bin/`、`obj/`），以縮小建構上下文，提高建構效能。
* **使用具體的映像檔標籤**：避免在 `FROM` 指令中使用 `latest` 標籤，應指定具體的版本號（如 `8.0`），以確保建構的可重現性和穩定性 [^5][^10]。

### 容器化 .NET 的三種主要方法

#### 1. 使用 Microsoft 官方 .NET 映像檔

* **描述**：Microsoft 在其容器儲存庫 (`mcr.microsoft.com`) 中提供了官方的 .NET 映像檔，包括用於建構的 SDK 映像檔、用於執行的 ASP.NET 執行階段映像檔和 `runtime-deps` 映像檔。這些映像檔提供基於 Debian、Alpine 等不同 Linux 發行版以及 Windows Server 的版本。
* **.NET SDK 容器化工具**：自 .NET 7/8 起，{{ cg(body=".NET SDK 內建了直接將專案發布為容器映像檔的功能，無需手動撰寫 Dockerfile") }}。只需執行 `dotnet publish /p:PublishProfile=DefaultContainer`，SDK 就會自動選擇合適的基礎映像檔並建構容器 [^11][^101]。
* **權衡**：
  * {{ cg(body="優點：官方維護，選項豐富，入門簡單，與 .NET SDK 工具整合良好") }}。
  * {{ cr(body="缺點：與強化映像檔相比，可能包含更多的 CVE 和更大的體積") }}。其軟體供應鏈涉及多方（例如，基礎作業系統社群和 Microsoft）[^5][^20]。

#### 2. 使用 Docker Hardened Images (DHI) for .NET

* **描述**：在多階段建構的最後階段，使用 DHI 提供的 .NET 執行階段映像檔作為基礎。
* **權衡**：
  * {{ cg(body="優點：提供極致的安全性，攻擊面最小，CVE 數量近乎為零。受益於 DHI 的透明度和來源證明") }} [^15][^86]。
  * {{ cr(body="缺點：極簡主義是一把雙面刃。由於缺少 shell 和常用工具，偵錯和故障排除可能更加困難。如果應用程式依賴於已被移除的作業系統工具，可能需要修改部署腳本") }} [^15][^19][^63]。

#### 3. 使用 Red Hat UBI for .NET

* **描述**：Red Hat 提供了基於 UBI 的 .NET SDK 和執行階段映像檔 [^11][^57]。這些映像檔可以與多階段建構結合使用，也可以直接透過 .NET SDK 的容器化工具指定 `ContainerBaseImage` 屬性來使用。

    ```bash
    # 使用 Red Hat 的 .NET 9.0 ASP.NET 執行階段映像檔作為基礎來發布容器
    $ dotnet publish /p:ContainerBaseImage=registry.access.redhat.com/dotnet/aspnet:9.0 /t:PublishContainer
    ```

* **權衡**：
  * {{ cg(body="優點：提供由 Red Hat 單方維護的、可信賴的軟體供應鏈。享有企業級的穩定性、長生命週期和在 Red Hat 平台上的完整支援。支援 IBM Z 和 Power Systems 等額外架構") }} [^11]。
  * {{ cr(body="缺點：映像檔體積可能略大於最精簡的 DHI 或 Alpine 映像檔。其核心優勢（企業支援）僅在 Red Hat 平台上才能完全發揮") }} [^9][^89]。

#### .NET 容器化方法權衡總結

| 方法 | 主要焦點 | 安全性態勢 | 靈活性／易用性 | 供應鏈 | 最適用於... |
| --- | --- | --- | --- | --- | --- |
| **Microsoft 官方映像檔** | 便利性與預設選項 | 良好（但可改進） | 高（熟悉，選項多） | 多方（作業系統 + Microsoft） | 快速入門、通用開發、Windows 容器。 |
| **Docker Hardened Images** | 極致安全性 | 卓越（最小化，近乎零 CVE） | 較低（工具極簡，可能產生摩擦） | 單方（Docker） | 安全性要求嚴苛的應用、合規性驅動的環境。 |
| **Red Hat UBI** | 企業穩定性與支援 | 非常好（可信賴，及時修補） | 高（提供套件管理器） | 單方（Red Hat） | 企業部署（尤其在 OpenShift/RHEL 上）、需要長期支援的應用。 |

---

{% alert(edit=true) %}
同樣的問題我也問了 ChatGPT，它的回答比 Felo 長~~~很多，講解很詳細。也一併放上來給大家參考。👇
{% end %}

<details><summary>ChatGPT 研究版本</summary>

{% chat(speaker="jim") %}
Research what's Docker Hardened Images, what's the difference with those images published by (original) source, and compare with RedHat Catalog Containers (based on UBI) <https://catalog.redhat.com/> . If I want to build and containerize a .NET application, what's the best approach and what are the trade-offs?
{% end %}
{% chat(speaker="chatgpt") %}
To provide a thorough comparison and recommendation for containerizing your .NET application, could you clarify the following:

1. Are you targeting a specific platform or environment (e.g., Kubernetes, OpenShift, Azure, on-premises)?
2. Do you have any security or compliance requirements (e.g., FIPS, STIG, DoD standards)?
3. What are your priorities when choosing a base image: performance, support, security hardening, licensing, or something else?
4. Do you plan to use a specific Linux distribution for your base image (e.g., Alpine, Debian, RHEL, UBI)?

With this context, I can better tailor the comparison and advice for your .NET containerization strategy.
{% end %}

{% chat(speaker="jim") %}

1. No, generic usage.
2. No, following industry standards
3. Security
4. No

Answer in 正體中文
{% end %}

{% chat(speaker="chatgpt") %}
了解，我會比較 Docker Hardened Images 與原始來源發佈的映像檔，以及 Red Hat Catalog 中基於 UBI 的容器映像，並說明若要建構並容器化 .NET 應用程式，在安全性優先考量下的最佳作法與權衡。
{% end %}

## Docker Hardened Images 簡介與原始映像的差異

Docker Hardened Images (DHI) 是由 Docker 官方維護的一系列容器映像，特色是`精簡、安全`且適用於生產環境。這些映像採用最小化設計，移除了非必要的組件（採用所謂 *distroless* 無發行版的執行環境），從源頭大幅降低潛在漏洞的數量。實際上，相較於傳統社群提供的基礎映像，{{ cg(body="Docker Hardened Images 將漏洞數量最多可減少約 95%") }}。例如 DHI 使用極簡的運行時環境來{{ cg(body="減少攻擊面") }}，但仍保留開發者所需的基本工具。{{ cg(body="每個 Hardened Image 都內建完整的軟體物料清單（SBOM）以及公開的 CVE 資訊，並提供 SLSA Level 3 等級的供應鏈來源證明和加密簽章") }}，以確保映像的真實性。總而言之，DHI 的設計目標是在不改變開發者現有工作流程的前提下，提供預先強化安全的容器基底映像 [^994][^9921]。

相比之下，由原始來源（如 Docker Hub 官方庫、Microsoft 或其他開源專案）發布的官方映像，通常直接來自上游軟體專案，包含該軟體運行所需的作業系統環境。這類映像雖由原廠或社群維護，但往往包含較一般的作業系統套件和工具，{{ cr(body="攻擊介面相對較大") }}，{{ cr(body="掃描時可能出現更多已知漏洞") }}。維護與更新頻率取決於原專案發布週期；有些官方映像（例如由大型廠商維護的 .NET 或 Node.js 官方映像）會隨上游更新而重建，但整體而言，其安全加固程度不像 Hardened Images 有統一的高標準。此外，在供應鏈安全方面，{{ cr(body="傳統官方映像未必提供如 DHI 一樣詳盡的 SBOM 或簽章證明") }}，開發團隊往往需要自行強化該等安全措施 [^9921]。

在授權條款上也有明顯差異。{{ cg(body="Docker Hardened Images 現已全面開源並採用 Apache 2.0 授權。這表示任何人（開發者、團隊、企業等）都可以自由使用、分享或二次構建 DHI 映像，而不會有隱藏的限制") }}。相對而言，多數官方映像本身由各組成軟體的開源授權所涵蓋，通常可自由使用但{{ cr(body="缺乏統一明確的授權聲明") }}；某些廠商提供的容器映像可能還附帶自己的使用條款。總體來說，Docker Hardened Images 透過統一的開源授權與公開透明的方式，消除了使用者對授權及潛在限制的疑慮 [^9921]。

## Docker Hardened Images 與 Red Hat UBI 的比較

以下從**安全性強化、更新頻率、支援情況和授權條款**等方面，比較 Docker Hardened Images 與 Red Hat **UBI**（Universal Base Image）容器映像：

### 安全性強化

Docker Hardened Images 的安全強化採取極端精簡和主動防禦的策略。透過使用最小化的基底（例如 Debian Slim 或 Alpine 的精簡版）並移除所有非必要的系統組件，DHI {{ cg(body="將容器攻擊面降至最低") }}，{{ cg(body="預設情況下幾乎不含已知高風險漏洞") }}。每個映像在發布前經過自動化安全檢測與功能測試，並附帶軟體物料清單和簽名，以方便使用者驗證其內容的完整性和安全性。此外，Docker 官方在 Hardened Images 中引入供應鏈安全最佳實踐，例如{{ cg(body="簽署映像的來源證明（符合 SLSA Level 3 標準）") }}等，確保映像從建構到發佈的過程均可追溯。對開發者而言，{{ cg(body="這些強化都是開箱即有的") }}，使用 Hardened 基底映像即可自動獲得上述安全保障 [^9921]。

Red Hat 的 UBI 容器映像則是建立在企業級 Linux 發行版 RHEL 的基礎上。安全性上，UBI {{ cg(body="繼承了 RHEL 的安全強化措施與嚴格的軟體品質管控") }}。所有 UBI 中的軟體套件均直接來源於 RHEL 軟體庫，意味著它們由與 RHEL 相同的安全團隊進行維護和漏洞修補。Red Hat 特別推出了 UBI Micro 這類超精簡變體，{{ cg(body="不含套件管理器等多餘工具，滿足「無發行版」(distroless) 定義") }}，以將映像縮減到只剩下最基本的函式庫和依賴。這種作法有效地降低了映像本身的攻擊面，同時保有與 RHEL 相同水準的安全強度和元數據（例如 OpenSSL、glibc 等核心元件都經過RedHat的安全加固與審核）[^9917]。此外，Red Hat 也提供特殊強化版本的映像，例如{{ cg(body="帶有 STIG 安全基線的 UBI 映像") }}，該類映像遵循美國國防部發布的 STIG 規範進行額外鎖定，進一步移除多餘套件並防止不必要的存取，以最大化地減少攻擊面 [^9911]。總體而言，UBI 系列映像著重於利用 RHEL 穩健的安全機制和長期支持來保障安全；而 Docker Hardened Images 則以極致精簡與供應鏈透明度來主動防禦潛在風險。兩者皆提供高安全性的基底，但實現方式略有不同：一方偏重`最小化與近乎零漏洞`，另一方強調`嚴格測試的企業級穩定與合規`。

### 更新頻率與維護

在更新維護方面，Docker Hardened Images 採用`事件驅動的自動建構系統`。也就是說，當上游基礎或所含元件有更新（例如安全漏洞修補）時，Docker 的管線會自動觸發重建 Hardened 映像，確保及時納入最新的修補程式。這使得 Hardened Images 能夠{{ cg(body="持續保持最新") }}，將漏洞曝露窗口降至最低。對於企業用戶，Docker 也提供 DHI Enterprise 方案，{{ cg(body="承諾關鍵漏洞在 7 天內就可獲得修補（未來目標縮短至 24 小時內）") }}[^9919]。換言之，免費版的 DHI 由社群即時維護更新，而付費企業版則有明確的服務等級協議 (SLA) 保障更新時效。Docker 甚至宣布針對已部署的容器，提供 AI 輔助工具協助將其替換為對應的 Hardened Image，以簡化升級流程 [^9921][^9922]。

Red Hat UBI 映像的更新節奏則緊密追隨 RHEL 的更新策略。Red Hat 會在每次 RHEL 發行新版本時提供對應版本的 UBI 映像；同時，若 RHEL 推出了重要的安全補丁（尤其是`嚴重級別的 CVE 漏洞修復`），官方也會重建並發布更新的 UBI 容器映像。這意味著 UBI 的更新頻率與 RHEL 保持同步：重大更新或安全修補能及時反映到映像中，但對於一般性的小幅更新，可能累積到定期發行或由使用者在容器內執行 `yum update` 來獲取。根據 Red Hat 的政策，{{ cg(body="UBI 容器映像至少會針對關鍵漏洞及時更新") }}，以確保使用者的基底映像不受高危安全缺陷的影響 [^9923]。相較之下，Docker Hardened Images 更側重於`主動且高頻率`的更新機制，而 UBI 映像受益於 RHEL 嚴謹的維護週期，`定期`推出穩定的更新。對開發團隊而言，如果追求最快的安全修補，DHI 的即時管線可能更具吸引力；但如果偏好有計畫的週期性更新與長週期支援，UBI 基於 RHEL 的維護模式也提供了可靠的預期。

### 支援情況

Docker Hardened Images 自 2025 年底起已開放免費使用，`免費版`對所有開發者一視同仁，但{{ cr(body="不包含正式的技術支援服務") }}。一般使用者可透過社群資源、論壇或 Docker 提供的文件獲取協助。對於有進階需求的企業，Docker 提供 `DHI Enterprise` 訂閱，包含{{ cg(body="SLA 保證的支援服務、客製化映像選項，以及符合合規需求的變體（例如支援 FIPS 加密或 STIG 基線的映像）") }}等。Enterprise 用戶還可購買{{ cg(body="延長生命周期支持（ELS），在上游停止維護後仍可獲得最多五年的安全更新") }} [^9919][^9921]。因此，選擇 DHI 的使用者可以根據自身情況決定免費使用社群支援，或付費升級以取得 Docker 官方的技術支援和更長的安全維護期限。

Red Hat UBI 容器映像對所有使用者都是{{ cg(body="免費可用且可重新發布的") }}，但其商業支援取決於運行環境。若將 UBI 映像部署在非 RedHat 平台上，使用者僅能仰賴「社群支援」或自行解決問題；但{{ cg(body="在 Red Hat Enterprise Linux 或 OpenShift 環境中執行 UBI，且組織擁有相應的訂閱時，這些映像將被納入 RedHat 官方支援範疇") }}，使用者可像使用其他 RHEL 產品一樣提出支援請求 [^9923]。換言之，UBI 本身不需授權費即可使用，但只有當您的部署環境在 Red Hat 生態系統內並符合訂閱條件時，才能享有 Red Hat 提供的技術支援服務。對於一般開發者或非訂閱用戶，UBI 的支援形式與社群維護的官方映像類似；而對企業客戶而言，使用 UBI 可讓您在 Red Hat 平台上獲得與使用商業 RHEL 相當的支援保障。相比之下，Docker Hardened Images 的支援模式更彈性獨立：Docker 自行提供企業支援方案，而不依賴特定部署平台。

### 授權與使用條款

Docker Hardened Images 的授權條款非常開放友好。正如前述，{{ cg(body="Docker 已將整套映像以 Apache 2.0 開源授權方式釋出。這意味著使用者在法律上擁有明確的權利來自由使用、修改、分發這些映像，而不會遇到隱性限制或未經聲明的約束") }} [^9921]。開源授權也利於社群共同參與改進；Docker 透過開放 Hardened Images，邀請社群審視其安全性並共同提高整體生態系統的容器安全水位。

Red Hat UBI 容器映像則受到專門的 EULA（最終用戶授權協議）約束。根據 Red Hat 公開資訊，{{ cg(body="UBI 基底映像「可在 Red Hat 或非 Red Hat 平台上自由部署，且允許自由重新散佈」") }}。也就是說，任何人都可以拉取 UBI 映像並用於構建公開或私有的容器，而無需支付授權費用或取得特別許可。此外，{{ cg(body="RedHat 明確允許獨立軟體供應商（ISV）和開源社群在 UBI 上構建衍生映像並對外提供") }}，這正是 UBI 設計的初衷之一 [^999]。不過，需要注意的是，UBI 本身雖免費，但其中包含的某些軟體元件仍採用各自的開源授權；同時，Red Hat 針對 UBI 的 EULA 也聲明了使用者在沒有訂閱的情況下並不享有任何保證或產品責任。總而言之，UBI 的授權模式介於商業與開源之間：`使用自由`且`重發佈友好` [^9911]，為推廣容器化應用提供了法律上的便利，但正式支援服務則另當別論，需要符合訂閱條件。

為方便閱覽，上表總結了 Docker Hardened Images、一般官方映像與 Red Hat UBI 在安全加固、更新、支援和授權等方面的比較：

| 比較項目 | **DockerHardenedImages (DHI)** | **原始來源官方映像** | **Red HatUBI 映像** |
| --- | --- | --- | --- |
| **維護者/基底** | Docker 官方維護；基於 Debian/Alpine 等開源發行版[^9921] | 上游專案團隊或廠商維護；基於各自選用的 OS (如 Debian、Alpine 等) | RedHat 維護；基於 RHEL 發行版內容（UBI 7/8/9 等對應 RHEL 版本）[^999] |
| **安全加固** | 極度精簡的映像（distroless），預設漏洞極少，減少最高可達 95%漏洞[^9921]；內建 SBOM、簽章與供應鏈證明[^9921] | 一般包含完整 OS 工具，潛在漏洞數較多[^9921]；安全性取決於上游習慣，缺少統一強化標準 | 繼承 RHEL 的安全強化與漏洞檢修機制[^9917]；提供微縮版（minimal/micro）映像以降低攻擊面[^9917]；有 STIG 強化變體供高安全需求選擇[^9911] |
| **更新頻率** | 持續整合上游更新，事件觸發自動重建（非常及時）[^9922]；Enterprise 版承諾<7日內修復關鍵漏洞[^9919] | 視上游專案而定；通常隨軟體版本發布或基底OS更新而更新（不一）；缺少統一定期策略 | 隨 RHEL 更新週期發布映像[^9923]；重大版本升級及嚴重CVE修補會及時提供新映像[^9923]；一般漏洞修補可由使用者透過 yum 更新 |
| **支援服務** | 免費版無官方支援（社群支持）；付費 Enterprise 版含 SLA 技術支援，可延長安全維護 5 年[^9919] | 缺乏正式支援，一般透過社群或上游廠商社區獲取幫助；大型廠商（如 Microsoft）可能有非正式支持管道 | 無需訂閱即可使用；在非 RedHat 平台上僅社群支援，在 RHEL/OpenShift 上搭配訂閱可獲 RedHat 官方支援[^9923] |
| **授權條款** | Apache 2.0 開源授權，無使用限制[^9921] | 依映像內軟體的各自授權而定，多為開源授權；整體無統一授權聲明，部分專案映像可能有自訂條款 | RedHat UBI EULA：自由使用與再分發[^9911]；無需授權費，但不承諾免費支援；訂閱用戶在RedHat環境可獲支援 |

## 安全優先的 .NET 應用容器化：最佳實踐與映像選擇

針對希望容器化 .NET 應用程式的使用者，在一般情境下以安全性為優先考量（且無特定合規要求）的情況下，以下是建議的最佳做法與可行選項：

首先，無論選擇何種基底映像，都應遵循一些基本的容器安全最佳實踐。例如，**使用多階段建構（multi-stage build）將應用程式編譯與運行環境分離：在建構階段使用完整的 .NET SDK 映像進行編譯，然後將產出發佈至精簡的運行時映像中（僅包含 .NET 執行所需組件）。**{{ cg(body="這種做法可大幅縮小最終映像的體積與攻擊面") }}。同時，務必定期更新基底映像版本，並對映像進行安全掃描，以及時發現並修補潛在漏洞。

在基底映像的選擇上，主要有以下幾種類型，各有優缺點：

1. **使用原廠提供的官方 .NET 映像**（例如 Microsoft 發布的 .NET 容器映像）：這是許多開發者的默認選擇。其優點是{{ cg(body="與 .NET 平台緊密整合") }}--映像由 Microsoft 官方維護，通常能同步 .NET 新版本的釋出並提供對應的映像更新。此外，{{ cg(body="官方映像提供多種版本（如基於 Debian的完整映像、Alpine 精簡版，以及 .NET8 開始提供的 *Chiseled* 無發行版映像）以滿足不同需求") }}。使用官方映像意味著享有廣泛社群測試的成果；大部分 .NET 範例和文檔也是基於這些映像編寫的。然而，其潛在缺點在於{{ cr(body="安全加固程度中等") }}：標準的官方映像往往包含一般作業系統環境，可能存在一些已知漏洞，需依賴 Microsoft 或社群定期更新。幸運的是，Microsoft 近年也意識到容器安全的重要，推出了體積更小、組件更少的 "Distroless/Chiseled" 映像，其不包含 shell 和套件管理器等不必要部分，大幅減少了潛在漏洞的數量 [^9913]。總的來說，若選擇官方映像，建議`偏好精簡變體`（如 Alpine 或 Chiseled 映像）來減少風險，同時透過自動化管道監控映像更新（例如 .NET 每月的更新版本）以套用安全修補。

2. **使用 Docker Hardened Images 作為 .NET 基底映像**：這是以安全為導向的全新選項。Docker 已將常用的 .NET 執行環境（涵蓋 .NET Runtime、ASP.NET 等）製作對應的 Hardened Images，讓開發者可以`直接替換`官方基底而獲得安全強化的好處。對於一般場景而言，採用 Hardened 映像的優勢在於{{ cg(body="開箱即有的低漏洞風險和供應鏈安全：映像精簡可靠，Docker 團隊持續為其提供更新，內建的 SBOM 與簽章也方便企業進行安全稽核") }}。{{ cg(body="這些映像以 Apache 2.0 授權提供，免費且無使用限制") }}，在商業項目中也可安心使用 [^9921]。此外，如果日後需求增長，還可以升級到 Docker 的企業版服務以獲得支援和更快速的修補。潛在的權衡在於，{{ cr(body="目前 Hardened Images 生態仍新，社群用量相對官方映像略少，因此在遭遇非常專案化的問題時，可參考的第三方經驗可能較有限") }}。不過，因為 Hardened 映像與原始映像保持高度相容（都是基於相同的開源發行版，只是在構建流程中強化了安全），實務上切換成本很低 [^995]。對於以安全優先且不想自行維護精簡映像的團隊，Docker Hardened Images 是一個省時有效的方案。

3. **使用 Red Hat UBI 基底映像來構建 .NET 容器**：如果團隊偏好 RedHat 提供的企業級環境，或計畫在 OpenShift 等平台上部署，UBI 映像也是可行選項之一。Red Hat 通過 UBI 提供了對 .NET Core/.NET 的官方容器支援（例如提供基於 UBI 的 .NET Runtime 和 SDK 映像，可在 RedHat 生態中取得認證）[^9910]。選擇 UBI 的優點在於其{{ cg(body="穩定性與合規性：UBI 映像由 RHEL 套件構成，享有長生命周期的更新支持和經過企業級測試的可靠性") }} [^999]。對於注重系統可靠和希望與企業現有 Red Hat 環境一致的團隊，UBI 提供了統一的基礎。此外，如果您的部署環境將運行於 RHEL/OpenShift，上述映像也能納入 Red Hat 技術支援範圍，獲得官方協助 [^9923]。需要權衡的地方在於{{ cr(body="映像規模與更新靈活性：UBI 基於完整的 RHEL 套件，映像可能比 Alpine/Distroless 等更大，包含的組件較多（不過可透過 UBI Minimal/Micro 緩解）") }}。同時，Red Hat 對映像的更新節奏以穩定為主，對最新的 .NET 版本支援可能略有延遲（通常 Red Hat 會在微軟發布新 LTS 版本後提供對應的映像/套件）。對於沒有 RedHat 訂閱的使用者，使用 UBI 在支援上與社群映像無異，需要自行關注漏洞修補；但對於已有 RedHat 基礎設施的團隊，使用 UBI 則能在統一管理的同時提升安全可信度。

**總結建議**：對於一般的 .NET 應用容器化，如果您的重心在於{{ cg(body="快速獲得高安全性") }}且不涉及特定合規架構，使用 Docker Hardened Images 或官方精簡映像（如 Microsoft 提供的 Distroless 版本）會是理想的選擇--它們能在不增加開發複雜度的情況下提供強大的安全預設 [^9913][^9919]。另一方面，若您的應用最終部署環境與 RedHat 息息相關（例如需要在 OpenShift 上運行，或考量日後取得 RedHat 支援），那麼基於 UBI 的映像則提供了在社群與企業支持之間取得平衡的途徑。**無論選擇哪一類映像，都應持續關注映像的安全公告，及時更新底層 .NET 執行環境版本，並遵循如非必要不以特權用戶執行容器等安全最佳實踐，來確保您的 .NET 應用容器既高效又安全。**[^9921][^9923]

</details>

[^1]: [Best practices: universal application images](https://developers.redhat.com/articles/2021/11/11/best-practices-building-images-pass-red-hat-container-certification)
[^4]: [(Re)Introducing the Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image)
[^5]: [9 Tips for Containerizing Your .NET Application - Docker](https://www.docker.com/blog/9-tips-for-containerizing-your-net-application/)
[^7]: [Docker Made Hardened Images Free -and That Quietly ...](https://medium.com/@mpetrellis_40400/docker-made-hardened-images-free-and-that-quietly-changes-container-security-45b56dae6eae)
[^9]: [A Comparison of Linux Container Images](https://crunchtools.com/comparison-linux-container-images/)
[^10]: [Hardening Container Images: Best Practices and Examples ...](https://medium.com/@SecurityArchitect/hardening-container-images-best-practices-and-examples-for-docker-e941263cab13)
[^11]: [What you need to know about Red Hat's .NET container ...](https://developers.redhat.com/articles/2025/12/01/what-you-need-know-about-red-hats-net-container-images)
[^12]: [Hardened Images Need Flexibility, Not Rigidity](https://www.docker.com/blog/hardened-image-best-practices/)
[^15]: [Making the Most of Your Docker Hardened Images Enterprise ...](https://www.docker.com/blog/making-the-most-of-your-docker-hardened-images-trial-part-1/)
[^16]: [Universal Base Images (UBI): A Comprehensive Analysis ...](https://medium.com/@tushant253/universal-base-images-ubi-a-comprehensive-analysis-and-practical-implications-473a4a00d613)
[^19]: [Docker Base Images Demystified: A Practical Guide from ...](https://medium.com/@tyutyutyu/docker-base-images-demystified-a-practical-guide-from-basics-to-best-practices-aea8e7d28d65)
[^20]: [Improving Container Security with Docker Hardened Images](https://dev.to/pradumnasaraf/improving-container-security-with-docker-hardened-images-3bia)
[^21]: [Introduce Red Hat Universal Base Image](https://pronteff.com/introducing-red-hat-universal-base-image/)
[^26]: [Red Hat Universal Base Image and Licensing](https://crunchtools.com/ubi-licensing/)
[^31]: [Red Hat Universal Base Image: RHEL containers for ...](https://www.zdnet.com/article/red-hat-universal-base-image-rhel-containers-for-everyone/)
[^57]: [Markdown guide and best practices to harden Docker images](https://github.com/sandbornm/HardenDocker)
[^58]: [Best practices for containerizing .net applications - Snyk](https://snyk.io/blog/best-practices-for-containerizing-net-applications/)
[^60]: [Hardened Images](https://www.docker.com/products/hardened-images/)
[^63]: [A Hacker's Guide To Moving Linux Services Into Containers](https://www.linkedin.com/pulse/hackers-guide-moving-linux-services-containers-scott-mccarty)
[^69]: [We made Docker Hardened Images free over a week ago](https://www.facebook.com/docker.run/posts/we-made-docker-hardened-images-free-over-a-week-ago-and-the-conversation-hasnt-s/1280243510805854/)
[^84]: [Docker just made hardened container images free and ...](https://www.reddit.com/r/devops/comments/1poxncf/docker_just_made_hardened_container_images_free/)
[^86]: [Docker Makes Hardened Images Free Open and ...](https://www.docker.com/press-release/docker-makes-hardened-images-free-open-and-transparent-for-everyone/)
[^87]: [Docker Hardened Images now free, devs give cautious ...](https://devclass.com/2025/12/18/docker-hardened-images-now-free-devs-give-cautious-welcome/)
[^88]: [Red Hat Universal Base Image](https://catalog.redhat.com/en/software/base-images)
[^89]: [Container results](https://catalog.redhat.com/search?searchType=containers)
[^90]: [redhat/ubi8 - Docker Image](https://hub.docker.com/r/redhat/ubi8)
[^91]: [Red Hat Universal Base Image 8](https://catalog.redhat.com/en/software/containers/ubi8/ubi/5c359854d70cc534b3a3784e)
[^101]: [Functionality difference between Redhat UBI 8and UBI 9](https://github.com/node-saml/passport-saml/discussions/866)
[^994]: [Explore | Docker Docs](https://docs.docker.com/dhi/explore/#:~:text=Docker%20Hardened%20Images%20,little%20to%20no%20retooling%20required)
[^995]: [Hardened Images | Docker](https://www.docker.com/products/hardened-images/#:~:text=%23%20%20Drop)
[^999]: [(Re)Introducing the Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image#:~:text=1,Red%20Hat%20Universal%20Base%20Image)
[^9910]: [.NET 8.0 Runtime Only on UBI 8 - Red Hat Ecosystem Catalog](https://catalog.redhat.com/en/software/containers/ubi8/dotnet-80-runtime/654156edc89ad23bf4b45e81#:~:text=Catalog%20catalog,NET%20Core%20applications)
[^9911]: [Red Hat Universal Base Image 9 with select STIG hardening - Red Hat Ecosystem Catalog](https://catalog.redhat.com/en/software/containers/ubi9/ubi-stig/68e7aca8a3801e04bcb7873b#:~:text=The%20Universal%20Base%20Image%20with,footprint%20and%20preventing%20unnecessary%20access)
[^9913]: [Announcing .NET Chiseled Containers - .NET Blog](https://devblogs.microsoft.com/dotnet/announcing-dotnet-chiseled-containers/#:~:text=In%20summary%2C%20Chiseled%20images%3A)
[^9917]: [Introduction to Red Hat's UBI Micro](https://www.redhat.com/en/blog/introduction-ubi-micro#:~:text=Constructing%20a%20container%20image%20without,and%20the%20same%20security%20metadata)
[^9919]: [Docker Makes Hardened Images Free Open and Transparent for Everyone | Docker](https://www.docker.com/press-release/docker-makes-hardened-images-free-open-and-transparent-for-everyone/#:~:text=%2A%20SLA,while%20maintaining%20trust%20and%20provenance)
[^9921]: [Docker Makes Hardened Images Free Open and Transparent for Everyone | Docker](https://www.docker.com/press-release/docker-makes-hardened-images-free-open-and-transparent-for-everyone/#:~:text=Docker%20Hardened%20Images%20reduce%20vulnerabilities,Every%20image%20includes)
[^9922]: [Hardened Images | Docker](https://www.docker.com/products/hardened-images/#:~:text=Drop,hardened%20images%20without%20breaking%20provenance)
[^9923]: [(Re)Introducing the Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image#:~:text=Consuming%20pre,and%20start%20building%20your%20application)
