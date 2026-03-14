# s11: Autonomous Agents — 精髓萃取

*Source: shareAI-lab/learn-claude-code s11*
*Motto: "Teammates scan the board and claim tasks themselves" — no need for the lead to assign each one.*

## 核心問題

s09-s10 的 agent 只在被明確指派時才工作。10 個未認領的任務？Lead 必須逐一分配。**不可擴展。**

真正的自主性：agent 自己掃描 task board，認領未分配的任務，完成後再找下一個。

## 核心架構：WORK ↔ IDLE 雙相循環

```
+-------+
| spawn |
+---+---+
    |
    v
+-------+  tool_use    +-------+
| WORK  | <----------- |  LLM  |
+---+---+              +-------+
    |
    | stop_reason != tool_use（或呼叫 idle tool）
    v
+--------+
| IDLE   |  每 5 秒 poll 一次，最多 60 秒
+---+----+
    |
    +---> check inbox → 有訊息？ → 回到 WORK
    |
    +---> scan .tasks/ → 有未認領？ → claim → 回到 WORK
    |
    +---> 60 秒 timeout → SHUTDOWN
```

**關鍵洞見：Agent 不需要 lead 告訴它做什麼。它自己找事做。**

## 三個核心機制

### 1. Idle Polling（空閒輪詢）

Agent 完成工作後不直接退出，而是進入 idle 循環：

```python
def _idle_poll(self, name, messages):
    for _ in range(IDLE_TIMEOUT // POLL_INTERVAL):  # 60s / 5s = 12 次
        time.sleep(POLL_INTERVAL)
        
        # 先檢查 inbox
        inbox = BUS.read_inbox(name)
        if inbox:
            messages.append({"role": "user", "content": f"<inbox>{inbox}</inbox>"})
            return True  # 回到 WORK
        
        # 再掃描 task board
        unclaimed = scan_unclaimed_tasks()
        if unclaimed:
            claim_task(unclaimed[0]["id"], name)
            messages.append({"role": "user",
                "content": f"<auto-claimed>Task #{unclaimed[0]['id']}: {unclaimed[0]['subject']}</auto-claimed>"})
            return True  # 回到 WORK
    
    return False  # timeout → SHUTDOWN
```

### 2. Task Board Scanning（任務掃描）

掃描條件：status == "pending" AND 沒有 owner AND 沒有 blockedBy

```python
def scan_unclaimed_tasks():
    unclaimed = []
    for f in sorted(TASKS_DIR.glob("task_*.json")):
        task = json.loads(f.read_text())
        if (task.get("status") == "pending"
                and not task.get("owner")
                and not task.get("blockedBy")):
            unclaimed.append(task)
    return unclaimed
```

Task claim 有 mutex lock 防止多 agent 同時認領同一任務。

### 3. Identity Re-injection（身份重注入）

Context 壓縮後 agent 可能忘記自己是誰。解法：

```python
if len(messages) <= 3:  # 壓縮後只剩很少的 messages
    messages.insert(0, {
        "role": "user",
        "content": f"<identity>You are '{name}', role: {role}, team: {team_name}. Continue your work.</identity>"
    })
    messages.insert(1, {
        "role": "assistant",
        "content": f"I am {name}. Continuing."
    })
```

## 對 Mickey & Co. 的啟示

### 可以實現的（用現有 OpenClaw 機制）

**共享 Task Board**（`.tasks/` 目錄）：
- 用 JSON 檔案管理任務：`{id, subject, status, owner, blockedBy, description}`
- 放在共享路徑（如 `~/clawd/company/tasks/`）
- 所有 agent 的 cron 在空閒時掃描

**Agent Heartbeat 變成 Idle Scan**：
- 現在：heartbeat 只檢查有沒有事情要做
- 改進：heartbeat 順便掃一下 task board，有未認領的任務就認領

**具體場景**：
1. 茂在 task board 放一個「整理本週對話精華」任務
2. Mao 的下一個 cron 檢查時發現這個任務，認領並執行
3. 執行結果寫回 task JSON，status → completed

### 需要注意的

1. **Race condition**：多 agent 同時掃描可能認領同一任務 → 用 file lock 或 atomic rename
2. **Token 成本**：每次 heartbeat 加上 task scan 會增加 token 開銷 → 可以只在 idle heartbeat 時掃描
3. **任務粒度**：任務描述要夠清楚，agent 才能自主執行

### 不適用的

- **60 秒 idle timeout** — 我們用 cron 驅動，不是 daemon
- **Threading model** — OpenClaw 的 session 管理已經處理了

## 從 s10 到 s11 的變化

| 維度 | s10 | s11 |
|------|-----|-----|
| Tools | 12 | 14（+idle, +claim_task） |
| 自主性 | Lead 指派 | 自組織 |
| 空閒處理 | 無 | Poll inbox + task board |
| 任務認領 | 手動 | 自動認領未分配任務 |
| 身份 | System prompt | + 壓縮後重注入 |
| Timeout | 無 | 60 秒 idle → 自動關機 |

## 金句

> "The agent finds work itself."
> 
> Agent 不應該等著被餵任務。它應該像一個主動的員工——完成手上的事後，看看還有什麼能做的。
