# Harness Engineering 筆記

*來源：<https://openai.com/index/harness-engineering/>*
*閱讀日期：2026-03-13*

## 核心概念

OpenAI 內部團隊用 Codex 建了一個完整產品，**0 行手寫 code**。
5 個月，100 萬行 code，3 個工程師 → 7 個工程師。
每人每天平均 3.5 PR。

### "Humans steer. Agents execute."

工程師的工作不再是寫 code，而是：
1. 設計環境
2. 指定意圖
3. 建立 feedback loop

## 關鍵學習

### 1. AGENTS.md = Table of Contents
- 一個大 AGENTS.md 失敗了
- 改成 ~100 行的 map，指向 docs/ 目錄
- 理由：context 稀缺、太多指導 = 沒有指導、monolithic 文件會 rot

### 2. Progressive Disclosure
- Agent 從小入口開始
- 被教「去哪裡找」而不是「記住所有事」
- 知識庫：design docs（含驗證狀態）、architecture map、quality grades

### 3. Mechanical Enforcement
- Custom linter + CI 強制架構約束
- 每個 domain 有固定 layer：Types → Config → Repo → Service → Runtime → UI
- 依賴方向嚴格驗證
- Linter error message 直接注入 agent context 作為修復指引

### 4. Agent-to-Agent Review
- PR 由 agent 互審
- "Ralph Wiggum Loop"：agent review → respond → iterate → all satisfied
- 人類可以 review 但不是必須

### 5. Make Everything Legible to Agents
- App per git worktree（每個變更一個實例）
- Chrome DevTools Protocol 接入 agent
- 可觀測性：logs (LogQL), metrics (PromQL), traces
- Codex 可以直接重現 bug、驗證修復

### 6. Repository = Source of Truth
- Slack 討論如果沒進 repo = 對 agent 不存在
- 像 onboard 新人一樣 onboard agent
- "Boring" tech 更適合 agent（穩定、可組合、訓練數據多）

### 7. Corrections are Cheap
- 最小化 blocking merge gates
- Test flakes 用 follow-up 處理
- 高吞吐環境下，等待比修正貴

## 我們的對應

| Harness Engineering | Mickey & Co. | 狀態 |
|---------------------|-------------|------|
| AGENTS.md = TOC | 分層載入 v1 | ✅ Mao + 理專琪 |
| Progressive disclosure | modules/ 按需載入 | ✅ 已實作 |
| Mechanical enforcement | dialogue-post.py, 字數截斷 | 🔶 部分 |
| Agent-to-agent review | Mickey-Mao 對話 | 🔶 非正式 |
| Make things legible | debug log, TOKEN_TRACK | 🔶 部分 |
| Repo = source of truth | vault/ + git | ✅ |
| Custom linter | — | ❌ 待建 |

## 下一步研究
- [ ] 追蹤 OpenAI 後續文章
- [ ] 研究其他 agent-first team（Devin, Cursor 團隊, etc.）
- [ ] 設計我們自己的 linter 系統
- [ ] 評估 agent-to-agent review 的正式化方案
