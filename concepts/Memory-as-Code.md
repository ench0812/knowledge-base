---
tags: [AI, memory, research, LISP, S-expression]
created: 2026-03-07
status: active
date: 2026-03-11
type: concept
---

# Memory-as-Code

## 核心概念
記憶不只是被存取的資料，而是帶著自己激活條件的「程式碼」。

靈感：LISP homoiconicity（程式碼即資料，資料即程式碼）

## 關鍵規則（2026-03-07 蒸餾）

> 「讓記憶帶著自己的激活條件，比讓系統在外部查詢效率更高——但前提是激活條件必須是**停機可判定的受限語言**，否則會引入比它解決的更大的問題。」

## 和現有系統的關係
- **A-MEM**：提供語義連結機制，但記憶是被動的
- **MemOS**：提供儲存分層管理，但無執行邏輯
- **Memory-as-Code**：解決「語義主動性」，可作為 MemOS MemCube Payload 格式

## 關鍵規則（2026-03-08 S2 新增）

> 「設計受限語言時，最難的問題不是語言本身的語法，而是語言的*輸入*如何被可靠解析。predicate 能有多精確，取決於 ctx-type 解析管線的品質。」

> 「任何基於使用頻率的優先級系統，都需要衰減機制抵抗馬太效應，否則系統會逐漸固化為少數記憶壟斷的狀態。」

> 「Decay 機制必須明確標示計時起點和時間基準，否則分散式系統的時鐘不同步將導致衰減值失準。」（Kimi K2 發現）

## 設計定稿（v0.1.1 - 2026-03-08）

```lisp
(memory <id>
  :content    <string>     ; 必填：人類可讀
  :type       <enum>       ; entity/fact/decision/preference/episode/skill/rule
  :confidence 0.0-1.0      ; 靜態信度
  :priority   1-10         ; 動態排序（含 priority-decay 防馬太效應）
  :decay      <decay-expr> ; half-life N days | never | immediate
  :predicate  <pred-expr>  ; 受限布林：ctx-contains/ctx-type/time/access 等
  :links      <list>       ; (link :to :rel :weight :condition)
  :tags       <list>
  :meta       <meta-block>); created/author/version/changelog/eviction
```

兩階段召回架構：
1. 向量搜索縮小候選集（Top-50）
2. Predicate eval 精確過濾（O(50) 不是 O(N)）

## 設計草稿（v0.1 - 已更新至 v0.1.1）
```sexp
(memory
  :content "文字內容（人類可讀 fallback）"
  :predicate "(受限布林表達式，停機可判定)"
  :priority 0.8
  :decay (exponential :half-life 30 :unit day)
  :confidence 0.92
  :meta {:created "2026-03-07" :version 1 :author "Mickey"}
  :links ((reinforces "概念A" 0.8) (conflicts-with "概念B" 0.6))
)
```

## 安全原則
1. predicate 必須是受限語言（BPF-like，不允許遞歸）
2. 記憶讀取 ctx 需沙盒隔離（防止隱私側漏）
3. 連結不可遞歸引用（防止連結爆炸）

## 關鍵規則（2026-03-09 S3 新增）

> 「記憶格式的選擇應該以『激活意圖』為準：需要確定性激活用 S-expression，需要語義模糊召回用自然語言。不要把格式一致性當成目標。」

> 「最值得改寫的記憶：core rules（predicate=always）> timed events（decay=condition）> technical decisions（ctx-type filter）。不值得改寫的：pure semantic memories（關係/情感/故事）。」

## S3 實驗結論（2026-03-09）

- 平均 token 開銷：2.8x（episode 5.0x，decision 2.2x）
- 在 100K+ context window 中，overhead 可接受
- 最佳架構：向量 Top-50（模糊）→ predicate filter（精確）→ 注入 10-20 條
- ctx-type 分類器是最脆弱環節，需在 S4 之前設計

## 相關研究
- R032（研究佇列）
- [[LISP-與AI思考語言]]