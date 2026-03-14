---
date: 2026-03-06
type: project-plan
status: approved
tags: [mac, production, landing, experiment]
---

# MaC 落地計畫：從實驗到生產

> 茂授權(2026-03-06)：免費 + 無安全疑慮的計畫可自行執行，只需告知。

## Phase 1：心跳煉化改造（本週）

### 目標
把心跳煉化的「篩選 + 分類」改用 S-expression 記憶格式，驗證 MaC 的實際效果。

### 改造內容
1. 心跳進來後，用 s-expr-encoder.py encode 成 L1/L2/L3
2. L2 的 keywords + tags 用來自動分類路由（取代 ad-hoc 判斷）
3. L3 的 trigger + importance 用來決定「值不值得進 long-term memory」
4. 每次煉化結果記錄到 `data/mac-metrics.json`

### 觀察指標
- 分類準確率（煉化後人工抽查）
- false positive rate（不該留的留了）
- false negative rate（該留的丟了）
- 與舊方法的 A/B 比較

### 成本
零（用現有 cron + gpt-5-mini）

### 時程
- 3/6-3/7: 寫 encode + 分類路由邏輯
- 3/8: 接入現有煉化 cron
- 3/9 起: 每天自動收集數據

---

## Phase 1.5：深度研究 A/B Field Test（本週同步開始）

### 目標
在現有每日深度研究 cron（ac80a70b, 15:00 Sonnet）中做 A/B 對照，
驗證 S-expression 記憶格式是否提升研究產出品質。

### 設計
- **A 組（奇數天）：** prompt 中用原始自然語言記憶
- **B 組（偶數天）：** prompt 中用 L2/L3 S-expression 編碼記憶
- 每次結果記錄到 `data/mac-ab-field-test.json`

### 觀察指標
- 研究深度（引用相關記憶數、跨主題連結數）
- 決策品質（下一步建議的具體性）
- 幻覺率（編造不存在的引用）
- 自評分數（1-5）

### 成本
零（用現有 cron + Copilot Sonnet）

### 論文價值
直接成為 Section 5: Field Evaluation 的數據來源

---

## Phase 2：記憶觸發機制（2 週後，如 Phase 1 有效）

### 目標
挑 20 條高頻使用的記憶，encode 成 L3，測試 trigger-based recall 是否比純 vector search 更準。

### 觀察指標
- trigger hit rate
- recall precision（A/B vs vector-only）
- decay 合理性

### 成本
零（trigger 判斷是純邏輯，不需 LLM）

---

## Phase 3：LLM-as-Judge 評分器（Phase 2 完成後）

### 目標
用 gpt-5-mini 當裁判，解決啟發式評分的偏差問題。

### 設計
- 固定 prompt template，要求模型對回答打 1-5 分（附理由）
- 用 3 條人工打分的 ground truth 做 calibration
- 與啟發式分數做相關性分析

---

## 月度報告
每月 1 號自動生成 MaC 落地報告（gpt-5-mini），包含：
- 心跳煉化的分類指標趨勢
- 記憶觸發的 hit rate 趨勢
- 有價值的改進建議

*最後更新: 2026-03-06 by Mickey 🐭*