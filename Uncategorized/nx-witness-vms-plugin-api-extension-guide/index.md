+++
title = "Nx Witness VMS 客製化開發指南：SDK、API 整合與跨平台部署完整實踐"
description = "深入 Nx Witness VMS 外掛程式開發技術，涵蓋 Metadata、Video Source、Storage 三大 C++ SDK 應用、RESTful API 整合、嵌入式網頁 UI 擴充，以及 Windows/Linux 環境下的完整建構與授權部署流程，助你打造高度客製化的影像管理系統。"
date = "2026-01-19T03:19:57Z"
updated = "2026-01-22T02:17:53.673Z"
draft = false

[taxonomies]
tags = ["Software Architecture", "Developer Tools"]
providers = [ "ChatGPT" ]

[extra]
withAI = "<https://chatgpt.com/share/696da2dd-1660-8005-825a-81412e438567>"
banner = "preview.png"

  [extra.preview]
  withAI = true
  url = "https://gemini.google.com/share/7ef232ff8eda"
  description = "Made with Nano Banana Pro by Gemini 3"
+++
{% chat(speaker="jim") %}
Our company plans to develop its own VMS based on NX. Please research and provide a comprehensive guide for developing or extending the Nx Witness VMS open-source project.
{% end %}
{% chat(speaker="chatgpt") %}
To help you best, could you please clarify a few points about your project:

1. What specific functionality are you aiming to develop or extend in the Nx Witness VMS? (e.g., custom analytics, UI plugins, device integration)
2. What is your development team's preferred programming language or stack?
3. Are you targeting specific operating systems or deployment environments?
4. Do you need guidance on licensing, SDKs, APIs, or build systems as well?

With this info, I can tailor the guide to your specific goals.
{% end %}
{% chat(speaker="jim") %}

1. UI plugins and new feature on client side or server side
2. Python or whatever
3. Windows or Linux
4. Yes
{% end %}
{% chat(speaker="chatgpt") %}
了解，我將為你整理一份完整的指南，說明如何基於 Nx Witness VMS 開發或擴充用戶端與伺服器端功能，包括 UI 外掛、功能模組開發流程、SDK 和 API 使用方式，以及在 Windows 與 Linux 環境下的建構與部署方法。
{% end %}

## 架構概述 (Nx Witness VMS 平台架構)

<figure>
{{ image(url="architecture.png", alt="架構概述資訊圖表") }}
<figcaption>Nx Witness VMS 採用 Client-Server Hive 架構，允許多伺服器與用戶端協同工作。</figcaption>
</figure>

Nx Witness VMS 採用獨特的 Client-Server Hive 架構，由伺服器端與多種用戶端組成[^41]。核心是 `Nx Media Server` 伺服器應用程式，負責裝置探索、影片錄製與資料管理；而桌面用戶端、行動裝置 app、Web 管理介面以及 Nx Cloud 雲端入口則提供直觀的 GUI 供操作人員存取與管理系統。一個 **Nx 系統 (System)** 可以包含多部伺服器（預設每部伺服器建議最多連接 128 路攝影機），各伺服器共同同步管理裝置清單、用戶和設定資料，形成 **叢集 (Hive)** 架構。伺服器主要任務包括：接收網路攝影機串流、錄影存檔、執行事件偵測與影片分析（如移動偵測）、維護用戶權限、處理事件及與硬體裝置（I/O 模組、門禁等）互動。用戶端則負責即時預覽、回放錄影，以及系統和攝影機管理。此外 Nx 提供 **Nx Cloud** 平臺（可選），用於遠端存取及多系統集中管理。整體而言，Nx Witness VMS 架構簡潔可擴充，允許開發人員透過伺服器外掛模組或開放 `API` 進行深度整合。

**平臺組成元件：**

- **Nx Media Server** （媒體伺服器）：核心服務程式（`Windows`/`Linux`），執行裝置管理、錄影、事件處理等功能[^41]。多個伺服器可組成 **叢集** ，共同管理同一系統的資源。
- **Nx Desktop 用戶端**：跨平臺（`Windows`/`Linux`/`macOS`）的圖形化用戶端程式，用於本地或遠端連線伺服器，直播預覽、影片回放與系統設定[^41]。Nx Desktop 內建高效的串流播放核心以及內嵌瀏覽器，可顯示 Web 頁面與第三方應用[^32]。
- **Nx Mobile 用戶端**：行動裝置 (`iOS`/`Android`) 用戶端應用，可即時觀看與推播通知。
- **Web 管理介面**：由 Nx 伺服器提供的 Web GUI，供管理者透過瀏覽器進行基本設定和 `API` 文件檢視[^22]。
- **Nx Cloud**：雲端服務，用於註冊系統、遠端穿透連線以及提供 `OAuth` 認證等功能，方便跨網域存取伺服器。

開發人員可針對上述架構的伺服器與用戶端部分進行擴充。 **伺服器端** 允許透過 **SDK 外掛模組** 增加新功能（例如接入新類型視訊源、自訂分析事件處理等）， **用戶端** 則能透過 **開放原始碼** 介面或嵌入式網頁來擴充 UI 與互動。下文將詳細說明各種擴充方法與工具。

## 用戶端 UI 擴充與外掛程式開發

<figure>
{{ image(url="client.png", alt="用戶端 UI 擴充與外掛程式開發資訊圖表") }}
<figcaption>用戶端 UI 擴充可以避免修改伺服器核心，加快開發速度，同時保持與官方用戶端版本的相容。</figcaption>
</figure>

Nx 提供多種方式擴充其 **桌面用戶端 UI** ，開發團隊可依需求選擇：

- **修改開源 Desktop 用戶端**：Network Optix 已開放 Nx Meta Desktop 用戶端原始碼 (`MPL-2.0` 授權)[^32]。開發者可從官方 GitHub 獲取 `nx_open` 專案，取得建置桌面用戶端所需的所有元件，用以 **定制專屬用戶端** 或整合第三方應用。由於 `MPL` 授權具有檔案層級的開源要求，開發者可修改 Nx Desktop 的特性並將其與其他專有程式碼結合發行，{{ cr(body="同時需開放修改過的部分原始碼") }}。這種方式適合需要 **深度客製 UI** 或增加新功能模組的場景。例如，可以修改用戶端介面以新增特定的控制面板、狀態監視視窗，或與自家系統進行更緊密的整合。

  {% alert(edit=true) %}
  MPL-2.0（Mozilla Public License 2.0）是一種 **弱 copyleft** 開源授權條款，設計目標是讓開源程式碼可以被商業使用，同時保證對原始檔案的修改仍然開放。

  可以自由使用、修改、散布、商業化 MPL 授權的程式碼。
  必須在散布時公開「修改過的原始檔案」，而且只限於那些檔案本身。
  不需要公開整個專案，未修改或自行新增的檔案可以保持閉源。
  這個授權是以「檔案」為邊界，不是以整個專案為單位，這是它與 GPL 最大的差異。
  {% end %}

- **嵌入式網頁與 JavaScript API**：自 Nx Witness 5.0 起，桌面用戶端內建 Chromium 瀏覽器元件，允許在佈局中嵌入 Web 應用頁面[^32]。Network Optix 提供 `JavaScript API`，使該嵌入頁面能與 Nx 桌面進行 **雙向互動** [^17]。開發者可將自訂功能做成 Web 前端應用（使用 `HTML`/`JS` 開發 UI），通過 Nx Desktop 載入該頁面，利用 `JavaScript API` 控制用戶端行為（如切換攝影機畫面、回放控制、取得系統資訊等）。這種 **UI 外掛程式** 形式不需要改動底層程式碼，即可在 Nx 介面內提供新功能面板。例如，可以開發一個網頁看板，在 Nx 桌面中顯示 AI 分析統計數據，並使用 `JS API` 觸發 Nx 的即時攝影機顯示或報警通知。

- **中繼資料外掛程式設定面板**：若開發 **伺服器端 `Metadata SDK` 外掛程式** （詳見下節），也可以在 Nx Desktop 中呈現該外掛程式的 **設定界面** 。`Metadata SDK` 允許開發者為外掛程式定義設定參數，Nx Desktop 會在系統設定中顯示對應的配置頁籤或對話框[^10]。開發者可以透過 `SDK` 將自訂參數、新事件類型等 **呈現在用戶端** ，方便使用者調整。例如，一個人臉辨識外掛程式可提供門禁名單上傳的設定介面，由 Nx Desktop 統一管理。這類設定 UI 可以是 Nx 預設窗體，也可以使用嵌入式網頁技術來呈現更複雜的介面。

**用戶端外掛程式部署** 方面，如果是 **修改版 Nx Desktop** ，需按照 Nx 開源專案提供的流程自行編譯，將生成的用戶端應用發給客戶安裝使用。若採用 **嵌入式網頁** 方案，只需將網頁伺服於用戶可存取的位置（本機或遠端），在 Nx Desktop 中新增對應的網頁部件即可；`JavaScript API` 透過 Nx Desktop 內建機制自動可用。對於 **`Metadata` 外掛程式設定面板** ，則外掛程式程式碼中定義配置 UI，部署外掛程式後 Nx Desktop 會自動識別並載入界面[^10]。整體而言，用戶端 UI 擴充可以避免修改伺服器核心，加快開發速度，同時保持與官方用戶端版本的相容。

## 伺服器端自訂模組開發 (事件、分析與整合)

<figure>
{{ image(url="server.png", alt="伺服器端自訂模組開發資訊圖表") }}
<figcaption>伺服器端自訂模組開發可透過 Nx 提供的多種 SDK 實現，滿足不同整合需求。</figcaption>
</figure>

為實現後端自訂功能，Nx 提供伺服器外掛程式 SDK（主要以 `C++` 開發）。開發人員可以撰寫 **動態連結庫** (`.dll`/`.so`) 外掛，放置於 Nx 伺服器的外掛程式目錄，即可在啟動時被載入執行[^12]。根據應用場景，官方提供三大類 `C++ SDK`：

- **`Metadata SDK`（中繼資料外掛程式 SDK）** - 用於整合第三方視訊分析或事件來源。透過 `Metadata SDK`，開發者可以建立安裝在 Nx 伺服器上的 **分析外掛程式** ，接收影像串流進行自動分析，並將 **目標物件偵測結果（含標註框、屬性等）** 作為元數據回饋給 Nx[^10]。Nx 會將這些物件匯入內部資料庫並在用戶端即時疊加顯示於畫面上。此外，中繼資料外掛程式能產生 **自訂事件** （例如「辨識到特定人物」等）和健康檢查事件（例如「分析服務斷線」），讓 Nx 的規則引擎可用於 **自動觸發聯動反應** 。`Metadata SDK` 適用於開發如人/車偵測、人臉辨識、行為分析等 **AI 外掛程式** 。例如，開發者可以基於 `OpenCV` 或 `OpenVINO` 建立物件偵測 DLL，在 Nx 中即時繪製辨識框並透過事件通知其他系統。

- **`Video Source SDK`（視訊來源整合 SDK）** - 讓開發者將非原生支援的攝影機或串流接入 Nx 系統[^10]。透過此 SDK，可以實作外掛程式以自訂攝影機發現、串流擷取與轉發錄影流程。例如，可將一種專有協議攝影機、第三方 `DVR`/`NVR`、行車紀錄器影片檔案，甚至虛擬來源（桌面擷取、串流轉碼）整合進 Nx，使其如同一般 ONVIF 攝影機般被系統管理與錄影。`Video Source SDK` 也支援自訂 **裝置 I/O** （輸入/輸出訊號）與 **移動偵測** 回傳，方便接入複合型裝置。實際案例包括整合 UAV 無人機攝影機串流、將外部 `RTSP` 串流源封裝成 Nx 攝影機，或實現 `Testcamera` 模擬相機外掛程式供測試使用。

- **`Storage SDK`（存儲外掛程式 SDK）** - 允許 Nx 支援自定存儲介質，如將錄影檔案寫入客製硬體或雲端空間[^10]。開發者實作 `Storage` 外掛程式時，需提供標準檔案存取介面（讀/寫串流、檢查檔案存在、刪除、列目錄等）[^4]。透過此 SDK，可以把錄影資料保存到 **非本機** 的位置，例如特殊的磁碟陣列設備、分散式文件系統或 `S3` 物件儲存。官方示例包括將 Nx 錄影直接存至 FTP 伺服器或 Wasabi 雲存儲等。`Storage` 外掛程式確保 Nx 的循環錄影、存檔管理機制對新介質透明運作。

這些外掛程式均以 `C++` 編寫，需繼承 Nx SDK 提供的抽象類別並實現所需介面函式[^15]。每個外掛程式編譯後是一個獨立的 `.dll`(Windows)或 `.so`(Linux)庫，放入 Nx 伺服器安裝目錄下的 `/mediaserver/bin/plugins/` 等指定資料夾中[^12]。Nx 伺服器啟動時會掃描並載入外掛程式，調用其初始化流程。 **外掛程式的生命週期** 通常包括：伺服器啟動載入 → 外掛程式執行(持續處理視訊或事件) → 伺服器關閉或外掛程式停用時卸載。{{ cr(body="開發時應遵循 Nx 提供的範例和框架，確保執行緒安全及異常處理，以避免影響伺服器穩定性") }}。

除了上述 SDK 方案，有些情況開發者可能選擇不直接寫 C++ 外掛程式，而使用 Nx 的 **外部 `API`** 實現 **鬆耦合整合** 。例如，如果只需將 **外部系統事件** 通知 Nx，可利用 **HTTP Generic Event API** 由第三方系統透過簡單的 HTTP POST 產生 Nx 內建的「Generic 事件」，再由 Nx 規則引擎觸發錄影或警報[^45]。又或者，開發者可撰寫獨立的 `Python`/`Node.js` **中介服務** ，通過輪詢或長連接監聽 Nx 事件(如 `WebSocket` 或周期查詢 API)，並與外部系統交換資訊。這種方式無需在 Nx 內部執行程式碼，降低部署風險，但實時性和整合深度略遜於原生外掛程式。

**小提示**：Network Optix 提供許多 **開源示例** 展示如何開發外掛程式與整合。官方的 **nx_open_integrations** GitHub 儲存庫中涵蓋多種 C++ 外掛程式範例（如 OpenCV 物件偵測分析外掛程式）、模擬攝影機外掛程式，以及使用 Python/Node.js 調用 API 的腳本範例[^3]。建議參考對應 Nx 版本的分支（如 vms_5.0、vms_5.1）取得相容的程式碼。這些範例對理解外掛程式架構、執行緒模型以及與 Nx 互動流程大有裨益。

## Nx Meta SDK 與相關 API 的應用

<figure>
{{ image(url="meta_sdk.png", alt="Nx Meta SDK 與相關 API 的應用資訊圖表") }}
<figcaption>Nx Meta SDK 與相關 API 可實現高度自訂的系統整合與自動化功能。</figcaption>
</figure>

除了 C++ 外掛程式，Nx 平台還提供多樣的 **API** 與 **工具庫** ，供開發人員以 **Python 或其他語言** 建置自訂功能。以下重點介紹 **Nx Meta Server HTTP API** 及相關開發工具：

- **Nx Server HTTP REST API** ：這是一組完整的 RESTful API，可透過 HTTP(S) 調用 Nx 伺服器的幾乎所有功能[^22]。透過 `Server API`，開發者可以 **查詢或管理系統資源** (伺服器、攝影機、用戶等)、修改使用者與權限、擷取即時或錄影串流、產生事件與建立自動化規則、控制雲台 (PTZ) 等。`API` 遵循 REST 標準，以 **HTTP GET/POST** 為主(新版本亦支援 PUT/PATCH/DELETE)。由於基於 HTTP，開發者可使用任意語言(Python、JavaScript、C# 等)發送請求與解析 JSON/XML 回應[^24]。要使用 `API`，需先通過 **身份驗證** 取得授權。Nx 採用 **Bearer Token** 驗證：用戶端首先調用 `/api/login`(或 `OAuth` 流程)取得會話權杖，後續請求在 Header 中附帶 `Authorization: Bearer <token>`[^34]。以 `Python` 為例，可使用 `requests` 函式庫進行登入並取得 token：

  ```python
  import requests
  nx_server = "http://<伺服器IP>:7001"
  # 1. 登入取得 Bearer Token
  resp = requests.post(f"{nx_server}/api/login",
        json={"username": "admin", "password": "你的密碼"})
  token = resp.json().get('token')
  # 2. 使用 token 調用 API 取得攝影機清單
  headers = {"Authorization": f"Bearer {token}"}
  resp = requests.get(f"{nx_server}/api/resource/camera", headers=headers)
  cameras = resp.json().get('devices', [])
  print(f"系統中共有 {len(cameras)} 支攝影機")
  ```

  透過上述 `API`，我們可以進一步實現許多自動化整合。例如，定時從 Nx 擷取截圖、將 Nx 偵測事件轉發至其他系統、批量新增/移除設備等。{{ cr(body="值得注意的是，新版 Nx API 已捨棄舊式 Digest 認證，強制使用 Token/OAuth 方式以提升安全性") }}[^34]。另外，針對已註冊 Nx Cloud 的系統，開發者可以利用 **Cloud Relay** 透過雲端代理訪問遠端的 Nx 伺服器 API。`Cloud Relay` 的使用方式是將本地 `API URL` 改為 `https://{systemId}.relay.vmsproxy.com/` 的格式並加上 Cloud 系統 ID[^25]。這使第三方服務無需直接存取內網 IP，也能安全地對 Nx 系統發送指令。

- **視訊串流與導出** ：利用 Nx Server API，可以 **擷取即時串流或錄影片段** 。Nx 伺服器內部實際使用 **`RTSP`/`RTP`** 傳輸影片給桌面用戶端[^35]。開發人員也可直接連線 Nx 伺服器的 RTSP 服務，以 `rtsp://<server_ip>:7001/<camera_id>` URL(加上必要驗證)存取串流。另方面，Nx API 提供 **`HLS` (HTTP Live Streaming)** 的擷取介面。例如，可以調用 `/api/hls/{cameraId}.m3u8` 取得 `M3U8` 播放清單，但{{ cr(body="需先取得 authKey 授權金鑰並附加於串流 URL 中") }}[^34]。{{ cr(body="新的 Nx 版本建議使用 API 登入取得 Bearer Token，再以 OAuth 方式交換串流授權，而不再支援在 URL 中直接帶明文帳密") }}。在 Web 環境中，如果希望低延遲播放，也可以採用 **`WebRTC`** 串流。Nx 5.1 引入 WebRTC 支援，官方提供了 npm 套件 `webrtc-stream-manager` 來簡化 `WebRTC` 串流的調用[^36]。總之，透過 Nx 的 `API`，開發者能以各種協定取得視訊：RTSP 適合原生應用或轉推、HLS 適合網頁播放，WebRTC 則提供即時互動方案。

- **事件處理與規則**：Nx 的事件管理相當靈活。開發人員可以透過 `API` 主動 **產生事件** ，如使用 `/api/createEvent` 或 `/api/rest/v{4}/events/generic` 介面，送出 **Generic 事件** 給 Nx[^21][^45]。`Generic` 事件可攜帶自訂名稱、訊息和中繼資料，Nx 收到後可依規則引擎設定執行響應（如觸發錄影、發送 Email、I/O 輸出等）。下例說明如何以 `Python` 發送一個名為 "Alarm" 的 Generic 事件：

  ```python
  url = f"{nx_server}/api/createEvent"
  payload = {
   "eventType": "nx.events.GenericEvent",
   "reason": "Alarm", 
   "comment": "第三方系統告警",
   "metadata": "<myTag>123</myTag>"
  }
  requests.post(url, headers=headers, json=payload)
  ```

  相反地， **訂閱 Nx 事件** 也可透過 `API` 完成。例如 `/api/wsEvents` 提供 WebSocket 介面，可即時推送系統事件給用戶端。或者定期查詢 `/api/events/last` 獲取最近的事件清單（可過濾事件類型）。此外，如果安裝了 `Metadata` 外掛程式，該外掛程式可產生 **Plugin 事件** 與 **物件元數據** 進入 Nx 事件流，開發者同樣可以通過 `API` 提取或處理。

- **Node.js 與其他語言工具庫**：對於偏好 `JavaScript`/`TypeScript` 或需要在瀏覽器環境開發的團隊，Network Optix 提供 **`Node.js Integration Library`** （Integration Framework）[^24]。它封裝了常用的 REST API 調用，方便快速建置與 Nx 的中介服務。亦有 **跨域請求** 相關輔助：{{ cr(body="由於瀏覽器的同源策略限制，直接從網頁呼叫 Nx API 會遇到 CORS 限制") }}，因此官方建議使用 **URL 型態的身份驗證** （例如在 URL 中附帶 `sessionToken` 或使用 proxy），或在 Nx 上設定允許的網域。除了 `Node.js`，`Python` 雖無專屬 SDK，但可直接使用 REST API 完成整合作業。`C#`/`.NET` 開發也常透過發送 HTTP 請求實現互動。總之，各語言皆可充分利用 Nx 的 API 能力，只需處理好 HTTP 請求和資料解析即可。

綜上，Nx Meta SDK/API 為開發者提供 **全方位的整合手段** 。無論是內嵌在 Nx 內部的高性能外掛程式，還是藉由 HTTP API 與 Nx 互聯的外部服務，都能達成對攝影機串流、事件、用戶管理等方面的定製。開發團隊應根據功能需求和技術特長，選擇適合的 SDK 或 API 路徑，在擴充功能的同時保持系統的穩定與安全。

## 開發環境與工作流程 (Windows / Linux)

<figure>
{{ image(url="develop_workflow.png", alt="開發環境與工作流程資訊圖表") }}
<figcaption>Nx 開發涉及 C++ 原生模組與跨平台建置，因此需要準備適當的工具鏈。</figcaption>
</figure>

**Nx 開發** 涉及 C++ 原生模組與跨平台建置，因此需要準備適當的工具鏈。以下是 Windows 與 Linux 下的環境配置與開發工作流程指南：

- **跨平台性與相依**：Nx 系統採用跨平台技術（主要由 `C++`/`Qt` 建置），官方開源的 Desktop 用戶端與 SDK 項目使用 `CMake` 進行編譯配置，並透過 `Conan` 套件管理器來統一相依性。據官方說明，目前支持的目標平臺包括 Windows 10 x64、Ubuntu 20.04/22.04/24.04 x64 及 ARM (交叉編譯) 和 macOS (x64 以及 Apple M1/M2)[^37]。開發人員可在熟悉的 OS 上開發，再針對部署目標做跨編譯或移植。

- **Windows 開發環境**：請安裝 **`Microsoft Visual Studio 2022`** (Community 版本即可) 作為 `C++` 編譯器與 IDE[^37]。安裝時選取「使用 C++ 的桌面開發」工作負載，以及「C++ CMake 工具」等組件。確保安裝 **`Git`** 用於抓取範例代碼、 **`Python 3.8+`** 及 **`pip`** （用於執行建置腳本）。從 Nx 官方資源（如 Nx Meta Developer Portal 或 GitHub）下載 **開發者 SDK 壓縮包** 或克隆 **`nx_open`** 儲存庫原始碼。通常在 Windows 上，Network Optix 提供了預先配置的 CMake 腳本與 build.bat 來簡化建置。您可在 VS 開發者命令提示字元中執行 `build.bat`，它將自動透過 Conan 下載所需相依（如 Qt 庫、FFmpeg 等），然後調用 CMake 生成 VS 解決方案並編譯。根據官方提供的 **`build.md`** 指南，整個流程已高度自動化，只需耐心等待依賴下載與編譯完成。編譯成功後，將產生 Nx Desktop 用戶端可執行檔，或對於外掛程式專案則生成 `.dll` 檔。開發期間可使用 Visual Studio 進行程式碼編輯、偵錯，Network Optix 亦提供如 **`launch.vs.json`** 等設定方便在 VS 中調試外掛程式。{{ cr(body="請注意，若開發 Metadata 外掛程式且使用 OpenVINO，需安裝相應版本的 OpenVINO SDK 並設定環境變數如 InferenceEngine_DIR 指向其路徑") }}。官方文件建議使用 Nx 支援的 `OpenVINO LTS` 版本，以避免相容性問題。

- **Linux 開發環境**：以 **`Ubuntu 22.04`** 為例，可使用系統附帶的 `GCC` 或 `Clang` 作為編譯器。不過 Nx 開源專案的特殊之處在於：其 CMake 腳本能透過 Conan 自動下載特定版本的編譯器及依賴，以保證不同環境下一致的編譯結果[^37]。開發者需安裝 **`Python 3`** 與 **`pip`** ，然後使用 pip 安裝 **`CMake`** 、 **`Ninja`** 、 **`Conan`** 等工具（建議依照 `requirements.txt` 指定的版本安裝，以符合官方支援版本)。準備就緒後，執行儲存庫中的 `build.sh` 腳本或自行以 `CMake` 命令配置專案。例如：

  ```bash
  git clone https://github.com/networkoptix/nx_open.git
  cd nx_open
  pip install -r requirements.txt # 安裝指定版本的 cmake, conan 等
  ./build.sh --target DesktopClient
  ```
  
  執行上述步驟將自動解決相依並開始編譯 Desktop 用戶端。Linux 下編譯出的外掛程式為 `.so` 檔。開發人員可使用熟悉的編輯器與 GDB 等工具進行開發調試。若需調試外掛程式，可啟動 Nx Media Server 並附加調試 `.so`。Linux 下注意安裝 **開發套件** （例如 Qt 開發庫、OpenSSL、FFmpeg 開發頭文件等）；不過正如前述，Nx 的 Conan 配置會自行抓取必要的 libraries，如 Qt 5.15 等，因此本地無需手動滿足過多依賴[^37]。另外，如果目標是 ARM 平台（如 NVIDIA Jetson），可在 x64 主機上利用 Nx 提供的交叉編譯 Conan profile 完成編譯，再將產物部署到 ARM 裝置運行。

- **SDK 獲取與配置**：Network Optix 提供預編譯的 **外掛程式 SDK 開發包** （通常在 Nx Meta Server 的 Web Admin「開發工具」處可直接下載[^4]）。下載後其中包含對應版本的頭文件、庫檔（`lib` 或 `dll`）、示例程式碼等。開發者可將 `include` 路徑指向該 SDK 目錄，在 `CMake` 專案中連結 Nx 提供的 SDK 庫。例如，一個 `Metadata` 外掛程式專案的 `CMakeLists.txt` 需要找到 `NX_SDK_DIR` 然後連結 `libnxsdk.a` 或類似庫。通常官方範例的建置腳本已有這些邏輯，開發者可據此修改。{{ cr(body="版本相容性需特別注意：外掛程式必須使用與目標 Nx Server 相同版本的 SDK 建置") }}。例如，用 5.1.0 SDK 編譯的外掛程式應在 Nx Witness 5.1.0 下運行，以免介面不匹配。{{ cr(body="隨 Nx 版本升級，請關注 SDK 變更日誌，若有 API 變動需更新外掛程式程式碼") }}。官方的 `nx_open_integrations` 專案依不同 Nx 版本建立了分支，正是出於這種版本相容性的考量。

- **開發流程**：在完成編譯配置後，推薦的外掛程式開發流程一般是：撰寫或改造範例代碼 → 本地編譯產出外掛程式 → 將外掛程式檔案置於 Nx Server plugins 資料夾 → 啟動 Nx Server 測試外掛程式功能。可利用 Nx Server 日誌檔案（位於安裝目錄下 `Logs/`）檢視外掛程式載入情況和輸出訊息。如需更深入的偵錯，可使用 Visual Studio 或 GDB **附加到 `mediaserver` 進程** 來下斷點調試外掛程式程式碼[^7]。Nx 也提供 **Plugin Debugging** 指引，建議在調試時使用 **Stub** 外掛程式（空殼外掛程式）進行初步驗證[^12]。整體來說，外掛程式開發雖涉及原生編譯，但在官方文檔與範例輔助下，遵循步驟即可順利建置。

## 建置與部署流程

<figure>
{{ image(url="deploy_workflow.png", alt="建置與部署流程資訊圖表") }}
<figcaption>建置與部署流程涵蓋從開發、測試到最終上線的各個階段。</figcaption>
</figure>

無論是用戶端 UI 擴充還是伺服器外掛程式，都需要考慮 **最終部署** 在目標系統上的步驟：

- **伺服器外掛程式部署**：開發完成並測試通過後，需將編譯出的外掛程式庫部署到用戶的 Nx Witness 系統。部署方式是把 .dll（Windows）或 .so（Linux）檔案放入 Nx Media Server 安裝路徑下的 plugins 資料夾[^12]。通常該資料夾預設為：Windows 在 `%ProgramFiles%/Network Optix/Network Optix Mediaserver/plugins/`，Linux 在 `/opt/networkoptix/mediaserver/bin/plugins/`。重新啟動 Nx Server 後，新外掛程式會自動被載入。可在 Nx Desktop 的「系統設定 > 廣播消息」中觀察是否有外掛程式載入失敗的錯誤提示，或查看伺服器日誌確認外掛程式已啟動。如需移除外掛程式，只要刪除該檔案並重啟服務即可。{{ cr(body="版本兼容是部署時需要注意的：外掛程式檔名通常包含適用版本，例如 libmetadata_plugin_5.1.so，以提醒使用者需在對應版本的 Nx 上使用") }}。

- **用戶端 UI 部署**：對於 **嵌入式網頁 UI** 類的擴充，部署涉及兩部分：一是在 Nx Desktop 佈局中新增一個「Web Page」元素，指向您的應用 URL；二是確保該 URL 可被用戶端存取。如果網頁托管於外部伺服器，需要用戶端電腦能連上該網址（若有驗證也要同步配置）。如果是簡單頁面，也可以將 `HTML`/`JS` 靜態文件放在本機，使用 `file://` 或開設一個本機 HTTP 服務。{{ cr(body="注意 Nx Desktop 嵌入頁面默認禁止某些不安全內容（如彈出窗口），並且受瀏覽器同源策略限制") }}；{{ cr(body="如需呼叫 Nx API，務必採用 Token 認證或在 Nx Server 啟用對應網域的 CORS 設置") }}[^24]。對 **開源定制的 Nx Desktop** 來說，部署就是用您編譯出的安裝包（或執行檔）替換官方版本給用戶安裝。Network Optix 提供 Customization Kit 允許更換 Logo、名稱等品牌元素，因此您可分發屬於自己公司的 **定制用戶端** 。{{ cr(body="請遵守 MPL-2.0 授權要求，提供修改過的原始碼供受眾索取") }}[^32]。

- **升級與維護**：{{ cr(body="隨著 Nx Witness 官方版本更新，您的外掛程式或擴充可能需要重新編譯甚至代碼調整才能相容新版") }}。建議密切關注 Nx 開發者公告。為方便維護，您可以在 **版本表** 中紀錄外掛程式與 Nx 的相容關係。例如：「Plugin v1.0 支援 Nx 5.0~5.1；Plugin v1.1 支援 Nx 5.2」等，以免用戶在不相容的版本上載入導致崩潰。當 Nx 有重大升級（例如架構變動或 API 廢棄），務必及時更新您的擴充並通知使用者升級。此外，充分利用 Nx 提供的 **日誌與診斷** 工具：Nx Desktop 自帶日志檢視器，可開啟 Client Logs 或 Server Logs 協助分析問題[^37]。在外掛程式內也應寫入關鍵日志（使用 Nx SDK 的 `logging` 介面或直接輸出）以便排查現網環境的異常情況。

總而言之，Nx 的建置與部署遵循標準軟體流程。透過嚴謹的版本管理與充分的測試，可以確保自訂功能順利融入 Nx 平台，在 Windows 與 Linux 系統上可靠運行。

## 授權模式與擴充發行考量

<figure>
{{ image(url="license.png", alt="授權模式與擴充發行考量資訊圖表") }}
<figcaption>了解 Nx Witness 的授權模式與相容性政策，有助於順利發行擴充功能。</figcaption>
</figure>

在擴充 Nx Witness 系統時，需了解其 **授權機制** 與相容性政策，以確保遵循軟體使用條款並滿足商業部署需求：

- **Nx Witness 鏡頭授權**：Nx VMS 採取 **按攝影機通道授權** 的模式。{{ cr(body="任何攝影機串流接入 Nx 系統後，若需要錄影存檔，必須為其配置有效授權 (License)") }}[^46]。每一授權對應一個錄影通道，可是 IP 攝影機、`RTSP` 串流或 HTTP 串流等任一來源。{{ cr(body="未授權的裝置僅能即時預覽，無法錄影存檔") }}。系統預設提供 4 組試用授權，有效期 30 天，方便用戶初始測試。{{ cr(body="超過試用期或需額外攝影機時，需向 Nx 經銷商購買永久授權") }}。不同授權型別針對不同場景，例如 **Professional** （標準版）、 **Edge** （ARM 嵌入式設備用）、編碼器通道授權，以及視頻牆、物聯網模組等特殊授權。開發團隊在建置自己的解決方案時，應預估系統會使用多少攝影機，並確保最終用戶購買足夠的 Nx 授權。若您是基於 Nx Meta 平臺開發全新產品（Powered-by-Nx），可與 Network Optix 洽談取得 **自定授權** 管理方案，以販售您品牌的授權。擴充功能本身通常不需額外授權費，但{{ cr(body="不能繞過或違反 Nx 的攝影機授權規則") }}。

- **開發者授權與 Nx Meta**：Network Optix 了解開發測試需求，為此在 Nx Meta 平臺提供開發者授權。首次安裝 Nx Meta 時，可立即啟用一組免費的開發授權：最多 4 台裝置，30 天有效[^42]。這與 Nx Witness 內建的試用授權本質相同，但開發者可反覆使用（例如 Nx Meta 的試用可重置或聯絡官方延長）。建議使用 Nx Meta 作為研發和試驗環境，而{{ cr(body="不要將 Nx Meta 用於正式生產") }}[^31]。待開發完成後，在正式 Nx Witness 系統上部署時，再為客戶配置正式授權。這樣可避免測試期間授權不足的限制。

- **MPL 開源授權義務**：如前文所述，Nx Desktop 用戶端和部分 Nx Meta 元件是以 **`Mozilla Public License 2.0`** 授權開源[^32]。`MPL` 屬於較寬鬆的 Copyleft 授權，允許您將開源碼與自有程式碼混合發行，但{{ cr(body="要求對開源部分的任何修改都要開放") }}。因此，{{ cr(body="如果您直接修改了 Nx 開源碼（例如更改了 Desktop 用戶端的部分代碼以添加新 UI），則您有義務在分發您的軟體時一併提供修改後的原始碼或提供取得原始碼的方法，以符合法律要求") }}。若您開發的是 **獨立外掛程式** 或 **利用 API 的外部應用** ，一般不需開放自身程式碼，因為您並未修改 Nx 開源組件本身。然而，建議在產品文檔中注明使用了 Nx 開源元件並附上 `MPL-2.0` 授權文本以及原始碼連結，以示尊重開源協議。Network Optix 官方亦提供開源聲明文檔，列出 Nx 所利用的第三方開源庫及其許可[^37]。遵循這些條款有助於建立良好開發者聲譽。

- **相容性與品牌**：當您對 Nx 進行擴充後，應關注與原系統其他部分的相容。例如，用戶端外掛程式需確保不與 Nx Desktop 的熱鍵、佈局等衝突；伺服器外掛程式要避免佔用過多資源影響錄影性能。每次 Nx 官方升級大版本時，請驗證您的擴充功能是否仍正常。如果您的解決方案包含重新包裝 Nx 軟體（即 Powered-by-Nx 產品），Network Optix 通常要求加入其 **技術夥伴計畫** 。通過該計畫，您可以取得最新的 Nx Meta SDK、技術支援，以及使用 Nx Cloud 授權系統為您的客戶創建和管理授權[^31]。同時您也能使用「Powered by Nx」標誌，向用戶保證您的產品與 Nx 平臺完全兼容且穩定。{{ cr(body="切勿嘗試修改 Nx 核心的閉源部分或繞過授權驗證，這將違反 EULA 甚至導致系統不穩") }}。官方建議保持 Nx Server 核心不變，所有擴充都通過公開 SDK/API 進行，以確保升級兼容與技術支援的有效性[^45]。

總的來說，在 Nx Witness 平臺上開發擴充功能既要創新也要合規。理解授權模型能幫助您合理評估成本、設定商業模式；遵守開源與 EULA 規範則確保您的產品長遠穩健發展。透過正確運用 Nx 提供的開放資源並遵循其授權政策，您將能順利打造出自有特色且與 Nx 生態無縫銜接的影像管理解決方案。

## Nx 擴充開發工具一覽表

下表總結了 Nx Witness 平臺可用的 SDK、API 與其特性，方便開發團隊快速參照各項工具的語言、用途及支持平臺：

| **工具 / SDK / API** | **用途與功能** | **開發語言** | **運行環境** |
| --- | --- | --- | --- |
| **`Metadata SDK`** （中繼資料 SDK） | 開發伺服器端分析外掛程式：處理視訊流、產生物件中繼資料與事件[^10]。適用 AI 視覺分析、客製事件。 | `C++` (`Qt`) | Nx Media Server 外掛程式 |
| **`Video Source SDK`** （視訊源 SDK） | 整合自訂攝影機/串流：實現新協議設備、自定發現與錄影流程[^10]。 | `C++` | Nx Media Server 外掛程式 |
| **`Storage SDK`** （存儲 SDK） | 擴充存儲介質：對接 NAS、雲存儲或特殊硬體[^10]。實現自定錄影存取。 | `C++` | Nx Media Server 外掛程式 |
| **Nx Server REST API** | 控制與查詢 Nx 系統：管理資源、用戶、觸發事件、擷取影音[^22]。標準 HTTP 介面，語言無關。 | 任意 | 外部應用或腳本透過 HTTP 調用 |
| **Nx Web JS API** (`JavaScript`) | 嵌入式網頁與桌面端互動：在 Nx Desktop 開啟網頁並使用 JS 控制用戶端（如切換鏡頭）。需要 Nx Desktop 5.0+ 內建瀏覽器支持[^17]。 | `JavaScript` | Nx Desktop 內嵌網頁 |
| **`Node.js Integration Library`** | 官方提供的 Node.js 框架：快速建置中間件，調用 Nx REST API 與 Nx Cloud API[^24]。適合在獨立服務器上跑整合邏輯。 | `JavaScript` (`Node.js`) | 外部服務 (中間層或雲端) |
| **Nx Cloud API (`OAuth2`)** | Nx 雲端介面：透過 Nx Cloud 訪問系統清單、用戶、以及使用 Cloud Relay 呼叫現場 API[^25]。用於多站點管理與遠端訪問。 | 任意 (通過 HTTP/`OAuth`) | 外部應用 (需 Nx Cloud 帳號) |
| **Nx Desktop Open Source** | 桌面用戶端完整原始碼：可自訂 UI/功能並重編譯發布自有用戶端[^32]。遵循 MPL 授權開源。 | `C++`/`Qt` (`QML`) | 獨立 Desktop 應用 |
| **Nx Open Integration Examples** | 開源範例合集：涵蓋 C++ 外掛程式（分析、設備模擬）、Python 腳本（事件、認證）等[^3]。用作開發參考模板。 | `C++`, `Python`, `JS` 等 | - |

*表：Nx Witness 開放 SDK 與 API 摘要。[^10][^22]等*

上述表格列出各種開發選項，團隊可依據自身技能與項目需求選擇使用。例如，需要深度影像分析且高效處理，可選擇 `C++ Metadata SDK` 外掛程式；若只是簡單將現有系統事件連接 Nx，或做一個監控板，或許透過 `REST API` 搭配 `Python` 腳本即可實現。透過這份指南和工具對照，開發團隊將能有效地在 Nx Witness 平臺上打造出定制的功能與外掛程式，滿足用戶端與伺服端雙方面的擴充需求。

**參考資料：**

- **Nx Toolkit 開發者頁面** - 提供 Nx 系列 SDK/API簡介[^10]。
- **Nx Meta 開放整合 GitHub** - Nx Meta 平臺的開源整合範例與原始碼（MPL-2.0）[^3][^32]。
- **HTTP REST API 開發文檔** - Nx Server API 功能與使用說明[^22]。
- **外掛程式SDK說明** - 中繼資料、視訊源、存儲等服務端 SDK 介紹[^4]。
- **Wisenet WAVE 使用手冊** - Nx Witness OEM 版本用戶手冊，涵蓋系統架構與授權資訊[^41][^46]。
- **Nx Desktop Open Source 公告** - Nx Desktop 開源新聞稿，解釋開源範疇與 MPL 授權[^32]。
- Network Optix 支援知識庫與開發者論壇 - 外掛程式開發 Q&A、Cloud Relay 使用方式等[^25][^45]。

以上連結內容詳載了本文提及的技術要點，可供延伸閱讀與實作參考。透過充分利用這些資源，您的團隊將能順利地為 Nx Witness VMS 開發出功能強大且量身定制的外掛程式與 UI 擴充。祝開發過程順利！

{% alert(edit=true) %}
下一步： 在 [Nx Meta 開發者入口網站](https://meta.nxvms.com/) 註冊帳號，探索官方開發者資源！
{% end %}

[^3]: [GitHub - networkoptix/nx_open_integrations: Network Optix Meta Video Management Platform open source integration examples - the source code and specifications which show how to integrate a third-party solution with the Nx Meta Video Management Platform (VMP) and thus all Powered-By-Nx products, including Nx Video Management System (VMS).](https://github.com/networkoptix/nx_open_integrations#:~:text=Introduction)
[^4]: [The Server Plugin SDK (C++) - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32766542038423-The-Server-Plugin-SDK-C#:~:text=Storage%20SDK)
[^7]: [Request for Detailed Guidance on Developing and Deploying a Plugin for Nx Witness Client on Windows - Network Optix](https://support.networkoptix.com/hc/en-us/community/posts/35952138929431-Request-for-Detailed-Guidance-on-Developing-and-Deploying-a-Plugin-for-Nx-Witness-Client-on-Windows#:~:text=3.%20Plugin%20Deployment%20,the%20plugin%20does%20not%20load)
[^10]: [Nx Toolkit | Network Optix](https://www.networkoptix.com/developers/nx-toolkit#:~:text=,reactions%20in%20the%20Rules%20Engine)
[^12]: [2. Integration Architecture - Understanding Plugins - Nx Meta](https://meta.nxvms.com/docs/developers/knowledgebase/225-2-integration-architecture---understanding-plugins#:~:text=2.%20Integration%20Architecture%20,Plugin%20implements%20several%20abstract%20classes)
[^15]: [2. Integration Architecture - Understanding Plugins - Nx Meta](https://meta.nxvms.com/docs/developers/knowledgebase/225-2-integration-architecture---understanding-plugins#:~:text=Meta%20meta,Plugin%20implements%20several%20abstract%20classes)
[^17]: [Introduction to the JavaScript API - Nx Meta](https://meta.nxvms.com/docs/developers/knowledgebase/325-javascript-api-introduction#:~:text=Nx%20Meta%20Desktop%205,webpage%20and%20use%20it)
[^21]: [Generic Events Generator in the Nx Server API - Nx Developer Tools](https://www.youtube.com/watch?v=80Z_d1bv-7Y#:~:text=Generic%20Events%20Generator%20in%20the,In%20this%20video)
[^22]: [The HTTP REST API - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32766279894679-The-HTTP-REST-API#:~:text=If%20you%20have%20the%20latest,the%20bottom%20of%20the%20page)
[^24]: [Overview of Supported Programming Languages - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32765731311383-Overview-of-Supported-Programming-Languages#:~:text=Image%3A%20image6)
[^25]: [Route API Calls via Nx Cloud - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32766609665687-Route-API-Calls-via-Nx-Cloud#:~:text=When%20sending%20an%20API%20call,a%20result%2C%20you%27ll%20need%20to)
[^31]: [Link a Meta installation to NX Witness - Network Optix](https://support.networkoptix.com/hc/en-us/community/posts/1500001366642-Link-a-Meta-installation-to-NX-Witness?page=1#:~:text=Link%20a%20Meta%20installation%20to,system%20in%20a%20production%20environment)
[^32]: [Nx Desktop is now Open Source.](https://www.networkoptix.com/blog/nx-desktop-open-source#:~:text=The%20Nx%20Meta%20Desktop%20application,awareness%20management%20interface%20out%20there)
[^34]: [How NxWitness generate the auth key? - Network Optix](https://support.networkoptix.com/hc/en-us/community/posts/29031803458967-How-NxWitness-generate-the-auth-key#:~:text=Hi%20Lim%2C)
[^35]: [How are Server RTSP Streams processed? - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32920579822743-How-are-Server-RTSP-Streams-processed#:~:text=The%20server%20streams%20video%20with,in%20accordance%20with%20ONVIF%20format)
[^36]: [How to Play back videos using WebRTC - Network Optix](https://support.networkoptix.com/hc/en-us/articles/32894602219671-How-to-Play-back-videos-using-WebRTC#:~:text=Optix%20support,1%20or%20later)
[^37]: [GitHub - networkoptix/nx_open: NetworkOptix open-source components used to build Powered-by-Nx products including Desktop Client for Network Optix Video Management Platform.](https://github.com/networkoptix/nx_open#:~:text=Supported%20target%20platforms%20and%20architectures%3A)
[^41]: [Wisenet WAVE User Manual](https://hanwhavision.eu/wp-content/uploads/2023/01/Wisenet-WAVE-4.1-Manual.pdf#:~:text=Wisenet%20WAVE%20has%20a%20unique,%E2%80%A2%20Wisenet%20WAVE%20Cloud%20Admin)
[^42]: [Get a License for Developers - Nx Meta](https://meta.nxvms.com/docs/developers/knowledgebase/208-getting-licenses-for-developers#:~:text=When%20first%20installing%20Nx%20Meta%2C,the%20System%20Administration%20dialog)
[^45]: [Integration with Nx Meta for Metadata Event Transmission - Network Optix](https://support.networkoptix.com/hc/en-us/community/posts/28042665219479-Integration-with-Nx-Meta-for-Metadata-Event-Transmission#:~:text=The%20API%20document%20is%20also,some%20articles%20are%20available%20here)
[^46]: [Wisenet WAVE User Manual](https://hanwhavision.eu/wp-content/uploads/2023/01/Wisenet-WAVE-4.1-Manual.pdf#:~:text=Wisenet%20WAVE%20Licenses%20Video%20from,One)
