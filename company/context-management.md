# Context Management Policy

*從 OpenAI Harness Engineering 提煉的可執行政策*

## 原則：Map, not Manual

> "Give Codex a map, not a 1,000-page instruction manual."

### AGENTS.md 規範
- **最大 100-150 行**
- 只放：身份、性格摘要、分層載入規則表、禁止事項
- 細節全部在 `modules/` 裡
- 定期巡檢（已有 cron：Context 瘦身巡檢）

### Progressive Disclosure
Agent 啟動時只載入 router。根據意圖判斷，再載入對應模組：
```
收到訊息 → 判斷意圖 → 載入 0-2 個模組 → 回應
```

### Context Budget
| Agent | 啟動上限 | 告警線 | 硬限制 |
|-------|---------|--------|--------|
| Mickey | 3000 tokens | 2500 | 4000 |
| Mao | 1500 tokens | 1200 | 2000 |
| 理專琪 | 1300 tokens | 1000 | 1800 |
| 新 agent | 1000 tokens | 800 | 1500 |

### 禁止
- ❌ 心跳 log 寫入 workspace context 文件
- ❌ 單個 context 文件超過 3000 bytes
- ❌ 重複資訊出現在多個 context 文件中
