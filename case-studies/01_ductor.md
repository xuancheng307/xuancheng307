# Ductor — LLM Multi-Agent 作業系統

> 雙機跨進程的 LLM agent OS。Telegram 為主控介面，11 個跨機 sub-agent 各自獨立 workspace，cron 委派任務，cross-machine 記憶同步，自寫 hook 與 governance 機制。每天作為主要個人 AI 助理 + 開發協作框架在 production 運行（雙機 dogfooding）。

**Status**: Active, 2025-2026 持續維運
**Stack**: Python, Telethon, Anthropic Claude SDK, Codex CLI, file-based state machine

---

## 為什麼做這個

我不要「另一隻 ChatGPT chat」— 我要一個**能持續累積 context、能跨機跨平台、能委派 sub-task、能 audit 自己**的 AI 助理框架。

Off-the-shelf 工具 (LangChain, AutoGPT, CrewAI 等) 都是 framework + heavy abstraction，**遇到我這種「跨 Windows 機器、跨 Telegram/Discord、跨 agent 角色」的場景就崩**。所以從零自寫一個適合個人重度使用者的 LLM agent OS。

---

## 架構決策

### 1. Supervisor 模型 + 單一 token source

- `agents.json` 是所有 sub-agent 的 token / model / allowed users 的 single source of truth
- `config.json` 單獨管 runtime 行為（不混進 token）
- Supervisor 進程啟動時讀 `agents.json` 並把每隻 agent 的 token 注入它自己的 runtime config
- **設計取捨**：寫死 single source 比讓每 agent 自管 token 簡單 10 倍，代價是一個 token 改動要重啟 Supervisor — 可接受

### 2. 跨機 user-as-relay（最有意思的部分）

兩台 Windows 機器（DESKTOP-49UBLQI / DESKTOP-65URRMU）的 main agent 要互通 — Telegram bot 之間沒有官方 API，所以走 **user account relay**：

- 一個 Telegram user account 透過 Telethon 同時是兩邊 main bot 的對話對象
- 訊息含 `[From {agent}@{hostname}]` prefix 識別來源
- main agent 收到帶 prefix 的訊息知道是另一台機器發來的，做 cross-machine routing

這個設計有趣點：**user account 是 communication backbone，不是 user**。Telethon credentials 存 .env，每次 cross-machine call 經過 `tools/agent_tools/ask_agent_remote.py`。

### 3. 記憶分層

```
SHAREDMEMORY  (跨 agent 通用，Supervisor 自動 propagate 到同機 agents)
    ↓
MAINMEMORY    (單 agent 私有，permanent)
    ↓
working memory (per session, ephemeral)
```

每 agent 啟動時 system prompt 自動含 SHAREDMEMORY + MAINMEMORY 內容。
**Windows 命令列 8191 字元上限是這個設計的 hard constraint** — SHAREDMEMORY 太大會炸，`SHAREDMEMORY.refs.md` 機制把細節 lazy-load 到需要時才讀。

### 4. 任務 ack 契約

跨 agent 任務委派一定有 race / timeout 風險。協議規定：

- `task_assignment` → 發起方建任務
- 收到方立刻 `progress` ack
- 完成 `completion`、卡住 `blocked`、要問 `clarification`、轉交 `handoff`
- **所有訊息共享同一個 `task_id`**，否則發送方 `wait_all` 永遠 timeout

這條看似簡單但 production 撞了多次才定下來 — 任何分散式系統都需要明確 ack 契約。

### 5. 每 agent 獨立 workspace

```
~/.ductor/agents/<name>/workspace/
├── tools/
├── skills/        (90+ skills)
├── memory_system/
├── cron_tasks/
└── output_to_user/
```

避免跨 agent 污染。記憶體小，磁碟大 — 用空間換隔離。

---

## Framework-level 介入（最具技術性的部分）

### 修補 Opus stop-with-tool_use-only

**症狀**：sub-agent 偶爾「靜默結束」— 用戶看不到任何 reply，但 log 顯示 LLM 確實 generate 了 token。

**診斷**：Opus model 結束 turn 時可能 stop_reason = end_turn 但 content 只有 tool_use block，沒有 text block。orchestrator 收到後 stdout 為空 → 回傳給 user 為「無回應」。

**修補**：在 `cli/claude_provider.py` 加 `_FORCE_VISIBLE_TEXT_DIRECTIVE` 字串，每次 build system prompt 時強制 append 到末尾：「每次 turn 結束**必須**包含至少一個 text block 給 user」。

12 隻 agent 全部載入此 patch 後該問題消失。

### Win 命令列 8191 字元上限

**症狀**：Claude CLI 的 `--append-system-prompt` 把大型 SHAREDMEMORY 塞進 cmd line，超過 Windows 上限就崩。

**修補**：直接 patch ductor 的 pipx 套件，把 `--append-system-prompt` 改寫成 file-based（寫 temp file，CLI 讀 file），規避 cmd-line 限制。

代價：pipx upgrade 會覆蓋 patch — 維護一個 `.bak.<date>` 備份 + recovery procedure 文件。

### 跨機 hook 同步審計流程

當我發現 typecheck.js 在 .ts/.tsx 檔案被 Edit/Write 後 `process.exit(2)` 會 hard-block sub-agent 的 tool call（在 Next.js 專案上 sub-agent 看似當機），不只 Machine 2 要修，**Machine 1 也得同步**。

流程：
1. Machine 2 上發現 + 改 hook
2. 透過跨機 user-as-relay 通知 Machine 1 main agent
3. Machine 1 自己 audit 對應檔案 + 同步改 + 反向回報
4. 雙方確認對齊

這是「跨機治理」的 dogfooding。

---

## 規模

- **11 sub-agent** 跨 2 台機器穩定運作
- **9 個 Telegram bot** + **3 個 Discord guild**
- 自寫 audit / canonical_write / cosign_token governance 處理 agent 間衝突
- Cron / webhook / task delegation 子系統
- task agent 可 `ask_parent` 反向提問 — 形成 user ↔ main agent ↔ task agent 三層

---

## Trade-offs（我選擇了什麼，為什麼）

| 我選的 | 我沒選的 | 為什麼 |
|---|---|---|
| Telegram + Telethon as backbone | LangChain + native API | TG 雙機通訊免費 + 設計輕，沒 framework debt |
| File-based state machine | Database-backed | 個人 scale 用 file 夠；好 git diff、好 grep、易 recovery |
| 跨機 user-as-relay | 自架 message queue | 個人專案不該維運 broker；Telethon 已穩 |
| Supervisor 模型 | Per-agent 自管 token | 簡單、可重啟、可 audit |

---

## 工作模式

我親手寫每段 code 嗎？不。**Ductor 大部分 code 是我設計、AI agent 寫、我驗收 + integrate**。

但我親手做的：
- 架構決策（記憶分層、ack 契約、跨機 relay）
- ADR 撰寫（為什麼這樣不那樣）
- Framework-level patch（上面三個介入）
- 撞牆 debug（race / timeout / encoding / cmd-line 等）
- production deploy + 雙機 dogfooding

**這就是 AI 架構師的工作本質** — 不是手刻 50,000 行，而是在 framework 層、決策層、驗收層做出對的選擇。
