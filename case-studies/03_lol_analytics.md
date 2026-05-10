# LOL Analytics — Research-grounded AI Coach (B2B SaaS)

> 給 PCS / Tier 2-3 戰隊用「付費得起的 to-coach 數據工具」。14-metric registry 套用 gol.gg LCK / Sage Journals 學術文獻、`<AiInsightCard>` 對接 Anthropic API、materialized view 用 GROUPING SETS 一張表同時產 win/loss/all 三組維度。

**Status**: Production live, 2025-2026, Round A→AP 30+ 迭代
**Stack**: Next.js, TypeScript, PostgreSQL (materialized views), Riot API, Anthropic Claude API

---

## 為什麼做這個

電競戰隊數據工具市場現狀：
- **op.gg / mobalytics / blitz** — to-C 個人提升用，不是給教練的決策工具
- **企業級工具** (Mobalytics Pro 等) — 月費 $X,000，PCS / Tier 2-3 戰隊根本付不起
- **PCS 戰隊現狀**：自己 review VOD + 手寫 spreadsheet

**機會**：to-coach 取向 + 中價位 SaaS，PCS / Tier 2-3 戰隊規模約幾十隊有預算需求。

---

## 三個技術核心

### 1. 資料層 — Materialized View + GROUPING SETS

**問題**：每場比賽要算「整體 / 勝場 / 敗場」三組 KDA / DPM / CSPM 等指標。如果 query time 即算，page load 慢；如果 pre-compute，三張 MV 浪費空間又難維護。

**解法**：用 PostgreSQL `GROUPING SETS` 一張 MV 同時產三組 row：

```sql
CREATE MATERIALIZED VIEW mv_player_30d_summary AS
SELECT player_id, position, outcome, 
       avg_kda, avg_dpm, avg_cspm, ...
FROM matches
GROUP BY GROUPING SETS (
  (player_id, position, outcome),
  (player_id, position, NULL),    -- outcome='all'
  ...
)
```

加 `outcome` 維度進 unique index → 仍可 `REFRESH MATERIALIZED VIEW CONCURRENTLY`，不阻塞 read。

API endpoint 只要加 `?outcome=all|win|loss` 就能 toggle。

### 2. Metric registry — research-grounded

**問題**：14 個 metric 哪些該對哪個位置 default 顯示？SUP 看 DamageShare 沒意義、JNG 看 DPM 偏低不是表現差。

**解法**：寫 `web/lib/metrics.ts` 14-metric registry 定 `ROLE_DEFAULT_METRICS`，依據：
- gol.gg LCK 統計
- Sage Journals 2026 esports 研究
- ScienceDirect 2025 lane phase 分析

**對線期門檻範例**：CSD@15（CS Difference at 15min）±5/10/20 三段分級 + `LaneGameScatter` 加 ±7 reference lines + glossary 寫清 caveats（scaling 英雄 / roamer mid 不要無腦看）。

把學術 paper 的 threshold 落地到產品 UI。

### 3. AI Coach 介面 — `<AiInsightCard>`

新 React 元件支援 5 placement × 4 kind × 2 variant：

- placement: `overview / lane / trends / lineup / champion drilldown`
- kind: `strong / weak / action / neutral`
- variant: full / compact

對接 Anthropic Claude API（API key 在 .env），prompt template 把 player metric snapshot + recent matches 喂進去產 insight。

設計取捨：scaffold 先寫好，**初期用手寫 sample insight 上線**（不接 LLM），確認 UI 對 + 戰隊覺得 useful → 再接 API。避免「先燒 LLM 錢 + 再發現 UI 沒人用」。

---

## Position scoping bug — 最教育性的 bug 修補

**症狀**：Kaze 是 SUP，但 leaderboard 顯示他的 DPM 偏高（被 MID 數據稀釋）。

**根因**：`mv_player_30d_summary` per (player, position) row，但 leaderboard 沒加 `position` filter，每個 player 有多個 position row → 聚合錯誤。

**修補（3 次迭代，Round AL → AM → AN → AO）**:
1. 新 `/player/{id}/main-position` endpoint 解析 `COALESCE(mapped(primary_role), most-played 365d)`
2. leaderboard no-filter 分支重寫成 `player_main_pos` CTE 每人只算主位
3. lane tab 5 個子組件做 page-level slice
4. 8 個 backend aggregate-per-player 沒 position 維度的 cards 加 hide-guard + callout

**驗證**：21 row 重複 → 7 row distinct。Kaze SUP DPM 不再被 MID 稀釋。

---

## 規模 / outcome

- **Production live** at `[domain TBD]`
- **Round A → AP** 30+ 迭代（每 Round 約 3-7 commit）
- **4000+ matches** Riot Match-V5 backfill 跨服（KR + TW）
- **Demo player Kaze**: 1094 場單雙積分跨服資料（KR Kaze#2nd + TW WindüChaser#0729）
- **Demo login**: `demo@lol-analytics.local` / `DemoT1Roster2026!`

---

## Trade-offs

| 我選的 | 我沒選的 | 為什麼 |
|---|---|---|
| GROUPING SETS 一張 MV | 三張 MV (all/win/loss) | 維護成本減半、refresh 一次到位 |
| Sample insight first, LLM later | 直接接 LLM | 先驗 UI 有沒有用，避免燒錢驗錯題目 |
| Hide-guard + callout (Round AN) | 直接砍 8 個 cards | 提供降級 UX，user 切回「全部」就能看 |
| Riot Match-V5 + custom timeline parsing | 純第三方 (op.gg API 等) | 完全 control 資料、不被 rate limit / pricing 卡 |

---

## 商業 framing（給看履歷的人）

這是我**第一個有意做給外部付費客戶的產品**。從 ICP 定義（PCS / Tier 2-3）、定價 framework、demo 登入 flow、研究文獻引用、AI Coach UX 都自己設計。

距離「有付費客戶」還差最後一哩（簽約 + onboarding + 客服流程）。技術已經 production-ready。
