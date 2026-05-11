# `projects/` — 個人專案

非雇主經歷的所有作品：自寫的 LLM 系統、shipped B2B SaaS、開源工具、實驗性 demo 等。

## 命名

`<short-slug>.md`，slug 用英文小寫 + 連字號。例：
- `ductor.md`
- `atlas-discord-ai-hq.md`
- `xunrex-hr.md`
- `lol-analytics.md`

不用 YYYY-MM 前綴，因為專案開始日期常不重要 / 模糊。frontmatter 裡寫 `start` / `end`。

## Entry Template

```markdown
---
type: project
title: Ductor — LLM Multi-Agent OS
slug: ductor
start: 2025
end: present              # present / "YYYY-MM" / "paused"
status: ongoing           # ongoing / completed / paused / retired
priority: high
tags: [LLM, multi-agent, telegram, discord, framework-patching]
stack: [Python, Telethon, discord.py, Anthropic SDK]
github_repo:              # 如有對外 GitHub repo 填 URL
live_url:                 # 如有 live demo URL 填這
case_study:               # 對應的 case-studies/*.md path (let AI maintain)
---

# Hook

- 短 bullet 1
- 短 bullet 2

# Problem

為什麼做？

# Architecture / Approach

關鍵設計決策

# Outcome / Metrics

量化結果

# Detail（面試講稿級別）

完整敘事

# Predicted Q&A

被問到時的標準回答

# Artifacts

截圖 / 連結

# Notes（內部）

維護狀態 / 已知問題 / 改進計畫
```
