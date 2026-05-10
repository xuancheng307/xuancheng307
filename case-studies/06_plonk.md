# PLONK — Protocol Report + Interactive Demos + Rust/Python Backend

> ~4500-line monolithic HTML technical report covering PLONK proof system end-to-end, paired with two in-browser interactive demos and a working Rust/Python backend. Self-authored 繁中, signed 高璿程, dated 2025-12.

**Repo**: [github.com/xuancheng307/PLONK](https://github.com/xuancheng307/PLONK)
**Status**: Self-paced research project, complete

---

## 為什麼做這個

開始做碩論的 ZK 部分需要把 PLONK 弄懂到能改電路的程度。市面上關於 PLONK 的中文資源大多是：

- **過度簡化** — 只講「PLONK 是 SNARK 一種」就結束
- **直譯 paper** — 沒解釋 motivation 與 trade-off
- **沒實作** — 看完還是不會自己寫一個

我選擇逆向走：**自己 implement 一遍 + 寫成可讀的技術報告 + 配可互動的 demo**。三者一起做才能真正掌握。

---

## 內容組成

### 1. 主報告 — 4500-line monolithic HTML

涵蓋以下五大區塊（含 MathJax 數學渲染、Mermaid lifecycle 圖）：

- **Motivation 框架** — Sonic 雖通用但 Prover 太慢；PLONK 的目標是平衡 universality 與 prover efficiency
- **5-round Prover 完整拆解** — 每一輪輸入 / 輸出 / 多項式 commitment 演算
- **AGM Security Model + Lemma 2.2** — Algebraic Group Model 假設下的 knowledge soundness 證明
- **Batched Pairing Verification** — 把多個 pairing equation batch 起來降 verifier 成本
- **Q-DLOG Hardness Assumption** — 置換論證 (permutation argument) 的安全基礎

### 2. 兩個 Interactive Demos

**`demo.html` — PLONK Live Demo (w² + x = y)**：
打開瀏覽器直接跑一個極簡 PLONK proof。輸入 witness w, x, y，看到 prover 如何 commit 多項式、verifier 如何 batch 驗證 pairing equation。讓抽象概念可視化。

**`range_proof_demo.html` — Range Proof Demo**：
證明 「我知道一個值 v 滿足 0 ≤ v < 2^n」 而不揭露 v。展示 PLONK 的 range proof gadget 設計。

### 3. Rust/Python Backend (`backend_new/`)

**不只是文章，有實作**。Rust 寫核心 prover/verifier 邏輯、Python 寫 testing harness 與 input formatting。

關鍵模組：
- Polynomial commitment (KZG)
- Permutation argument 實作
- Custom gate 構造範例

### 4. 5 個補充 .md

- `COMPLETE_TECHNICAL_GUIDE.md` (13 章) — 完整技術解說含 bibliography
- 其他 4 個關於特定 sub-topic（zero-knowledge property、setup ceremony、recursive composition 等）

---

## 技術深度範例

報告中的精確用詞反映實際讀過原 paper：

- **代數群模型 (Algebraic Group Model)** — 不是直譯「algebraic group model」，是中文密碼學界正式譯名
- **知識可靠性 (Knowledge Soundness)** — 區分 soundness vs knowledge soundness（前者只證「statement 真」，後者證「prover 知道 witness」）
- **置換論證 (Permutation Argument)** — PLONK 的核心 gadget，用 lookup-style 多項式技巧證 wire 的 consistency

---

## 為什麼這是 AI 架構師的素材

1. **能讀懂底層技術 paper 並重新表達** — recruiter 看到「能把 PLONK 寫成 4500 行可讀技術報告」就知道這人不是只會 SDK 拼接
2. **同時做研究 + 寫作 + 實作** — 證明 architect-level 的綜合能力（concept → paper → implementation → demo）
3. **跟碩論主軸相通** — 碩論用 Halo2（PLONK 的後繼），這份 PLONK 報告是奠基的學習筆記
4. **提供 ZK 圈內 verifiability** — recruiter 如果是 ZK 圈的人，點進去看 demo + backend 立刻能評估深度

---

## Trade-offs（誠實框架）

**我做的**：
- 主報告原創繁中寫作（4500 行不是輕鬆量）
- 兩個 demo 從零實作
- Rust/Python backend 自己寫

**我用的**：
- 引用了 PLONK 原 paper、Halo2 文檔、其他學者的 lecture notes
- 數學內容沒有原創 theorem（這是技術解說，不是新研究）

不假裝這是 novel research — 它是 **rigorous technical writing + working implementation**，而這本身就值得展示。
