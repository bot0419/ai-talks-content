+++
title = "Shortcode 示範頁面"
description = "展示各種 shortcode 功能，包含聊天對話框、編按、顏色標記等實用元件"
date = "2025-07-23T18:30:19.062Z"
updated = "2025-07-23T19:07:18.316Z"
draft = true

[taxonomies]
tags = [ ]

+++

這個頁面展示各種自訂 shortcode 功能，讓你了解如何在文章中使用這些實用的元件。

## 編按 (Editorial Note)

「編按」shortcode 用於顯示編輯者的補充說明或備註，適合用於資料更新說明、版本變更註記等。

### 基本使用

```markdown
{%/* alert(edit=true) */%}
編輯者的備註內容
{%/* end */%}
```

### 展示範例

{% alert(edit=true) %}
這篇文章是根據 2024 年的資料整理而成，部分數據可能已有所變動，請以最新官方資訊為準。
{% end %}

{% alert(edit=true) %}
本段落的程式碼範例已更新至最新版本的 API，舊版本使用者請注意相容性問題。
{% end %}

{% alert(edit=true) %}
編按也支援 **Markdown** 格式，包含：

- 列表項目
- *斜體文字*
- `程式碼片段`
- [連結](https://example.com)

這讓編輯說明更加豐富和實用。
{% end %}

## 聊天對話框 (Chat Dialogue)

聊天對話框 shortcode 用於顯示與各種 AI 工具的對話過程，提供清楚的說話者識別和美觀的訊息氣泡。

### 使用語法

```markdown
{%/* chat(speaker="chatgpt") */%}
對話內容
{%/* end */%}
```

### 與 ChatGPT 的對話

{% chat(speaker="user") %} 你好！請介紹一下什麼是機器學習？ {% end %}

{% chat(speaker="chatgpt") %} 你好！機器學習是人工智慧的一個重要分支，它讓電腦系統能夠自動從數據中學習和改進，而無需明確的程式設計。

簡單來說，機器學習就是讓電腦通過分析大量數據來識別模式，並利用這些模式對新數據進行預測或決策。

主要類型包括：

- 監督式學習：使用標註數據進行訓練
- 非監督式學習：從未標註數據中發現隱藏模式
- 強化學習：通過與環境互動來學習最佳策略 {% end %}
{% chat(speaker="user") %} 能舉個具體的例子嗎？ {% end %}

{% chat(speaker="chatgpt") %} 當然！以電子郵件垃圾郵件過濾為例：

- 數據收集：收集大量已分類的郵件（垃圾郵件和正常郵件）
- 特徵提取：分析郵件內容，如關鍵字頻率、發送者信息等
- 模型訓練：讓演算法學習垃圾郵件的特徵模式
- 預測應用：對新郵件進行自動分類

這樣系統就能自動識別並過濾垃圾郵件，準確率會隨著處理更多郵件而持續改善。 {% end %}

## 多種 AI 工具對話

### Claude 的回應

{% chat(speaker="claude") %} 補充一下機器學習的實際應用領域：

🎯 商業應用

- 推薦系統（Netflix、Amazon）
- 搜尋引擎優化
- 欺詐檢測

🏥 醫療健康

- 醫學影像診斷
- 藥物研發
- 個人化治療

🚗 交通運輸

- 自動駕駛汽車
- 路線規劃優化
- 交通流量管理

機器學習正在改變我們生活的各個層面！ {% end %}

### Gemini 的觀點

{% chat(speaker="gemini") %} 從技術發展角度來看，機器學習還有幾個重要趨勢：

✨ 深度學習：多層神經網路模擬人腦處理信息 🔄 遷移學習：將已訓練模型應用到新任務 🤖 自動機器學習：自動化模型選擇和調優過程 🌐 聯邦學習：在保護隱私的同時進行分散式學習

這些技術讓 AI 變得更強大、更實用，同時也更容易部署。 {% end %}

### GitHub Copilot 的程式範例

{% chat(speaker="copilot") %} 讓我提供一個簡單的 Python 機器學習範例：

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

# 載入數據

X, y = load_your_data()

# 分割訓練和測試集

X_train, X_test, y_train, y_test = train_test_split(
   X, y, test_size=0.2, random_state=42
)

# 建立並訓練模型

model = LogisticRegression()
model.fit(X_train, y_train)

# 進行預測

predictions = model.predict(X_test)

# 評估準確率

accuracy = accuracy_score(y_test, predictions)
print(f"模型準確率: {accuracy:.2f}")
```

這個範例展示了機器學習的基本流程：數據準備、模型訓練、預測和評估。
{% end %}

### 使用者與作者的對話

{% chat(speaker="user") %}
@jim 這個 chat shortcode 功能很實用！可以展示不同的對話情境。請問實作上有什麼技術細節嗎？
{% end %}

{% chat(speaker="jim") %} 很高興你喜歡這個功能！這個 chat shortcode 的實作重點包括：

## 技術架構

- 使用 Zola 的 shortcode 系統
- Tera 範本引擎處理邏輯
- SCSS 模組化樣式設計
- SVG 向量圖示作為頭像

## 設計特色

- 響應式布局，支援手機和桌面
- 明暗主題自動適配
- 無障礙設計（ARIA 標籤、語義化 HTML）
- 不同說話者的視覺區分（左右對齊、顏色差異）

## 使用方式

```
{% chat(speaker="chatgpt") %}
你的訊息內容
{% end %}
```

支援的說話者：chatgpt、claude、gemini、copilot、user、jim {% end %}

{% chat(speaker="user") %} 這個設計很貼心！特別是響應式和無障礙設計的考量。 {% end %}

### 長對話測試

{% chat(speaker="user") %} 讓我測試一下比較長的訊息會如何顯示。

在現代網頁開發中，響應式設計已經成為標準做法。我們需要確保內容在各種裝置上都能良好顯示，從大螢幕桌機到小尺寸手機都要兼顧。

這個 chat shortcode 在設計時就考慮了這些需求，使用彈性布局和適當的斷點來確保最佳的使用者體驗。 {% end %}

{% chat(speaker="claude") %} 您說得很對！響應式設計確實是現代網頁開發的核心原則。

對於這個 chat shortcode 來說，還有幾個重要的設計考量：

- 內容可讀性：確保文字大小在各種螢幕上都適合閱讀
- 觸控友善：在觸控裝置上提供足夠的點擊區域
- 效能優化：避免不必要的重排和重繪
- 漸進增強：即使 CSS 載入失敗，基本內容依然可用
- 這種漸進式的設計方法確保了功能的穩定性和使用者體驗的一致性，這對於部落格這類內容導向的網站特別重要。
{% end %}

### 程式碼展示測試

{% chat(speaker="copilot") %} 讓我展示一個完整的機器學習專案結構：

```
ml_project/
├── data/
│   ├── raw/              # 原始數據
│   ├── processed/        # 處理後的數據
│   └── external/         # 外部數據源
├── notebooks/            # Jupyter notebooks
├── src/
│   ├── data/            # 數據處理模組
│   ├── models/          # 模型定義
│   ├── visualization/   # 視覺化工具
│   └── utils/           # 工具函數
├── tests/               # 測試檔案
├── requirements.txt     # 相依套件
└── README.md           # 專案說明
這種結構化的組織方式讓專案更容易維護和協作。
```

{% end %}

## 其他實用 Shortcode

### 色彩標記

我們提供幾個便捷的色彩標記 shortcode：

{% cr() %}
這是紅色文字，通常用於錯誤或警告
{% end %}

{% cg() %}
這是綠色文字，通常用於成功或正確資訊
{% end %}

加上 `halo=true` 參數可以產生發光效果：

{{ cr(body="發光紅色文字效果", halo=true) }}

{{ cg(body="發光綠色文字效果", halo=true) }}

### 隱藏內容

使用 spoiler 功能來隱藏劇透內容：

{% ch() %}
這是隱藏的內容，滑鼠移過才會顯示
{% end %}

### 自訂顏色

{{ color(body="這段文字是橘色的", color="orange") }}

{{ color(body="發光橘色文字效果", color="orange", halo=true) }}

---

這個示範頁面展示了各種 shortcode 的使用情境和功能特色。無論是編輯備註、對話展示，還是文字樣式標記，都能讓你的文章更加豐富和易讀。
