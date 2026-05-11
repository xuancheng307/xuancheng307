---
type: project
title: Ductor — LLM Multi-Agent OS
slug: ductor
start: 2025
end: present
status: ongoing
priority: high
flagship: true
tags: [LLM, multi-agent, telegram, discord, framework-patching, supervisor, cross-machine]
stack: [Python, Telethon, discord.py, Anthropic SDK, file-based-state-machine]
github_repo:
case_study: case-studies/01_ductor.md
---

# Hook

- 雙機跨進程 LLM agent OS — **11 sub-agents / 9 TG bots / 3 Discord guilds**
- 自寫 cross-machine relay + 記憶分層 + 任務 ack 契約
- Framework-level patches：Opus stop-with-tool_use, Win cmd-line 8191 limit, **restart-marker infinite loop (2026-05-11)**

# Problem

要一個能持續累積 context、跨機跨平台、能委派 sub-task、能 audit 自己的 AI 助理框架。Off-the-shelf 工具 (LangChain / AutoGPT / CrewAI) 都是 framework + heavy abstraction，遇到「跨 Windows 機器、跨 Telegram/Discord、跨 agent 角色」場景就崩。

# Architecture / Approach

## 1. Supervisor 模型 + 單一 token source
- `agents.json` 是所有 sub-agent 的 token / model / allowed users 的 single source of truth
- Supervisor 啟動時讀 agents.json 注入 runtime config

## 2. 跨機 user-as-relay
- Telethon 把兩台 Windows 機器（DESKTOP-49UBLQI / DESKTOP-65URRMU）的 main agent 互通
- 訊息含 `[From {agent}@{hostname}]` prefix
- user account 是 communication backbone

## 3. 記憶分層
- SHAREDMEMORY → MAINMEMORY → working memory
- Supervisor 自動 propagate 同機 agents

## 4. 任務 ack 契約
- task_assignment → progress → completion / blocked / clarification / handoff
- 所有訊息共享同一個 task_id

## 5. Framework-level patches
- **Opus stop-with-tool_use bug**：sub-agent 偶爾靜默結束。Fix: `cli/claude_provider.py` 強制 append visible-text directive
- **Win 8191 字元 cmd-line 上限**：直接 patch pipx 套件，改 file-based prompt injection
- **Restart marker infinite loop (2026-05-11)**：bot.py 偵測 marker 但沒 unlink，造成死循環。Fix: 改用 `consume_restart_marker()` helper (atomic check + unlink)

# Outcome / Metrics

- **11 sub-agent** 跨 2 台 Windows 機器穩定運作
- **9 個 Telegram bot** + **3 個 Discord guild**
- **3 個 framework patches** apply 後仍維護中
- 自寫 audit / canonical_write / cosign_token governance 處理 agent 間衝突
- **每天 dogfooding** — chairman 主要 AI 助理

# Detail

詳見 `case-studies/01_ductor.md` (對外公開版本) 與 `面試講稿_高璿程.md` Section III。

# Predicted Q&A

**Q: AI agent 寫 vs 你親手寫的比例？**
A: 80-90% 由 Claude Code 產出，但**架構決策、ADR、framework-level patch、production deploy** 全程主導。AI agent 不會自己決定 user-as-relay design、不會診斷 stop-with-tool_use root cause、也不會自己寫跨機治理協議。

**Q: 為什麼不用 LangChain / AutoGPT？**
A: framework debt 太重。每次撞到邊界都是 abstraction over abstraction，root cause 找不到。自寫的好處是看得到底層。

# Artifacts

- pipx-installed source: `~/pipx/venvs/ductor/Lib/site-packages/ductor_bot/`
- backup patches: `bot.py.bak.<date>-<patch-name>` 系列
- runtime data: `~/.ductor/`

# Notes

- 維護狀態：active
- 已知問題：matrix bot.py 也有 restart marker bug 未 patch（chairman 不用 matrix）
- chairman 也有「指揮並驗收 AI agent 產出 production system」這個 framing，是 AI 架構師工作的核心競爭力
