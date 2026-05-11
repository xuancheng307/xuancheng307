---
type: project
title: LOL Analytics — Research-grounded AI Coach (B2B SaaS)
slug: lol-analytics
start: 2025
end: 2026
status: ongoing
priority: high
tags: [B2B-SaaS, esports, AI-coach, PostgreSQL, materialized-view, Anthropic-API]
stack: [Next.js, TypeScript, PostgreSQL, Riot API, Anthropic API]
case_study: case-studies/03_lol_analytics.md
---

# Hook

- PCS / Tier 2-3 戰隊用 to-coach 數據工具 — **4,000+ matches 跨服資料 (KR/TW)**
- PostgreSQL materialized view + **GROUPING SETS** 一張 MV 同產 win/loss/all 三組
- **14-metric registry** 套用 gol.gg LCK / Sage Journals 2026 / ScienceDirect 2025 學術文獻
- `<AiInsightCard>` 對接 Anthropic API（5 placement × 4 kind × 2 variant）

# Problem

電競數據工具現狀：op.gg / mobalytics 是 to-C，Mobalytics Pro 月費 PCS 戰隊付不起。需要 to-coach + 中價位 SaaS。

# Architecture / Approach

## Materialized view + GROUPING SETS
一張 MV 同時產 (player_id, position, outcome) 三組 row，加 outcome 維度進 unique idx 仍可 CONCURRENTLY refresh。

## Research-grounded metrics
14-metric registry，ROLE_DEFAULT_METRICS 依文獻設定。CSD@15 ±5/10/20 三段分級 + caveats。

## AiInsightCard
React 元件 5 placement (overview / lane / trends / lineup / champion drilldown) × 4 kind (strong / weak / action / neutral) × 2 variant。

設計取捨：scaffold 先寫好，**初期用手寫 sample insight 上線**確認 UI 對 + 戰隊覺得 useful 再接 LLM。避免「先燒 LLM 錢 + 再發現 UI 沒人用」。

## Position scoping bug 修補（教育性 case）
Round AL → AO 4 次迭代。Kaze SUP DPM 被 MID 數據稀釋。寫 `/player/{id}/main-position` endpoint + leaderboard 改 `player_main_pos` CTE。

# Outcome / Metrics

- **Production live**
- Round A → AP 30+ 迭代
- 4000+ matches Riot Match-V5 backfill 跨服
- Demo player Kaze: 1094 場單雙積分

# Notes

- D:\claude-projects2\LOL數據分析專案\
- chairman 第一個有意做給外部付費客戶的產品
- 距離「有付費客戶」差最後一哩（簽約 + onboarding）
