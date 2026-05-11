---
type: research
title: PLONK — Protocol Report, Interactive Demos, Rust/Python Backend
slug: plonk
artifact_type: technical-report
start: 2025
end: 2025-12
status: completed
priority: high
authorship: solo-original
tags: [PLONK, ZK, KZG, AGM, batched-pairing, Rust, technical-writing]
github_repo: https://github.com/xuancheng307/PLONK
location: D:\claude-projects\PLONK\
size: ~4500 line monolithic HTML report
---

# Hook

- Authored **~4,500-line 繁中 technical report** covering 5-round prover, AGM security, batched pairing
- **2 in-browser interactive demos** (PLONK Live Demo w²+x=y / range_proof_demo)
- `backend_new/` Rust + Python PLONK backend (real implementation, not just article)
- 5 supplementary .md including 13-chapter COMPLETE_TECHNICAL_GUIDE

# Technical Depth

- 完整 5-round prover 拆解
- AGM (Algebraic Group Model) security + Lemma 2.2
- Batched pairing equation
- Q-DLOG hardness assumption
- 用詞精確：代數群模型、知識可靠性、置換論證

# Honest Originality Framing

不是 novel research — 是 **rigorous technical writing + working implementation**。
引用 PLONK 原 paper、Halo2 docs、其他學者 lecture notes。沒有新 theorem。
價值在「能讀懂底層 paper 並重新表達 + 配 working demo」。

# Predicted Q&A

**Q: 為什麼自己重寫一個 backend？**
A: 真正讀懂 PLONK 的方法是 implement 一遍。看完 paper 不會自己寫的話就是沒讀懂。

# Notes

- 簽名 高璿程, 2025-12
- 為碩論做準備的學習筆記 — 碩論用 Halo2 (PLONK 後繼)
- Tier 1 履歷素材
