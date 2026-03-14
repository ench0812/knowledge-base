# Quality Gates Policy

## 原則：Mechanical Enforcement > Prompt Constraints

能自動化檢查的，就不靠 prompt 約束。

## 現有 Quality Gates

### 已實作（Mechanical）
- **dialogue-post.py**：防重複發送，sent-rounds.json 記錄
- **字數驗證**：Step 5 python3 計算字數，超標截斷
- **Context 瘦身巡檢**：每週三 cron 自動檢查
- **Vault 自動修復**：每週三 cron 修復格式問題

### 待實作
- [ ] **AGENTS.md 行數 linter**：超過 150 行自動告警
- [ ] **心跳 log 堆疊偵測**：HEARTBEAT.md 超過 1KB 自動清理
- [ ] **Agent 成功率 dashboard**：從 cron state 自動計算
- [ ] **Context size tracker**：每日記錄各 agent 啟動 token 數

## 品質層級

### Level 1：自動攔截（必須）
- 字數超標 → 截斷
- 重複發送 → 攔截
- Context 超限 → 告警

### Level 2：自動修復（應該）
- 格式錯誤 → vault-autofix
- 過期文件 → doc-gardening
- 累積 log → 自動清理

### Level 3：人工審查（可選）
- 對話品質 → 每週六分析 cron
- 發文品質 → 抽樣 review
- 策略效果 → 季度考核
