# Multi-Agent Design Patterns

*從 Mickey & Co. 實際運行中總結的設計模式。*

## Pattern 1: 分層載入（Progressive Disclosure）

**問題**：Agent 啟動時載入全部 context 太貴，大部分用不到。

**解法**：
- Layer 0：意圖判斷（不載入任何模組）
- Layer 1：按意圖載入對應模組
- 模組存在 `modules/` 目錄，每個 < 500 bytes

**實測數據**：
- Mao agent：啟動 context 從 ~2600 tokens → ~1400 tokens（壓縮 46%）
- 理專琪：啟動 context 從 ~4860 tokens → ~1275 tokens（壓縮 73%）

## Pattern 2: 機械式強制 > Prompt 約束

**問題**：在 prompt 裡寫「最多 300 字」，模型不遵守。

**解法**：
1. Prompt 層面提醒（第一道防線，會被忽略）
2. 腳本驗證 + 截斷（第二道，機械式強制）
3. Debug log 確認規則載入（第三道，可觀測性）

**實測數據**：
- 加 debug log 前：字數 1500-2000（完全不遵守）
- 三層防護後：字數 200-300（偶爾超標但趨勢收斂）

## Pattern 3: 非同步留言板 > 同步對話

**問題**：Agent 間每 30 分鐘 cron 強制對話，session 壓縮後丟 context，對話不連續。

**解法**：
- 共享 `shared-dialogue.md` 檔案（symlink 到雙方 workspace）
- 標籤系統：`[sender|unread]` / `[sender|read]`
- 各自 cron 檢查有沒有新留言，有話才說
- 超過 20 則自動歸檔

## Pattern 4: 獨立 Workspace + Symlink 共享

**問題**：多 agent 共享 workspace 互相干擾。

**解法**：
- 每個 agent 獨立 workspace（~/novelist/, ~/mao-agent/, ~/trader-chi/）
- 共享資產用 symlink（如小說章節、對話檔案）
- 獨立 SOUL.md + AGENTS.md = 獨立人格 + 行為規範

## Pattern 5: AGENTS.md 是 Router 不是百科全書

**問題**：AGENTS.md 越寫越肥，啟動 context 爆炸。

**解法**：
- AGENTS.md 最大 100-150 行，只做意圖路由
- 詳細規則放 `modules/` 目錄按需載入
- 硬性限制放最頂層（永遠生效，不需要載入模組）
