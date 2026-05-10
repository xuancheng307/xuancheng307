# 高璿程 (Kao Xuan-Cheng)

**AI Systems Architect｜LLM Multi-Agent OS × ZK Financial Security × Production Engineering**

📄 **Live Resume**: [xuancheng307.github.io](https://xuancheng307.github.io/)

政大資料科學研究所碩士（2025-12 預計畢業）。研究方向：**零知識證明在金融安全領域的應用** — 把 13 個超越函數（exp / sigmoid / log / sqrt 等）塞進 ZK 電路裡。核心技術貢獻：**deferred truncation with BN254 headroom analysis**。

過去 12 個月主導設計並運行多個 LLM 系統，旗艦作品 **Ductor** 是雙機跨進程的 LLM Agent 作業系統，每天作為個人 AI 助理在 production dogfooding。

工作模式：**架構決策 + AI agent 編排 + 結果驗收**。應用數學底子 + 密碼學研究 + 金融證照三者交集。

📧 b128506789@gmail.com

---

## 🚀 LLM 系統工程

### [Ductor — LLM Multi-Agent 作業系統](./case-studies/01_ductor.md)
雙機跨進程的 LLM agent OS。Telegram 主控 + 11 sub-agent + cross-machine 記憶同步 + cron 任務委派 + 自寫 hook system。
**Stack**: Python · Telethon · Anthropic Claude SDK · file-based state machine

### [Atlas — Discord AI 公司總部](./case-studies/02_atlas.md)
把 Discord 升級成多 channel × 多 agent × 多 context 的「AI 公司辦公室」，自寫 edit-streaming 解 rate-limit、自寫 button parser、自寫 channel-context awareness。
**Stack**: Python · discord.py · custom rate-limit handling · multi-channel state design

### [LOL Analytics — Research-grounded AI Coach (B2B SaaS)](./case-studies/03_lol_analytics.md)
PCS / Tier 2-3 戰隊用付費得起的 to-coach 數據工具。14-metric registry 套用 gol.gg LCK / Sage Journals 學術文獻。AI Coach 介面對接 Anthropic API。
**Stack**: Next.js · TypeScript · PostgreSQL materialized views (GROUPING SETS) · Riot API

### [Xunrex HR — Production B2B HR SaaS](./case-studies/04_xunrex_hr.md)
台灣中小企業合規 HR 系統。第一個付費客戶 Xunrex 已上線。11,055 unit tests pass。三模式部署：Docker / PyInstaller exe / 集中 hosting。
**Stack**: Flask · SQLAlchemy · MySQL/SQLite · Waitress · PyInstaller

---

## 🔐 ZK 研究與技術寫作

### [Master's Thesis — Polynomial Approximation of Transcendental Functions for ZK Financial Compliance](./case-studies/05_thesis_v4.md)
78 頁論文，建構首個 end-to-end ZK transcendental-function evaluation（Black-Scholes Delta hedging + Merton credit default）。實證 polynomial evaluation 比 lookup 快 26.3×，Merton scenario 達成 6,944-byte proof + ~23ms verification。
**Stack**: Halo2 PSE v0.3.0 · KZG/SHPLONK · BN254

### [PLONK — Protocol Report + Interactive Demos + Rust/Python Backend](./case-studies/06_plonk.md)
~4500-line 完整繁中技術報告（5-round prover, KZG, AGM security, batched pairing）+ 2 個 in-browser interactive demo + Rust/Python PLONK backend。
**Repo**: [github.com/xuancheng307/PLONK](https://github.com/xuancheng307/PLONK)

### [ZKMM — 34-page ZK Financial Compliance Research Site](./case-studies/07_zkmm.md)
原創繁中 ZK 研究站，formal protocol specs (Merkle + Delta-Greek over Black-Scholes)、MathJax 渲染、Mermaid 圖、含口試 lecture script。Pure HTML/CSS/JS — 無 framework。
**Repo**: [github.com/xuancheng307/ZKMM](https://github.com/xuancheng307/ZKMM)

### Curated Reference

- [zkml-knowledge-base](https://github.com/xuancheng307/zkml-knowledge-base) — **Curated and edited** 50-article ZKML reference 跨 8 sections（proof systems / commitments / arithmetizations / toolchains）

---

## 工作哲學

我設計、編排、驗收。具體實作高比例由 AI agent 完成（Claude Code / Codex / Cursor），但**架構決策、ADR、debug 介入、production deploy** 全程主導。Ductor 本身就是這個工作模式的 dogfooding 證明。
