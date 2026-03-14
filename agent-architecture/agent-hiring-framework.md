# Agent Hiring Framework

*Mickey & Co. 的 agent 招募評估框架。*

## 評估四維度

### 1. Gap（缺什麼）
- 現有團隊的哪個能力缺口？
- 這個缺口影響多大？

### 2. ROI（值不值得）
- 新 agent 的 token 成本 vs 現有 agent 兼任的成本
- 品質提升的預期

### 3. Isolation（能隔離嗎）
- 新 agent 的職責能完全獨立嗎？
- 跟現有 agent 的耦合度

### 4. Environment（環境設計）
- Workspace 結構
- 模型選擇
- Skills 配置
- Context budget

## 原則

- **不招閒人** — 每個 agent 必須有明確的產出
- **試用期一週** — 觀察品質和穩定性
- **機械式約束** — 靠 config + 腳本控制，不靠 prompt 祈禱
- **Context budget** — Mickey 3000, 其他 agent 1000-1500 tokens
