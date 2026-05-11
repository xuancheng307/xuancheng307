# `experience/` — 工作經驗

放真正的雇主經歷（實習 / 全職 / 助教）。**個人專案不放這裡**，去 `projects/`。

## 命名

`<YYYY-MM>_<short-slug>.md`，例：
- `2025-07_uni-president-securities.md`
- `2025-02_sen-rong-electronics.md`
- `2020-09_nccu-management-ta.md`

YYYY-MM 是「開始月份」，方便 chronological sort。

## Entry Template

```markdown
---
type: experience
company:
  zh: 統一證券
  en: Uni-President Securities
role:
  zh: 量化交易部實習生
  en: Quantitative Trading Intern
start: 2025-07
end: 2025-08              # 或 "present"
priority: high            # high / medium / low — 決定要不要進預設履歷
tags: [stablecoin, quant-trading, ZKP, finance]
overlap_with: [filename]  # 若跟另一個 entry 時間重疊，列檔名
location: Taipei
---

# Hook（履歷顯示用，2-4 條短 bullets）

- 短 bullet 1
- 短 bullet 2

# Detail（面試講稿級別）

完整敘事 + 預期 Q&A...

# Artifacts

- 截圖 / 連結 / PDF
- 也可以 `mkdir <entry-name>_artifacts/` 把實體檔案放進去

# Notes（內部備忘）

- 日期校對紀錄
- chairman 沒講但記得的細節
```

## Priority 怎麼定

- **high**：履歷主軸要顯眼出現的（最近 2-3 年內的、跟目標職位高度相關的）
- **medium**：要列但不用詳細展開（例如多年前的相關經驗）
- **low**：不進預設履歷，但留著當講稿素材
