# Zero-Knowledge Proofs in Financial Security

> 政大資料科學研究所碩士論文研究方向：用密碼學在金融場景同時達成隱私保護與可驗證性。涵蓋 CBDC 隱私支付架構、ZK 基金合規（zkComply, Rust 實作）、ML 推理可驗證化（多項式逼近處理 ZK 電路中的非線性算子）。

**Status**: Master's research, 2024-2026 持續中
**Stack**: ZK proof systems (Halo2 / Plonky2 學習中), Rust (zkComply), 密碼學理論 + ML 算子電路化

---

## 研究主題定位

「金融」是 ZK 應用的高價值場景 — 同時要隱私（個人 / 機構不想曝光交易細節）也要可驗證（監管 / 對手方 / LP 需要證明合規）。傳統解法（信任第三方 / 公開全鏈）都犧牲一個換另一個。**ZK 是少數能同時給兩者的密碼學工具**。

我的研究主軸圍繞這個 tension 展開：

```
                  隱私保護
                     ↑
                     |
           [傳統金融]      [ZK 金融證明]  ← 我的研究
                     |
                     |
           [公鏈 / 公開審計] →   可驗證性
```

---

## 三個 sub-projects

### 1. CBDC 隱私支付架構

**問題**：央行數位貨幣（CBDC）落地最大爭議是「使用者匿名性 vs 反洗錢監管」。各國 pilot 大多選擇 **顯式 user identity + 監管全可視**，犧牲隱私。

**研究方向**：用 ZK 設計分層匿名架構：
- 一般小額交易 → 完全匿名（user 對其他 user / 央行）
- 大額或可疑交易 → 監管可解密（threshold cryptography）
- 證明每筆交易合規（餘額 ≥ 0 / 不重複花用 / 在白名單內）但不揭交易內容

接近 Zcash + 監管 backdoor 的混合架構，但設計更貼合**台灣 / 亞洲監管需求**。

### 2. zkComply — ZK 基金合規證明（Rust 實作）

**問題**：基金經理人想對 LP（有限合夥人）證明「投資組合符合委託合規條款」（如「股債比 70/30 ±5%」、「不買菸草股」、「ESG 評分 ≥ X」），但**不想揭露具體持股**（避免被 front-run、避免揭策略）。

**架構**：
- 基金 manager 把 portfolio 編成 ZK circuit
- 對 LP 給 ZK proof：「我滿足契約條款 P1, P2, P3」
- LP 收到 proof 不見 portfolio 但確信合規

**狀態**：Rust 實作中，2026-04 暫停（碩論期間排序）。架構與電路設計已完成。

**這個 project 同時是**：
- 履歷上的 case study（密碼學設計 + 金融合規 domain 跨界）
- 碩論研究的 applied 案例
- 未來可能商業化方向（fund administrator 工具）

### 3. ZKML — ML 推理可驗證化

**問題**：用 ML 做金融決策（信用評分、反詐、風險定價）時，模型 inference 結果該如何讓「沒有 model weights 的對方」相信？

**技術核心**：ZK circuit 處理非線性 ML 算子（exp / sigmoid / tanh）成本極高 — 標準 ZK arithmetic circuit 只擅長加減乘 mod p，非線性算子要 lookup table / 範圍展開 / 多項式逼近三種主流路線。

**我的方向**：研究**多項式逼近** —— 用低階多項式替代 exp / sigmoid，控制誤差範圍同時讓 prover overhead 可接受。

**目標**：在 verifiable inference accuracy 與 prover overhead 之間找實務可行的 sweet spot。

---

## 公開知識庫（Research Communication）

研究做到一半發現中文圈缺乏 ZK 教材，所以同步建了三個介紹網站：

- **[ZKMM](https://github.com/xuancheng307/ZKMM)** — ZKML 介紹網站
- **[zkml-knowledge-base](https://github.com/xuancheng307/zkml-knowledge-base)** — ZKML 知識庫（深度 reference）
- **[PLONK](https://github.com/xuancheng307/PLONK)** — PLONK 證明系統技術解說

**Why this matters**：能把硬核技術寫給人讀的研究者比能做研究的研究者**少**。這幾個網站證明我能做 research dissemination — 對任何 ZK 應用團隊都是稀缺能力。

---

## 跟我其他 work 的連結

| 我的其他 project | 跟金融安全證明的連結 |
|---|---|
| **Ductor** | 跨機 + 跨 agent governance 設計，跟分散式信任協議思維同源 |
| **Atlas** | Channel-context awareness 跟「什麼資訊在什麼 audience 揭露多少」是同一類設計問題 |
| **Xunrex HR** | HR 系統有大量「審批 / 簽核 / 稽核」需求，跟 ZK 在金融合規場景的 audit trail 思維對齊 |
| **應用數學系背景** | ZK 證明系統的數學基礎（橢圓曲線、有限域、多項式 commitment）需要硬數學底子 |
| **金融證照（券商高級 / 期貨）** | 對金融合規 / 信用評等 / 風控 domain 有 baseline 理解 |

---

## 為什麼這個 framing 對 AI 架構師職位重要

1. **跨界**：純 ML / 純金融 / 純密碼學的人多，三邊交集少
2. **ZKML 是 AI × 密碼學的前沿**：看得懂的人可以設計「能驗證的 AI 服務」這類新產品形態
3. **金融合規是 enterprise AI 落地最大阻礙之一**：ZK 是少數能把「AI 模型決策」轉成「可向監管 / 對手方證明合規」的工具
4. **research 證明**：公開知識庫 + 碩論進度 + zkComply Rust 實作 = 可驗證的研究深度

---

## 目前進度

- 碩論題目方向確定，多項式逼近 exp 函數初步結果中
- zkComply Rust 實作架構完成（暫停）
- 三個公開知識庫網站 live
- 預計 2025-12 完成碩論答辯
