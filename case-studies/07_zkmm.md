# ZKMM — 34-page ZK Financial Compliance Research Site

> 34 pages 4 sections，繁中原創 ZK 研究站。Formal protocol specs (Merkle commitments + Delta-Greek constraints over Black-Scholes)、MathJax 渲染數學、Mermaid pipeline diagrams、含口試 lecture script。**Pure HTML/CSS/vanilla JS — no framework**。

**Repo**: [github.com/xuancheng307/ZKMM](https://github.com/xuancheng307/ZKMM)
**Stack**: Pure HTML5 + CSS + vanilla JS + MathJax 3 + Mermaid

---

## 為什麼做這個

碩論寫到一半發現一個問題：**論文 PDF 不適合給非密碼學家讀**。投資人 / LP / 同事看到 78 頁 LaTeX 直接放棄。但金融 ZK 議題的 audience 大部分**不是密碼學家**。

需要一個介面：

- 比 paper 短、視覺友善
- 比 blog 嚴謹、留住數學公式
- **不需要 framework 環境就能 run**（任何瀏覽器打開 = 可以讀）

ZKMM 這個 web 站就是論文敘事的「**面向 informed non-cryptographer**」版本。

---

## 內容結構（4 sections, 34 pages）

```
背景知識 (16 pages)
├── ZK 是什麼、為什麼能做隱私驗證
├── 金融場景痛點：基金合規 / LP 透明度
├── 現有解法的缺口（信任第三方、公開全鏈、homomorphic encryption）
└── ZK 的位置

協議說明 (7 pages)
├── Merkle commitment 設計（state root + market data root）
├── Delta-Greek constraints over Black-Scholes（公式 + ZK 電路化）
├── Dual-proof architecture（compliance + state-transition）
└── Trust model 拆解

提案報告 (5 pages)
├── 完整流程圖（Mermaid pipeline diagrams）
├── Demo / Walk-through
└── Open questions

Root (6 pages)
├── 索引、術語對照、bibliography、versioning
└── Lecture script（口試準備）
```

---

## 三個技術設計亮點

### 1. Formal Protocol Specs — Merkle + Delta-Greek

協議說明段不只是 prose，**含正式規格**：
- Merkle commitment 的 leaf / branch / root 結構描述
- Delta（Black-Scholes 對股價的偏微分）如何 ZK-circuit 化
- Constraint 系統的 high-level 寫法

這部分跟我碩論第 4 章 Scenario A 直接對應 — **網站是論文 protocol 的可讀版**。

### 2. MathJax 3 + Mermaid 雙渲染

- **數學公式**：用 MathJax 3 渲染（Black-Scholes Delta 的微分公式、Greek calculations、ZK proof 的 commitment equation）
- **架構流程**：用 Mermaid 畫 prover / verifier / data 的 pipeline lifecycle

22/34 個檔案有 Mermaid 圖。**圖文比例適合非數學專家但有 STEM 背景的讀者**。

### 3. Lecture Script with Stage Directions（口試準備）

最特別的部分：**有 toggleable lecture script blocks**，含：
- 講稿原文（要說的話）
- Stage directions（gesture / pause / 翻頁節奏）
- 預設 Q&A 與回應

這是把網站從「靜態知識頁」延伸成「**口試訓練教材**」。寫程式的人通常不寫 lecture script，這個跨界讓研究內容更扎實落地。

---

## 為什麼用 vanilla HTML/CSS/JS

選擇不用 framework（React / Vue / Next）的理由：

1. **永久可讀** — 10 年後打開瀏覽器還是能 render，不會因為某 framework deprecated 壞掉
2. **零部署複雜度** — GitHub Pages / 任何 static host 直接 serve，不需要 build step
3. **學術論文應該長壽** — paper 通常被引用多年，配套的網站也該有同等耐久度
4. **證明能力** — 能用底層 web stack 做出 designed UI，比靠 framework 抽象更有 craft 感

---

## 為什麼這是 AI 架構師的素材

1. **跨域整合**：把碩論的 ZK 數學、金融 domain、前端設計、教學說明整合成單一介面
2. **不依賴 framework**：證明能在底層寫出 production-quality web — 不是「會用 React 才能做事」
3. **研究溝通能力**：能把硬技術做給非專家讀懂，這在 AI 應用工程裡是稀缺技能（解釋 model behavior、跟非技術 stakeholder 溝通架構決策都需要）
4. **碩論的可讀化身**：recruiter 想快速理解我研究做什麼，10 分鐘看完 ZKMM 比 30 分鐘讀 78 頁 PDF 有效率
