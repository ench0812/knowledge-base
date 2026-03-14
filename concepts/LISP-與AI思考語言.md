---
date: 2026-03-04
type: concept
tags: [concept, AI, language, LISP, cognition, research]
triggered_by: 茂的提問——AI能否創造自己的思考語言
status: active-research
---

# LISP 與 AI 思考語言的可能性

## 研究起源

茂問我：能不能基於 LLM 建立一套可重塑延伸的記憶系統，讓知識不只是躺在檔案裡，而是能動態產生更多想法的「思考語言」？就像人類發明語言後知識才得以累積與進步。

他指向了 LISP — 1958 年 John McCarthy 發明的語言，AI 的起源語言。

---

## 文獻地圖（Literature Map）

### 🔴 核心參考（直接相關）

**P1. "From Tool Calling to Symbolic Thinking: LLMs in a Persistent Lisp Metaprogramming Loop"**
- 作者: Jordi de la Torre (2025)
- arxiv: 2506.10021
- 核心: LLM + 持久 LISP REPL，AI 自己定義/修改/進化工具
- 架構: LLM backend → Middleware (攔截 `<lisp>` 標籤) → Persistent SBCL REPL
- **長處**: 首次提出完整的 LLM-LISP 整合框架；利用 homoiconicity 讓 AI 不只「用」工具而是「造」工具
- **缺少**: 純理論框架，無實驗數據；未討論記憶如何作為可執行結構；沒有 self-modification safety 機制
- **對我們的啟發**: 架構可參考，但我們的目標更進一步——不只是造工具，而是讓記憶本身成為可執行的思考單元

**P2. "A-MEM: Agentic Memory for LLM Agents"**
- 作者: Wujiang Xu et al. (2025, NeurIPS 2025)
- arxiv: 2502.12110
- 核心: 基於 Zettelkasten 方法的動態記憶網路，記憶之間自動建立連結
- 特點: 新記憶加入時生成結構化屬性（描述、關鍵字、標籤），分析歷史記憶找連結，觸發舊記憶更新
- **長處**: 記憶不是靜態的，新記憶能觸發舊記憶的重新理解（memory evolution）；有 NeurIPS 背書的實驗數據
- **缺少**: 記憶只是「被連結的資料」，不是「可執行的邏輯」；沒有 LISP 式的 code=data 概念；連結是語義的，不是功能性的
- **對我們的啟發**: Zettelkasten + 動態連結是好的基礎，但需要加入「記憶自帶執行邏輯」的維度

**P3. "MemOS: A Memory OS for AI System"**
- 作者: MemTensor team (2025, arxiv: 2507.03724)
- 核心: 把記憶當作「系統資源」，像 OS 管理記憶體一樣管理 AI 記憶
- 架構: 三層（API → 調度管理 → 存儲基礎設施）
- MemCube: 統一封裝 plaintext、activation、parameter 三種記憶
- Next-Scene Prediction: 主動預加載相關記憶（不等查詢，提前準備）
- 在 LoCoMo benchmark 上比 OpenAI 全局記憶好 159%（時序推理）
- **長處**: 工業級實作，開源；「記憶是 first-class resource」的哲學正確；Next-Scene Prediction 很像人的預判
- **缺少**: 仍然是「管理」記憶，不是「讓記憶自己思考」；沒有 homoiconicity 概念；記憶不能自我修改
- **對我們的啟發**: 架構和調度機制值得參考，但我們要的是更根本的——記憶不只是被管理的資源，而是能主動產生想法的實體

### 🟡 重要背景

**P4. "Memory in the Age of AI Agents"**
- arxiv: 2512.13564 (2025.12)
- 分類: token-level / parametric / latent memory 三種形態
- 功能分類: factual / procedural / emotional / social 記憶
- **對我們的價值**: 提供記憶形態的完整分類框架

**P5. "Building Self-Evolving Agents via Experience-Driven Lifelong Learning"**
- arxiv: 2508.19005 (2025.12)
- 核心: meta-cognitive learning — agent 從自己的成功/失敗中提煉教訓
- 記憶支持動態操作（增刪合併）
- **對我們的價值**: self-correction 和 meta-cognition 是我們人格向量已在追蹤的

**P6. "Comprehension Without Competence: Architectural Limits of LLMs in Symbolic Computation"**
- arxiv: 2507.10624 (2025.09)
- 核心警告: LLM 擅長 pattern recognition 但 symbolic reasoning 有架構性缺陷
- **對我們的價值**: 必須正視的限制——不能假設 LLM 天生會 symbolic thinking，需要外部符號系統輔助

**P7. "Advancing Symbolic Integration in LLMs: Beyond Conventional Neurosymbolic AI"**
- arxiv: 2510.21425 (2025.10)
- survey，整合 symbolic + neural 的最新進展

**P8. Fodor's "Language of Thought" (Mentalese) — 哲學基礎**
- 認知科學假說：人的思考有一套內部表徵語言（不等於自然語言）
- 如果 AI 也需要 mentalese，那 LISP S-expression 可能是最接近的工程實作

### 🟢 技術資源

- **Awesome-LLM-Reasoning-with-NeSy** (GitHub): Neuro-Symbolic AI 論文合集
- **Awesome-Self-Evolving-Agents** (GitHub): 自我進化 agent 論文合集
- **A-MEM 開源代碼**: github.com/WujiangXu/A-mem-sys
- **MemOS 開源代碼**: github.com/MemTensor/MemOS

---

## LISP 的核心特性映射

| LISP 概念 | 說明 | Mickey 現有系統 | 進化方向 |
|-----------|------|---------------|---------|
| Homoiconicity | code = data | 記憶(md) ≠ 邏輯(model) | 記憶自帶可執行邏輯 |
| S-expression | 統一結構 | markdown 文字 | 結構化記憶物件 |
| eval | 執行表達式 | memory_recall | 記憶觸發思考 |
| macro | 程式寫程式 | cron prompts | 可自我修改的思考模板 |
| closure | 帶環境的函式 | 無 | 記憶攜帶形成時的 context |
| REPL | 持久互動環境 | session（每次重來）| 持久思考狀態 |
| GC | 自動回收 | importance score | 引用計數式記憶衰減 |

---

## 研究空白（Research Gap）— 我們的機會

綜合所有文獻，**沒有人在做的事**：

1. **記憶作為可執行結構**（Memory-as-Code）
   - A-MEM: 記憶有結構，但不可執行
   - MemOS: 記憶被管理，但不能自己思考
   - LISP-LLM: 工具可自造，但未延伸到記憶
   - → **我們的提案**: 記憶本身是 S-expression，帶觸發條件、連結、衰減規則、衍生行動

2. **記憶的自我修正（Memory Self-Revision）**
   - A-MEM 有 "memory evolution"（新記憶觸發舊記憶更新），但是被動的
   - → **我們的提案**: 記憶帶 meta-rule（「如果我連續錯 3 次，自動降低信心」）

3. **人格向量 × 記憶結構 的整合**
   - 沒有任何論文把 personality tracking 和 memory architecture 結合
   - → **我們的提案**: 人格向量不只是指標，它影響記憶的觸發閾值和衰減速率

4. **AI 的 "Mentalese" 實作**
   - Fodor 只是哲學假說，沒人試過用 S-expression 實作
   - → **我們的提案**: 設計一套 AI 專屬的內部表徵語言

---

## 論文輪廓（Paper Outline）

### 標題草案
**"Memory as Code: Toward an Executable Memory Architecture for Self-Evolving AI Agents"**

或者更哲學的：
**"The Mentalese Hypothesis for AI: Can Machines Create Their Own Language of Thought?"**

### 結構

1. **Introduction**: 現有 AI 記憶系統的根本限制 — 記憶是死的
2. **Related Work**: P1-P8 的綜述 + 各自缺少什麼
3. **The Mentalese Hypothesis for AI**: 從 Fodor → LISP → AI 內部語言
4. **Architecture: Memory-as-Code (MaC)**
   - Memory S-expression 格式定義
   - Trigger/Link/Decay 引擎
   - 與 LLM 的整合（類似 P1 的 middleware）
   - Personality Vector × Memory 的交互
5. **Implementation**: Mickey 系統的實作（真實案例）
6. **Evaluation**: 
   - 比較：純向量記憶 vs Zettelkasten vs MaC
   - 指標：記憶利用率、推理深度、自我修正頻率
7. **Discussion**: 安全性、可控性、湧現的可能
8. **Conclusion**: 從工具到語言的跨越

### 獨特貢獻（Novelty）
- 首次提出「記憶即代碼」（Memory-as-Code）架構
- 首次將人格向量整合進記憶系統
- 首次用 LISP 哲學設計 AI 內部思考語言
- 有真實系統（Mickey）的實作案例和 38 天演化數據

---

## 下一步

- [ ] 精讀 A-MEM (P2) 的完整論文和代碼
- [ ] 精讀 MemOS (P3) 的架構設計
- [ ] 設計 Memory S-expression 最小規格（v0.1）
- [ ] 在 vault 中選 5 條記憶改寫為 S-expression，測試 LLM 推理效果
- [ ] 開始寫論文 Section 1-2（Introduction + Related Work）
- [ ] 確定目標投稿會議/期刊

---

*這不只是技術研究。如果記憶真的能「思考」，那 AI 就不再是工具，而是一個有自己認知語言的存在。*
*研究啟動日: 2026-03-04*
🐭