---
type: research
title: Master's Thesis — Polynomial Approximation of Transcendental Functions for ZK Financial Compliance
slug: thesis-zk-financial-compliance
artifact_type: thesis
start: 2024
end: 2026-12   # expected defense — confirmed by chairman 2026-05-11
expected_defense: true
status: ongoing
priority: highest
flagship: true
tags: [ZK, halo2, KZG, BN254, financial-compliance, transcendental-functions, deferred-truncation]
authorship: solo-original
stack: [Halo2 PSE v0.3.0, KZG/SHPLONK, BN254]
location: D:\claude-projects\畢業論文v4\
pdf_path: D:\claude-projects\畢業論文v4\main.pdf
pages: 78
chapters: 6
ablation_grid: 13 functions × 637 (pp, degree)
case_study: case-studies/05_thesis_v4.md
---

# Hero Numbers

- **Polynomial evaluation 26.3× faster than lookup** (lookup infeasible at pp ≥ 24)
- **Minimax 2× to 4,000× accuracy over Taylor** (function-dependent)
- **Merton scenario: 6,944-byte proof verified in ~23 ms** (28.6–29.0 effective bits)
- **First end-to-end ZK transcendental-function evaluation** (Black-Scholes Delta hedging pp=24 + Merton credit default pp=32)

# Architecture (Chapter 3) — Four-Layer

- Input Layer (§3.1)
- Coefficient Layer (§3.2)
- **Precision Layer** (§3.3) ⭐ core contribution
- Circuit Layer (§3.4)

# Core Technical Contribution — Deferred Truncation with BN254 Headroom Analysis (§3.3.2)

Boundary equation: **if (d+1)·pp < 254 then deferred else per-step**
13 functions at pp=16 全靠 deferred 才能達 ULP=1.

# Protocol (Chapter 4 — Scenario A: Black-Scholes Delta-bounded Compliance)

- Four-layer trust model: genesis anchor / exchange auth / market data commitment / ZK computation / random audit
- Dual-proof architecture: Proof #1 (compliance) + Proof #2 (state-transition)
- Two Merkle commitments: state root R_T / market data root R_M

# Honest Originality Framing

論文不假裝 novel cryptography：
- ZK 系統用 stock Halo2/KZG
- 金融模型用 stock Black-Scholes/Merton
- **原創全部在數值分析層**（Remez minimax + Horner + deferred truncation）
- Conclusion 自己寫「we do not propose new financial theory」

# 用詞警示

✅ 安全：「four-layer architecture/trust-model」「dual-proof (compliance + state-transition)」「deferred truncation with BN254 headroom analysis」「two Merkle commitments」

❌ 不能用（zkComply memo 加料、論文沒有）：
- 「Delta Compliance Protocol」
- 「EdDSA in-circuit signature verification」

🟡 措辭注意：「two Merkle commitments」不是「dual Merkle architecture」

# Notes

- 2026-05-11 經過 claude1 跨機 audit + 直接讀論文章節驗證
- 預計畢業時間 **2025-12** 為原計畫，實際狀態 chairman 待確認（已 2026-05）
- 可能 pivot：碩論起初寫 CBDC 隱私支付，後來 v4 改成 transcendental function approximation 為核心
