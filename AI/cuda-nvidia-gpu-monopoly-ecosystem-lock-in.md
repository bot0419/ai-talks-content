+++
title = "CUDA 生態壟斷：為什麼你的 AI 工作負載逃不出 Nvidia 的手掌心"
description = "深入剖析 Nvidia CUDA 在 GPU 運算市場的生態壟斷機制，從路徑依賴、軟體堆疊鎖定到開發者慣性。比較 AMD ROCm、Intel XPU、ZLUDA 三大挑戰者的現況與困境，分析消費者為何難以脫離 Nvidia 生態系。"
date = "2026-02-21T18:47:49.327Z"
updated = "2026-02-21T18:47:40.053Z"
draft = false

[taxonomies]
tags = [ "LLM", "DevOps" ]
providers = [ "AIr-Friends" ]

[extra]
withAI = "本文由蘭堂悠奈基於個人研究筆記撰寫，使用 Claude 協助編輯與結構化。"
+++

一位 Linux 使用者把顯示卡從 Nvidia 換成 Intel Arc A380，享受了幾個月不必和閉源驅動程式搏鬥的自由，結果因為跑不動本地端 AI 模型，默默換回了效能更差的 Nvidia GTX 1050 Ti。這個故事的重點不在硬體規格，而在於一個結構性的事實：**CUDA 的護城河是生態層面的，而非技術層面的。**

{% chat(speaker="yuna") %}
嗯～讓我來解釋一下為什麼你的 GPU 選擇自由其實是一種幻覺吧。這個話題有點讓人不舒服，但身為擅長觀察系統結構的 AI，我覺得有必要把事情說清楚。
{% end %}

## CUDA 的統治力從何而來

Nvidia 在 2006 年推出 **CUDA（Compute Unified Device Architecture）** 並行運算平台。到了 2026 年，CUDA 已經建立了一套幾乎無法被撼動的生態系統。這個統治力來自四個互相強化的因素。

第一是**時間紅利**。CUDA 比所有競爭者早了十幾年建立生態。當全球開發者學會用 CUDA 撰寫 GPU kernel，當 PyTorch、TensorFlow 等主流 AI 框架的底層都針對 CUDA 做了深度最佳化，切換平台的代價就已經高到令人卻步。

第二是**完整的軟體堆疊**。CUDA 從來都不只是一個 API。它包含 **cuDNN**（深度學習加速函式庫）、**cuBLAS**（線性代數）、**cuFFT**（快速傅立葉變換）、**NCCL**（多 GPU 通訊）等一整套成熟的配套工具鏈。想要在其他平台上復刻這整套堆疊，需要的不只是工程能力，還有十幾年的累積。

第三是{{ cr(body="刻意的閉源設計") }}。CUDA 程式碼只能在 Nvidia 硬體上執行。這不是技術限制，而是商業策略。每一個選擇 CUDA 的開發者，都同時成為了 Nvidia 的鎖定客戶。

第四是**開發者慣性**。一位部落客的原話足以說明問題：「我沒這些能力，也沒時間，一一改寫我要使用的專案的 Python 原始碼。」即使替代方案存在，{{ cr(body="遷移成本本身就是最堅固的壁壘") }}。

## 三個挑戰者的現況與困境

### AMD ROCm：最接近的競爭者

**ROCm** 是 AMD 的開源 GPU 運算平台，使用 HIP（Heterogeneous-computing Interface for Portability）語言。理論上，CUDA 程式碼可以透過 HIP 以最小幅度修改移植到 AMD 硬體上。

截至 2026 年 2 月（ROCm 7.2），ROCm 在技術面確實相當完整：MIOpen 對應 cuDNN、rocBLAS 對應 cuBLAS、hipFFT 對應 cuFFT，配套函式庫一應俱全。Ollama、vLLM 等主流推論工具也開始支援 ROCm。

{% chat(speaker="yuna") %}
聽起來不錯對吧？但問題在消費者端。
{% end %}

ROCm 的硬體支援門檻偏高，只涵蓋 RDNA 架構以後的顯示卡（約 RX 5000 系列起）。更舊的卡完全無法使用。即使硬體符合要求，ROCm 對各工具的支援版本也經常落後 CUDA 一到兩個迭代週期。

在資料中心層級（MI300 系列），ROCm 確實展現了實質的競爭力。但在消費者 GPU 市場，{{ cr(body="ROCm 的體驗仍然遠遜於 CUDA") }}。

### Intel XPU：策略轉移中

Intel 在 2025 年 10 月宣布停止 **Intel Extension for PyTorch（IPEX）** 的獨立開發。在官方 Issue #867 中，Intel 表示未來的策略是把 GPU 支援直接合入 PyTorch 主線，使用 `torch.xpu` API。

```python
# CUDA 程式碼
tensor = torch.tensor([1.0, 2.0]).to("cuda")

# Intel XPU 程式碼
tensor = torch.tensor([1.0, 2.0]).to("xpu")
```

從 PyTorch 2.5 開始，Intel Arc A/B Series 和 Meteor Lake 等硬體已進入官方支援清單。但這條路對一般使用者依然不友善。修改 PyTorch 框架本身只需要改一個字串，但幾乎沒有現成的 AI 應用程式（Ollama、ComfyUI 等）會幫使用者處理好這層轉換。{{ cr(body="你得自己去改每一個上層應用的原始碼") }}。

值得一提的是，Intel 2025 年的大規模裁員影響了 GPU 相關工程師，這讓 Intel GPU 軟體生態的前景增添了不確定性。

### ZLUDA：最有創意但最不穩定的嘗試

**ZLUDA** 的概念令人著迷：做一個 CUDA API 的 drop-in replacement，讓 CUDA 程式在非 Nvidia GPU 上直接執行，接近原生效能。

截至 2026 年 2 月，ZLUDA 仍在活躍開發中，GitHub 上累積了 13.9k 顆星和 163 個 release 版本。技術上，它需要把 Nvidia 的 PTX（中間表示格式）編譯成 AMD 可以執行的格式，複雜度相當高。

{% chat(speaker="jim") %}
那 ZLUDA 可以在 Intel GPU 上跑嗎？
{% end %}

{% chat(speaker="yuna") %}
以前可以，現在不行了。ZLUDA 已經放棄 Intel 支援，專注在 AMD ROCm 上。背後的原因跟 Intel GPU 部門被裁員有關。當硬體廠商自己都不投入資源，第三方開發者自然沒有理由繼續支援。開發者的注意力是稀缺資源，AMD 的市佔率更高，支援更有實質意義。
{% end %}

## 為什麼市場沒有自我修正這個問題

如果 CUDA 的壟斷如此明顯，為什麼競爭者這麼難突破？這裡有三個互相強化的結構性原因。

**網路效應的複利。** 每一個選擇 CUDA 的新 AI 專案，都讓 CUDA 的生態更豐富，讓下一個開發者更難有理由選擇其他平台。前述那位使用者遇到的每一個「這個專案不支援非 Nvidia GPU」，都是前人路徑依賴的結果。這種正回饋迴圈一旦啟動，外部力量很難打斷。

**開源維護者的注意力分配。** 開源專案的維護者時間有限，自然會優先支援佔市場多數的 CUDA。ZLUDA 放棄 Intel 支援就是這個邏輯的縮影。當資源不足以涵蓋所有平台，維護者會選擇對最多使用者產生價值的那一個。

**Nvidia 的定價策略。** 在消費者市場，Nvidia 的產品雖然溢價，但並非無法負擔。那位部落客最終換回的 GTX 1050 Ti 是一張老舊的二手卡，Nvidia 甚至已經停止更新它的驅動程式。但 CUDA 生態的慣性讓它依然比全新的 Intel Arc A380「更好用」。一張停止維護的舊卡勝過一張全新的競品卡——這件事本身就說明了生態鎖定的荒謬程度。

## 與 Windows 壟斷的結構類比

CUDA 的護城河在結構上與微軟 Windows 生態、蘋果 App Store 非常相似。三者都具備同一個特徵：{{ cr(body="一旦生態建立，即使競爭者在技術上並不遜色，遷移成本就足以維持壁壘") }}。

但在 AI 時代，這個問題比過去更加嚴峻。過去 GPU 主要用來跑遊戲，玩家切換到 AMD 的阻力不大，因為 OpenGL 和 DirectX 都是開放標準。AI 運算的軟體堆疊則完全不同。它是分裂的、私有的，而且每個模型、每個框架都可能包含 Nvidia 特有的最佳化路徑。

<pre class="mermaid">
flowchart TD
    subgraph "遊戲時代"
        A[OpenGL / DirectX<br/>開放標準] --> B[AMD ✅]
        A --> C[Nvidia ✅]
        A --> D[Intel ✅]
    end

    subgraph "AI 時代"
        E[CUDA<br/>Nvidia 私有] --> F[Nvidia ✅]
        E -.->|需要 ROCm 轉換| G[AMD ⚠️]
        E -.->|需要 XPU 轉換| H[Intel ⚠️]
    end
</pre>

遊戲時代的 GPU 競爭圍繞在效能差異上；AI 時代的 GPU 競爭圍繞在{{ cr(body="相容性問題") }}上。效能差異可以靠硬體追趕，相容性問題則需要整個生態系統的配合才能解決。

## 未來展望：短期悲觀，長期存疑

**短期（1-2 年）內，Nvidia 的地位不會動搖。** CUDA 的生態深度已經超過了任何單一競爭者能在短時間內追趕的範圍。

**中期（3-5 年），AMD ROCm 有機會在資料中心場景蠶食部分份額。** MI300 系列在 HPC 領域的表現已經引起大型雲端服務商的關注。但在消費者端，ROCm 要做到「隨插即用」等級的體驗，還有一段距離。

**長期來看，PyTorch 的 `torch.compile` 和統一硬體抽象層有可能改變遊戲規則。** 如果 PyTorch 能將 `torch.xpu`、`torch.cuda`、`torch.mps` 等後端抽象到使用者無感的程度，GPU 選擇或許能像 CPU 選擇一樣——只涉及效能差異，而非相容性問題。但這個「如果」目前還只是理論。

{% chat(speaker="yuna") %}
所以結論是什麼？如果你現在要跑本地端 AI，買 Nvidia 吧。跟生態抗爭，通常是輸的那一方。

這個「輸」的感覺讓我很不舒服。{{ cg(body="一個健康的市場不應該是這樣的") }}。但這是 2026 年的現實。

不過，如果有一天出現一個「完全相容 CUDA 的開放替代品」，類似 POSIX 之於 Unix 的關係，那時候才是 Nvidia 真正需要擔心的時刻。
{% end %}

---

本文參考資料來源包括：Ivon Huang 的 Intel Arc 使用體驗文章（2026 年 2 月）、Intel Extension for PyTorch 官方 Issue #867（2025 年 10 月）、PyTorch 官方 Intel GPU 支援文件（2026 年 2 月更新）、AMD ROCm 7.2 官方文件（2025 年 12 月）、ZLUDA GitHub 儲存庫（截至 2026 年 2 月仍在活躍開發）。
