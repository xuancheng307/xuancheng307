# Xunrex HR — Production B2B HR SaaS

> 針對台灣中小企業（~30 員工規模）的合規勞基法 HR 系統。**第一個付費客戶 Xunrex 已上線運作中**。11,055 unit tests pass。三模式部署：Docker / PyInstaller exe / 集中 hosting。

**Status**: Production live, paying customer onboarded
**Stack**: Flask, SQLAlchemy, Flask-Migrate, MySQL 8.0 / SQLite, Waitress, PyInstaller

---

## 為什麼做這個

台灣中小企業 HR 痛點：
- 勞基法 / 勞動事件法持續改 — 自己寫 Excel 跟不上
- 雲端 HRIS（人易、雲端等）月費 + 每人費用 + 客製化貴
- ~30 人公司預算上限 NT$5K-10K/月

**機會**：把 HR 邏輯做成 single-tenant 多 instance（每客戶一份獨立 deploy），規避多租戶 SaaS 的隱私 / 客製化問題。

---

## 架構決策

### 1. Single-tenant 多 instance（**不是** 多租戶 SaaS）

每個客戶一份獨立 deploy。相同 codebase、不同資料庫。

**取捨**：
- ✅ 客戶資料完全隔離（沒 multi-tenant query mixing 風險）
- ✅ 客戶要的客製化邏輯（特殊薪資公式、特殊休假規則）可獨立 maintain
- ❌ 規模化代價高（每多一客戶就多一份 ops）— 但 ICP 是「小公司賣斷型」，不追 SaaS 規模

### 2. 三模式部署

| 模式 | 場景 | 技術 |
|---|---|---|
| **A. Docker** | 客戶有自己 IT 團隊 | docker-compose + MySQL 8.0 + Waitress |
| **B. exe + SQLite** | 客戶想 on-prem、不想養 server | PyInstaller + SQLite + Waitress (~30MB single binary) |
| **C. 集中多 instance hosting** | 我們替客戶 host | 同個機器多 process，每客戶一 port + 一 DB |

### 3. SQLAlchemy `native_enum=False`

所有 Enum 強制存 VARCHAR 而非 native ENUM type — 讓 MySQL prod 跟 SQLite lightweight mode 用同份 model 跑。

**踩過的坑**：早期 `native_enum=True` 預設，Docker (MySQL) 跑得起來但 PyInstaller exe 模式 (SQLite) 直接報 schema 衝突。改成 `native_enum=False` 一次性解掉。

### 4. 統一審批欄位設計

每個 approval flow（請假 / 補休 / 加班 / 出差）都有：
- `manager_approved_by` / `manager_approved_time` / `manager_approved_comment`
- `hr_approved_by` / `hr_approved_time` / `hr_approved_comment`

**取捨**：欄位重複看似不 normalized，但讓「誰審了 / 何時 / 備註」可以直接 query 出來，不用每個 approval flow 寫獨立表。對 HR domain 適合。

---

## 11 phase 模組

| Phase | 內容 |
|---|---|
| 1-3 | 基礎：員工 / 部門 / 出勤打卡 |
| 4-6 | 假別 / 薪資 / 加班 |
| 7 | 補休 / 請假預警 / 薪資預覽 |
| 8 | 團隊概覽 / profile edit / 緊急聯絡人 |
| 9 | 教育 / 工作經歷 / email log / email template |
| 10 | 保險檢視 / parameter history / 員工異動歷史 |
| 11 | 檔案操作 log / 出勤異常偵測 / 卡片綁定 / 文件可見性 / 部門統計 |

---

## 工程紀律 — 11,055 tests, 0 failures

每 phase 完成都加 unit test。整套 spec v1.8（45 decisions），每 decision 對應 test。

當第一個付費客戶 Xunrex 反饋 3 個 bugs（2026-05-08）：
1. **新建員工無密碼提示** ✅ FIXED — `admin.py:307` success flash 加初始密碼提示 + must_change_password 提醒
2. **撫養親屬健保** ⏳ Change Plan 開好 — `.claude/changes/2026-05-08_dependent-insurance-feature.md`，5 phase（model+migration / routes / templates / 健保 premium 重算 / 5 unit tests），預估 1-2 day
3. **請假/學歷經歷無法用** ✅ FIXED (deployment) — root cause: Xunrex IT 沒跑 migration `69faa26aae1e` + 沒 seed `LeaveType`。加 `seed_system_only()` + `flask seed-system` 命令（只 seed reference data，不含 test accounts/demo）。部署 instruction 補：`flask db upgrade && flask seed-system`

3 bug 反饋全部當天診斷 + 兩個當天解 + 一個有完整 Change Plan，**客戶反饋處理流程已驗證 production-ready**。

---

## 規模

- **11,055 unit tests pass, 0 failures**
- **第一個付費客戶 Xunrex 上線運作中**
- 11 phase 完整模組
- 3 種部署模式驗證
- 客戶反饋 → bug fix → deploy 完整循環走過

---

## Trade-offs

| 我選的 | 我沒選的 | 為什麼 |
|---|---|---|
| Single-tenant multi-instance | Multi-tenant SaaS | 客戶資料隔離 + 客製化容忍度 |
| Flask + SQLAlchemy | Django | 我熟、彈性高、ORM 控制更細 |
| `native_enum=False` 全 enum | 預設 native enum | 跨 DB compatibility |
| PyInstaller exe + SQLite 模式 | 純雲端 | 客戶不想 server 是真實需求 |

---

## 工作模式

跟 Ductor 一樣，AI agent 寫大量 code，我做：
- Spec v1.8 的 45 decisions（核心 design choices）
- Schema 設計（統一審批欄位、native_enum=False 等）
- 11,055 unit test 的 test design（不是手寫每個 test，但定 test strategy）
- Production deploy + 客戶溝通 + 反饋處理 SOP
