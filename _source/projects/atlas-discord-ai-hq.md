---
type: project
title: Atlas — Discord AI Company HQ
slug: atlas-discord-ai-hq
start: 2026-04
end: 2026-05
status: ongoing
priority: high
tags: [LLM, discord, rate-limit, edit-streaming, multi-channel]
stack: [Python, discord.py, Anthropic SDK]
github_repo:
case_study: case-studies/02_atlas.md
---

# Hook

- 自寫 **DiscordEditStreamer** (~225 LOC) 解 rate-limit 40062 + **buttons.py** (~120 LOC) message marker → native UI
- Channel-context awareness 4-section memory schema
- Live across **3 guilds / 5 slash commands / 14 channels / 3 ADRs**

# Problem

把 Discord 從聊天室升級成「AI 公司辦公室」 — 多 channel 對應不同 role / agent / context。

# Architecture / Approach

## DiscordEditStreamer
3 次迭代修補 rate-limit 40062：
- iter 1：移除 typing → 還是慢
- iter 2：one-shot typing 1 次/訊息 → 改善但不夠
- iter 3 ✅：完整 edit-streaming class — placeholder send → 1.5s/edit throttle → 1900-char split → 3-fail fallback

## buttons.py
`[button:Label]` markers → `discord.ui.View` + `Button`。Click 透過 `_ClickedMessage` proxy 重新走 full agent context。

## Channel-context awareness
per-channel rules JSON / per-guild FAQ / per-channel memory 4-section schema：
- Identity（LLM 寫）
- Persistent facts（LLM 寫）
- Open threads（LLM 寫）
- Auto-summary（cron 寫不踩前者）

# Outcome / Metrics

3 guilds、5 slash commands global synced、14 channel bootstrap、3 個 ADR、auto-respond channels（runtime-reload 不需重啟）。

# Predicted Q&A

**Q: Discord rate-limit 40062 怎麼診斷的？**
A: chairman「為什麼他沒在回覆我了 壞掉了嗎」+「不能像tg一樣 逐行回復嗎」。連續 POST /typing 每 5-9s × 4 auto-respond channels concurrent，30-60s Opus reply 期間 typing 重試風暴。

# Artifacts

- D:\claude-projects2\discord-ai-manager\ — main repo
- DESIGN.md — canonical doc
- 3 ADR

# Notes

- 維護中
- DESIGN.md Optimization Log 持續追加每次優化
