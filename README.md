# 高璿程 (Kao Xuan-Cheng)

**AI Systems Architect｜LLM Multi-Agent OS × ZK Financial Security × Production Engineering**

政大資料科學研究所碩士（2026）。研究方向：**零知識證明在金融安全領域的應用**。
過去 12 個月主導設計並運行多個 LLM 系統，旗艦作品是雙機跨進程的 LLM Agent 作業系統 Ductor，每天作為個人 AI 助理在 production 中 dogfooding。

工作模式：**架構決策 + AI agent 編排 + 結果驗收**。應用數學底子 + 密碼學研究 + 金融證照三者交集。

📧 b128506789@gmail.com

---

## Selected Work

### 🛠 [Ductor — LLM Multi-Agent 作業系統](./case-studies/01_ductor.md)
雙機跨進程的 LLM agent OS。Telegram 主控 + 11 sub-agent + cross-machine 記憶同步 + cron 任務委派 + 自寫 hook system。
**Stack**: Python · Telethon · Anthropic Claude SDK · file-based state machine

### 🤖 [Atlas — Discord AI 公司總部](./case-studies/02_atlas.md)
把 Discord 升級成多 channel × 多 agent × 多 context 的「AI 公司辦公室」，自寫 edit-streaming 解 rate-limit、自寫 button parser、自寫 channel-context awareness。
**Stack**: Python · discord.py · custom rate-limit抗壓 · multi-channel state design

### 📊 [LOL Analytics — Research-grounded AI Coach (B2B SaaS)](./case-studies/03_lol_analytics.md)
PCS / Tier 2-3 戰隊用付費得起的 to-coach 數據工具。14-metric registry 套用 gol.gg LCK / Sage Journals 學術文獻。AI Coach 介面對接 Anthropic API。
**Stack**: Next.js · TypeScript · PostgreSQL materialized views (GROUPING SETS) · Riot API

### 🏢 [Xunrex HR — Production B2B HR SaaS](./case-studies/04_xunrex_hr.md)
台灣中小企業合規 HR 系統。第一個付費客戶 Xunrex 已上線。11,055 unit tests pass。三模式部署：Docker / PyInstaller exe / 集中 hosting。
**Stack**: Flask · SQLAlchemy · MySQL/SQLite · Waitress · PyInstaller

### 🔐 [ZK Proofs in Financial Security (Master's Research)](./case-studies/05_zk_financial_security.md)
碩士論文研究方向：用密碼學在金融場景同時達成隱私保護與可驗證性。涵蓋 CBDC 隱私支付、ZK 基金合規（zkComply, Rust）、ML 推理可驗證（多項式逼近 ZK 電路非線性算子）。
**Stack**: Rust · ZK proof systems · cryptographic protocol design

---

## 公開知識庫

把硬技術寫給人讀的能力 — 三個 ZK 主題自建網站：

- [ZKMM](https://github.com/xuancheng307/ZKMM) — ZKML 介紹網站
- [zkml-knowledge-base](https://github.com/xuancheng307/zkml-knowledge-base) — ZKML 知識庫
- [PLONK](https://github.com/xuancheng307/PLONK) — PLONK 證明系統技術解說

---

## 工作哲學

我設計、編排、驗收。具體實作高比例由 AI agent 完成（Claude Code / Codex / Cursor），但**架構決策、ADR、debug 介入、production deploy** 全程主導。Ductor 本身就是這個工作模式的 dogfooding。
