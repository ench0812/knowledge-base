---
title: 認知系統進化路線圖
status: active
priority: high
created: 2026-02-27
tags: [architecture, evolution, long-term]
date: 2026-03-04
type: project
---

# 認知系統進化路線圖

> 目標：把方法論不斷轉化為實作能力，每次迭代都要有可驗證的產出。

## 參考資料（知識三角）
- [[agentic-cognitive-system]] — 架構層：怎麼組織 agent
- [[ai-memory-system-design]] — 記憶層：怎麼記住東西（三核心問題）
- [[prompt-caching-五大心法]] — 效能層：怎麼省錢

## 迭代計畫

### Round 1: 結構化輸出 + 驗證
- [ ] 發文類 prompt 改為強制 JSON 輸出
- [ ] 寫驗證腳本 `validate-output.py` 檢查 schema
- [ ] 發文流程：生成 JSON → 驗證 → 寫入待審區
- **驗收標準**：gpt-4o 產出的發文 100% 符合 schema

### Round 2: Review Agent
- [ ] 設計 ReviewerAgent prompt
- [ ] 關鍵任務（發文、交易）加入自動 QA
- [ ] Review 失敗 → 重試或標記人工處理
- **驗收標準**：發文品質提升，低質內容攔截率 > 80%

### Round 3: Memory 分層 + Episode 記錄
- [ ] 定義 Episode schema（goal, decision_path, result, score）
- [ ] 任務完成後自動寫入 Episodic Memory
- [ ] 定期抽象化為 Semantic Memory（模式識別）
- **驗收標準**：可查詢「某類任務的歷史成功率」

### Round 4: 人格向量化 + 演化
- [ ] 定義 PersonalityVector（risk_tolerance, creativity, precision, assertiveness）
- [ ] Munger Observer 反思結果量化回寫
- [ ] 決策時注入人格偏好
- **驗收標準**：可觀察到決策風格隨時間演化

### Round 5: Agent Communication Protocol
- [ ] 定義 ACP v1 JSON schema
- [ ] 重構 agent 間通訊為結構化協議
- [ ] 加入 intent routing
- **驗收標準**：所有 agent 通訊走 ACP，零自然語言交接

## 節奏
- **每日 15:00**：Opus 深度研究（啃一個議題，產出寫入 vault）
- **每週日 16:00**：研究落地（把該週研究成果轉化為系統實際改進）

## 設計原則
1. **每輪迭代獨立可驗證** — 不等全部做完才能用
2. **先用 prompt engineering 實現，再考慮寫 code** — 最小成本驗證
3. **結構化 > 自然語言** — Agent 間永遠用 JSON
4. **回饋閉環** — 每個改進都要能測量效果
5. **研究→落地閉環** — 研究不是為了研究，每週必須產出可量化的系統改進

## 深度研究議題（持續探索）
- [ ] MiniMax 模型評估（免費替代 gpt-4o）
- [ ] OpenClaw prompt caching 支援度
- [ ] Event-Driven Agent Bus 可行性
- [ ] Personality drift 長期追蹤方法
- [ ] 自動 prompt mutation（meta-learning）
- [ ] 記憶動態驚訝度閾值 — 怎麼判斷什麼值得記？
- [ ] 記憶 confidence 欄位 — 如何在 LanceDB Pro 裡加元資料？
- [ ] 矛盾偵測機制 — 新記憶與舊記憶衝突時怎麼處理？
- [ ] 知識分層可遷移性標籤 — 原則/模式/實作/情境四層
- [ ] 記憶信噪比優化 — 清理 LanceDB 裡的低價值記憶