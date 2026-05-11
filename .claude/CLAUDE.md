# 我的履歷 — Resume Authoring Repo

## Quick context (read this first)

這個 repo 同時是：
1. **本地 authoring workspace**（你日常編輯）
2. **GitHub repo `xuancheng307/xuancheng307`**
3. **GitHub Pages 主頁 `xuancheng307.github.io/xuancheng307/`**

主要設計：

- **`_source/`**：你 / AI 編輯的「履歷素材」（GitHub Pages 不渲染，Jekyll 慣例底線忽略）
- **root（`index.html` / `case-studies/` / `build.css` / `fonts/`）**：渲染成品（對外可見）
- **`README.md`**：GitHub repo profile readme（github.com/xuancheng307 主頁顯示）

讀 `_source/README.md` 看完整 schema 跟使用流程。

## Stack

- Static site：universal-resume base (Tailwind + FiraGO + Noto Sans TC)
- GitHub Pages 自動部署 (`main` branch root)
- 無 build step — index.html 是手寫 HTML（之後 AI 維護一個 rebuild 流程從 _source/ synth）

## 主要 workflow

### 加新作品 / 經驗
1. 找對應 category：`_source/<category>/`
2. 複製 `_README.md` 裡的 template
3. 改檔名為 `<slug>.md` 或 `<YYYY-MM>_<slug>.md`
4. 填 frontmatter + `# Hook` + `# Detail`
5. 告訴 AI「rebuild resume」→ 同步到 index.html

### 更新基本資料
- `_source/facts.yaml` — 結構化
- `_source/about.md` — 敘事

### Rebuild 流程（AI 執行）
1. Read `_source/**/*.md` + frontmatter
2. Sort by `priority` + `start_date` desc
3. Synthesize → `index.html` + `case-studies/<slug>.md`
4. `git add -A && git commit -m "..." && git push`
5. GitHub Pages 1 分鐘內上線

### Deploy
- `git push origin main` 後 GitHub Pages 自動建置（~30-60s）
- Live URL: https://xuancheng307.github.io/xuancheng307/

## 重要事實 / Rules

- **不要在 _source/ 放敏感資訊**（雖然底線開頭 Jekyll 不渲染，但 GitHub repo 看得到）
- **碩論 originality framing**：原創在數值分析層、ZK 用 stock Halo2/KZG。**不要說 novel cryptography**
- **zkml-knowledge-base 寫 curated 不是 authored**（claude1 audit 證實 AI 生成 + 人工 curated）
- **zkComply 是 product memo 不是 implementation**（無 .rs / 無 Cargo.toml）— 不要說「Rust 實作」
- **語馨自學專案永遠不放這裡**（高敏感未成年個資 / chairman 是 ghostwriter）
- **碩論畢業日期**：原寫 2025-12，已 2026-05 還沒畢業 — 實際 status 待 chairman 確認，目前先留 TBD

## 跨機關係

- Machine 2 (DESKTOP-65URRMU): `D:\claude-projects2\我的履歷\`
- Machine 1 (DESKTOP-49UBLQI): 應 clone 同一個 repo，目前未建（chairman 視需要）

## 連結

- GitHub: https://github.com/xuancheng307/xuancheng307
- Live: https://xuancheng307.github.io/xuancheng307/
- Profile: https://github.com/xuancheng307

## 改造紀錄（v3 → v16 簡史）

v3-v8 多輪 design iteration（chairman 反覆嫌設計）→
v10 研究 GitHub top templates → v11 fork universal-resume →
v12 砍內容到 hook level → v13-15 layout fix（單欄 + navy section header） →
v16 加統一證券 entry + LangGraph

詳細 history 在 `output_to_user/` (`workspace/output_to_user/履歷_高璿程_v*.html`).

`_source/` 結構是 2026-05-11 引入的，作為長期 authoring 系統。
