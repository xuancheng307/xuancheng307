# Master's Thesis — Polynomial Approximation of Transcendental Functions for ZK Financial Compliance

> 78-page thesis at NCCU (政治大學資料科學研究所), expected defense 2026-12. Stack: Halo2 PSE v0.3.0, KZG/SHPLONK, BN254. **Core technical contribution: deferred truncation with BN254 headroom analysis** — letting 13 transcendental functions (exp, sigmoid, log, sqrt, etc.) be evaluated inside ZK circuits at production-grade precision.

**Status**: Active research, defense expected 2026-12

---

## 為什麼這個研究

**Tension**：金融場景一手要隱私（個人 / 機構不想曝光持倉與策略），一手要可驗證（監管 / LP / 對手方需要證明合規）。傳統解法都犧牲一邊：

- 完全公開（如以太坊全鏈）→ 隱私死
- 信任第三方審計 → 可驗證性靠人

ZK 是少數同時給隱私 + 可驗證的密碼學工具，**但 ZK 電路天生不擅長處理 ML / 金融模型裡常見的非線性算子**（exp、sigmoid、Black-Scholes Delta 公式等）。傳統做法用 lookup table，但 lookup 在固定點精度 (pp) ≥ 24 已經不可行（電路規模炸）。

我的研究 sweet spot：**用多項式逼近代替 lookup**，並透過工程級的 truncation 策略把精度做夠。

---

## 三層貢獻

### 1. 架構設計（Chapter 3 — Four-Layer Architecture）

論文第 3 章把 ZK 電路裡處理超越函數的整套流程拆成 4 層：

- **Input Layer** (§3.1) — 資料攝取與 fixed-point 表示
- **Coefficient Layer** (§3.2) — 多項式係數產生與儲存（Remez minimax 算法）
- **Precision Layer** (§3.3) — 固定點精度管理與 truncation 策略 ⭐ **核心貢獻層**
- **Circuit Layer** (§3.4) — 算術電路構造（Halo2 custom gates）

### 2. 核心技術：Deferred Truncation with BN254 Headroom Analysis

**問題**：ZK 電路在 BN254 上做 fixed-point 算術時，每一步都要 truncate 不然會 overflow。Per-step truncation 會引入累積誤差，讓高階多項式逼近的精度被吃光。

**論文貢獻**（§3.3.2 sec:deferred）：用 BN254 的 254-bit field headroom 分析，得出**正式 boundary equation**：

```
若 (d + 1) × pp < 254 → 整段 deferred truncation（合在最後一次）
否則                  → per-step truncation
```

其中 d 是多項式 degree、pp 是固定點精度。

**實證影響**（§3.3.5）：13 個超越函數在 pp = 16 時，**全靠 deferred truncation 才能達到 ULP = 1（unit in last place 誤差 ≤ 1）**的工程級精度。Per-step truncation 在這個 regime 完全做不到。

這是論文真正的原創貢獻 — **不在 ZK 系統設計、不在金融理論，在數值分析層**。

### 3. Scenario-Driven Evaluation（Chapter 4）

論文用兩個 production-style 金融 scenario 驗證整套設計：

#### Scenario A: Black-Scholes Delta-bounded Compliance

針對基金合規場景：基金經理人要對 LP 證明「投資組合 Delta 在閾值內」但**不揭露具體持倉**。

**架構組件**：

- **Four-layer trust model**：
  - Layer 1 — genesis anchor
  - Layer 2 — exchange authentication
  - Layer 2b — market data commitment
  - Layer 3 — ZK computation
  - Layer 4 — random audit
- **Dual-proof architecture**：
  - **Proof #1**（compliance proof）— 投資組合 Delta 在閾值內
  - **Proof #2**（state-transition proof）— state root R<sub>T</sub> 從 R<sub>T-1</sub> 正確衍生
- **Two Merkle commitments**：state root (R<sub>T</sub>) 與 market data root (R<sub>M</sub>) 分離

#### Scenario B: Merton Credit Default

更高精度需求的場景（pp=32）：用 Merton 結構模型計算企業違約機率，proof 大小 + verification 時間 + 精度均達到工程級。

---

## 實驗結果（Chapter 4 — 11-section Evaluation）

13 functions × 637 (pp, degree) configurations 的 **full ablation grid**。

### 主要 highlight 數字

- **Polynomial evaluation 比 lookup table 快 26.3×**；lookup 在 pp ≥ 24 已不可行
- **Minimax 比 Taylor 展開準確 2× 到 4,000×**（依函數而異）
- **Merton scenario：6,944-byte proof 在 ~23ms 內驗證，達到 28.6–29.0 effective bits 精度**
- **首個 end-to-end ZK transcendental-function evaluation** — Black-Scholes Delta hedging (pp=24) + Merton credit default (pp=32)

---

## 誠實的 Originality Framing

論文不假裝 novel cryptography：

- **ZK 系統用 stock Halo2/KZG** — 沒新證明系統、沒新承諾
- **金融模型用 stock Black-Scholes / Merton** — 沒新金融理論
- **原創全部在中間層**：把超越函數搬進 ZK 電路的數值分析方法（Remez minimax + Horner evaluation + deferred truncation with BN254 headroom analysis）

論文 conclusion 自己寫：「we do not propose new financial theory」。

這個誠實 framing 反而比假裝原創強得多 — 把問題定位在「**密碼學家熟悉的工具碰上密碼學家通常不碰的數值問題**」這個交叉地帶，是真正稀缺的位置。

---

## 為什麼這是 AI 架構師的關鍵素材

1. **數學嚴謹度**：78 頁論文 + formal algorithm box + theorem environments + 完整 ablation grid，跟 SDK 拼接級的 AI 工程師區別開
2. **金融 domain**：與我手上的證券業務員 / 期貨業務員證照 + 跨修商學院形成可驗證的金融理解閉環
3. **實作落地力**：不只 paper，PLONK 自實作 Rust/Python backend、ZKMM 公開 protocol spec、ezkl ONNX→Halo2 真實跑通
4. **誠實表達**：能清楚分清「我做的」（數值分析層）跟「我用的」（stock ZK system）— AI 架構師最重要的職位品質之一

---

## 相關工件（同 GitHub）

- [ZKMM](https://github.com/xuancheng307/ZKMM) — 34-page ZK financial compliance research site (含 lecture script for oral defense)
- [PLONK](https://github.com/xuancheng307/PLONK) — PLONK protocol report + 2 interactive demos + Rust/Python backend
- [zkml-knowledge-base](https://github.com/xuancheng307/zkml-knowledge-base) — 50-article ZKML curated reference
