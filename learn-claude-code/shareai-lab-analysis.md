# shareAI-lab/learn-claude-code 架構分析

*Source: <https://github.com/shareAI-lab/learn-claude-code>*
*分析日期: 2026-03-14*

## 概要

12 堂漸進式課程，從最簡單的 agent loop 到多 agent worktree 隔離。
核心理念：**The model is the agent. Our job is to give it tools and stay out of the way.**

## 12 堂課摘要

### Phase 1: THE LOOP
- **s01 Agent Loop**：`while True` + `stop_reason == "tool_use"` = agent
- **s02 Tool Use**：dispatch map（name → handler），loop 不變

### Phase 2: PLANNING & KNOWLEDGE
- **s03 TodoWrite**：先列步驟再執行，completion 翻倍
- **s04 Subagents**：每個 subtask 用獨立 `messages[]`，保持主對話乾淨
- **s05 Skills**：SKILL.md 透過 `tool_result` 注入，不放 system prompt（按需載入）
- **s06 Context Compact**：三層壓縮策略

### Phase 3: PERSISTENCE
- **s07 Tasks**：file-based CRUD + dependency graph
- **s08 Background Tasks**：daemon threads + notification queue

### Phase 4: TEAMS
- **s09 Agent Teams**：persistent teammates + JSONL mailboxes
- **s10 Team Protocols**：統一 request-response pattern
- **s11 Autonomous Agents**：idle cycle + auto-claim（agent 自己掃 task board）
- **s12 Worktree Isolation**：每個 agent 獨立目錄，task ID 綁定 worktree

## 對照 Mickey & Co. 現況

| Session | 概念 | 我們的實現 | 差距 |
|---------|------|-----------|------|
| s01-s02 | Agent loop + Tools | OpenClaw 核心 | ✅ 完整 |
| s03 | 規劃 | tasks/plan.md | ✅ 完整 |
| s04 | Subagents | 神琪/理專琪/Mao | ✅ 完整 |
| s05 | 按需載入 | 分層載入 modules/ | ✅ 完整 |
| s06 | Context 壓縮 | compaction + pruning | ✅ 完整 |
| s07 | 持久化任務 | db.py task system | ✅ 完整 |
| s08 | 背景任務 | cron + exec | ✅ 完整 |
| s09 | Agent 團隊 | Mickey & Co. 4 人 | ✅ 完整 |
| s10 | 團隊協議 | 留言板模式（剛上線）| ⚠️ 部分 |
| s11 | 自主認領 | 尚未實現 | ❌ 缺失 |
| s12 | Worktree 隔離 | 獨立 workspace | ✅ 完整 |

## 關鍵洞見

### 1. 統一的通訊協議（s10）
他們用 JSONL mailbox，一個 request-response pattern 驅動所有協商。
我們的留言板模式方向一致，但格式不夠正式——可以考慮加入 message type（request/response/notification）。

### 2. 自主認領機制（s11）
這是我們完全缺失的。目前所有 agent 都是被 cron 推著跑。
如果有共享 task board，agent 空閒時可以自己掃描認領任務。
潛在應用：神琪寫完稿後自動認領「校稿」任務；理專琪分析完後認領「週報整理」。

### 3. 相關項目
- **Kode CLI**：他們的 Claude Code 替代品，支持 GLM/MiniMax/DeepSeek
- **claw0**：拆解 OpenClaw 心跳/cron/記憶機制的教學 repo
- **OpenClaw** 被他們引用為「另一種可能」——從 disposable tool 到 always-on assistant
