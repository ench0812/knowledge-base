---
created: 2026-02-14
source: https://github.com/microsoft/agent-lightning
tags: [AI, RL, agent-training, microsoft]
date: 2026-03-04
type: concept
---

# Agent Lightning ⚡

微軟開源的 AI agent 訓練框架。

## 核心概念
- **零代碼改動**把現有 agent 變成可用 RL 優化的
- 支援任何框架（LangChain、OpenAI SDK、AutoGen 等）
- 可選擇性優化多 agent 系統中的單一 agent
- 演算法：RL、自動 Prompt 優化、SFT

## 對 Mickey 的啟發
- 收集 trajectory（成功/失敗的操作序列）→ 用 RL 優化
- 投資判斷、工具使用、寫作品質都可以用這方式迭代
- 與 記憶衰減系統 互補：記憶系統記住經驗，RL 系統優化行為
- 需要實戰數據量夠大才有意義，先觀望

## 技術要點
- Trajectory Level Aggregation — 加速訓練
- 解決 Retokenization Drift — vLLM 相容 API 的 token ID 回傳問題
- 論文：arXiv 2508.03680

## 狀態
📝 概念吸收，暫不安裝