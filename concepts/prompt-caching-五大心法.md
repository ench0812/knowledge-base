---
title: Prompt Caching 五大心法
source: https://www.ithome.com.tw/news/174022
date: 2026-02-27
tags: [prompt-caching, cost-optimization, claude-code, architecture]
type: concept
---

# Prompt Caching 五大心法（Claude Code 團隊）

來源：Claude Code 核心工程師 Thariq Shihipar

## 心法 1：前綴匹配（Prefix Match）
- 快取靠「前綴完全一致」命中
- 越不會變的內容放越前面：System Prompt → 工具清單 → 專案規範 → Session 狀態 → 對話
- ⚠️ system prompt 加時間戳或工具順序不固定會讓快取全部失效

## 心法 2：用對話訊息更新狀態，不改 System Prompt
- 狀態變更用 `<system-reminder>` 標籤放在下一輪對話中
- 不要為了更新資訊去改 system prompt

## 心法 3：不要中途切換模型
- 快取與模型綁定，切模型 = 快取全部重建
- 需要不同模型時用 sub-agent，不要切主對話的模型

## 心法 4：工具清單要固定
- 不在中途新增/移除/重排工具
- 把「模式切換」設計成工具（如 EnterPlanMode），而不是改 system prompt
- 大量工具用延遲載入（stub → 真正使用時才載入完整定義）

## 心法 5：壓縮上下文時維持前綴一致
- Compaction 時用 Cache-Safe Forking：保持相同前綴，只在最後加摘要指令
- 避免另開 request 用不同 system prompt

## 對我們的影響（2026-02-27 評估）
- **目前不需調整**：免費模型不計費，快取優化無成本收益
- **主 session（Opus）**：由 OpenClaw 框架控制，我們無法直接操作
- **未來參考**：回到付費模型或 MiniMax 支援快取時，考慮讓同類任務共享 session