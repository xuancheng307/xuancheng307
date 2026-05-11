# `_source/` — Resume Authoring Workspace

這個資料夾是**履歷的原始素材**。你日常增刪改 `_source/` 下的檔案，AI 助手會把它組裝成 `index.html` + case-studies 推上 GitHub Pages 顯示在 https://xuancheng307.github.io/xuancheng307/ 。

GitHub Pages 不渲染底線開頭的資料夾（Jekyll 慣例），所以 `_source/` 對外不可見 — 安心放草稿、未來規劃、敏感細節。

---

## 怎麼用

### 加新作品 / 新經驗
1. 找對應的 category 資料夾（experience / projects / research / certifications / etc.）
2. 複製一份 `_README.md` 裡的 entry template
3. 改檔名為 `<YYYY-MM_short-name>.md`（experience 用日期前綴，projects/research 可不用）
4. 填 frontmatter + hook + detail
5. （可選）`mkdir <entry-name>_artifacts/` 放截圖 / PDF / demo 連結
6. 完成後 commit + push 到 GitHub，也順手告訴 AI 助手「rebuild resume」

### 更新技能
- `skills/advanced.md` / `intermediate.md` / `learning.md` 直接加減
- 一個技能就一行 — 用 `·` 分隔

### 改基本資料
- `facts.yaml`（姓名 / 聯絡 / 生日）
- `about.md`（自我介紹敘事）

### 新增軟性成就
- `achievements.md`：教師沿用、客戶上線、研究成果引用、媒體曝光、得獎等

### 未來規劃
- `roadmap.md`：想學的技能、想做的專案、學位後計畫

---

## Schema 通則

每個 entry markdown 用 frontmatter 存結構化資料：

```yaml
---
type: experience | project | research | education | certification
title: 顯示在履歷上的主標
[entry-specific fields]
priority: high | medium | low    # 決定要不要進預設履歷
tags: [list, of, keywords]        # 用來 cross-reference
status: ongoing | completed | paused | retired
---
```

下面 free-form 寫 `# Hook`（履歷顯示用，3 條短 bullets）+ `# Detail`（面試講稿級別）+ `# Artifacts`（截圖 / 連結）+ `# Notes`（內部備忘）。

---

## 跟對外資料夾的關係

```
我的履歷/  (= GitHub repo xuancheng307/xuancheng307)
├── _source/         ← 你寫的（GitHub Pages 不渲染）
├── index.html       ← AI 從 _source/ 組裝的成品
├── case-studies/    ← AI 從 _source/projects/ + research/ 組裝
├── build.css        ← FiraGO 樣式（universal-resume base）
└── fonts/           ← FiraGO 字檔
```

「rebuild resume」流程（之後 AI 自動化）：
1. 讀所有 `_source/**/*.md` frontmatter + body
2. 依 `priority: high` 挑要進主履歷的條目
3. 渲染 v17+ index.html 覆蓋
4. 渲染 case-studies/*.md 覆蓋
5. commit + push 到 main
6. GitHub Pages 1 分鐘內上線新版

---

## 同個 repo 兩種視角

- **github.com/xuancheng307/xuancheng307**：你日常 push、看 commit history、看所有 files（含 _source）
- **xuancheng307.github.io/xuancheng307/**：對外 live 履歷（只渲染非底線開頭的檔案）

如果 `_source/` 內容有敏感（草稿、未公開的合作、家人資料等），不要寫進去。要私密就走另外的私人 repo。
