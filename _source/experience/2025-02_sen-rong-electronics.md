---
type: experience
company:
  zh: 森嶸電子
  en: Sen Rong Electronics
role:
  zh: 研發部門 資料工程實習生
  en: Data Engineering Intern (R&D Department)
start: 2025-02
end: 2025-08
priority: high
tags: [data-engineering, ETL, MySQL, Streamlit, Python, MES]
location: Hsinchu (or wherever)
---

# Hook

- DRAM 模組測試 log ETL pipeline — **1 億+ records** 自動化進 production MySQL
- 跨部門 Streamlit 內部查詢介面（測試現場 / 財務 / 研發）

# Detail

主要負責公司 DRAM 模組測試與生產流程的數據系統建置與整合。

## 資料處理與自動化

規劃並實作 log 資料自動解析流程，處理超過 1 億筆測試數據（涵蓋 SPD、電壓、PMIC、錯誤 bitmap 等），轉為結構化資料並寫入 MySQL 資料庫。

撰寫 Python 腳本結合 SQL 進行資料清洗與轉檔，支援測試結果查詢、錯誤對應與報表統計。

## MES 系統規劃

預計導入自建 MES（Manufacturing Execution System），整合測試記錄、模組流程追蹤、品管資訊與財務模組。
- 建立模組化資料結構與功能需求列表
- 預備導入生產即時監控與報工機制
- ⚠️ 「預計」狀態 — 公司是否實際導入待確認，履歷上不宣稱已上線

## 機器學習嘗試

協助團隊回收歷史資料，導入簡易機器學習模型預測產線接單後產出狀況，作為未來訂單管理與產能規劃參考。

## 跨部門溝通

與一線測試人員密切溝通，了解實務瓶頸與流程痛點。擔任部門與現場、部門間橋梁。

## 部門支援

備援角色，學習兩位資深人員的核心工作內容（伺服器端系統設計、測試設備控制程式、介面維護）。

# Predicted Q&A

**Q: 為什麼 2025-08 結束？**
A: 開學前回校全力寫碩論，且暑期同時在統一證券實習，主軸轉到金融 + ZK domain。

**Q: 1 億筆是怎麼算的？**
A: 從 2025-02 到 2025-08 累積處理量，包含所有歷史 log 重 backfill + 即時新增。

# Artifacts

- 內部 Streamlit 查詢介面（confidential）
- ETL pipeline Python 腳本（公司資產）

# Notes

- 2026-05-11 chairman 確認結束日期為 2025-08（不是「現在」）
- 月份出缺勤紀錄在 `Downloads/*森嶸出缺勤_高璿程*.xlsx` 可佐證
