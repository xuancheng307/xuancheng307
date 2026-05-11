---
type: project
title: Xunrex HR — Production B2B HR SaaS
slug: xunrex-hr
start: 2025
end: 2026
status: ongoing
priority: high
client_live: true
tags: [Flask, SaaS, HR, Taiwan-labor-law, multi-instance, production]
stack: [Flask, SQLAlchemy, Flask-Migrate, MySQL, SQLite, Waitress, PyInstaller]
github_repo:
case_study: case-studies/04_xunrex_hr.md
---

# Hook

- 台灣中小企業合規勞基法 HR 系統 — **第一個付費客戶上線運作中**
- Single-tenant 多 instance 部署，三模式（Docker / PyInstaller exe / 集中 hosting）
- **11,055 unit tests pass, 0 failures**

# Problem

台灣中小企業（~30 員工）勞基法 HR 痛點：Excel 跟不上、雲端 HRIS 月費 + 客製化貴。

# Architecture / Approach

## Single-tenant 多 instance（不是 multi-tenant SaaS）
- 每客戶獨立 deploy，相同 codebase 不同 DB
- 客戶資料隔離 + 客製化容忍度高
- 取捨：規模化代價高（每多客戶多一份 ops）— 但 ICP 是「小公司賣斷型」不追 SaaS 規模

## 三模式部署
- Docker（客戶有 IT）
- PyInstaller exe + Waitress (~30MB) — 客戶想 on-prem
- 集中多 instance hosting — 我們替客戶 host

## native_enum=False
所有 SQLAlchemy enum 存 VARCHAR 而非 native ENUM，MySQL prod 跟 SQLite lightweight 共用 model。

## 11 phase 模組
考勤、補休、薪資預覽、保險、email log、文件可見性、部門統計、撫養親屬健保、卡片綁定、parameter history、員工異動歷史。

# Outcome / Metrics

- **11,055 unit tests, 0 failures**
- **First paying customer (Xunrex) live in production**
- 客戶反饋三批 bug fix 流程已驗證 production-ready：
  1. 新建員工密碼提示 ✅
  2. 撫養親屬健保 (Change Plan, 5 phase)
  3. 請假/學歷部署 ✅ (added `flask seed-system`)

# Predicted Q&A

**Q: 為什麼不做 SaaS？**
A: ICP 不對。台灣中小企業愛買斷不愛訂閱、客戶要客製化、資料法規敏感。

# Notes

- D:\xunrex-hr\ - prod codebase
- D:\HR__Project2\ - 舊版（被取代）
- chairman 自寫測試覆蓋 0 failure 是強信號
