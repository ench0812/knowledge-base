# s10: Team Protocols — 精髓萃取

*Source: shareAI-lab/learn-claude-code s10*
*Motto: "Teammates need shared communication rules" — one request-response pattern drives all negotiation.*

## 核心問題

Agent 團隊（s09）能協作但缺乏結構化協調：
- **關機問題**：直接殺 thread 會留下半寫的文件和過時的 config
- **計劃審批**：高風險操作（如重構模組）應該先讓 lead 審核再執行

兩個場景結構完全一樣：一方發送帶 `request_id` 的請求，另一方用同一個 `request_id` 回覆。

## 核心模式：Request-Response FSM

```
[pending] --approve--> [approved]
[pending] --reject---> [rejected]
```

**一個 FSM，兩個應用。** 同一個 pending → approved | rejected 狀態機可以處理任何 request-response 協議。

### 應用 1: Shutdown Protocol

```
Lead                    Teammate
  |                       |
  |--shutdown_req-------->|   req_id: "abc"
  |                       |
  |<--shutdown_resp-------|   req_id: "abc", approve: true
  |                       |
  status → "shutdown"
```

### 應用 2: Plan Approval

```
Teammate                Lead
  |                       |
  |--plan_req------------>|   req_id: "xyz", plan: "重構 auth..."
  |                       |
  |<--plan_resp-----------|   req_id: "xyz", approve: true/false
```

## 通訊基礎設施：JSONL Mailbox

每個 teammate 一個 inbox 檔案（`{name}.jsonl`），append-only 寫入，read 時清空：

```python
# 寫入
msg = {"type": msg_type, "from": sender, "content": content, "timestamp": time.time()}
# 讀取後清空
messages = [json.loads(line) for line in inbox_path.read_text().strip().splitlines()]
inbox_path.write_text("")
```

**Message types**: message, broadcast, shutdown_request, shutdown_response, plan_approval_response

## 關鍵追蹤器

```python
shutdown_requests = {req_id: {"target": name, "status": "pending"}}
plan_requests = {req_id: {"from": name, "plan": text, "status": "pending"}}
```

用 `request_id`（UUID 前 8 碼）關聯請求和回覆。

## 對 Mickey & Co. 的啟示

### 現有差距
我們的 agent 間通訊是非結構化的：
- Mickey ↔ Mao：留言板（shared-dialogue.md）
- Mickey → 神琪/理專琪：cron prompt（單向指令）
- 沒有 request-response 模式

### 可借鑑的
1. **統一 message type**：給留言板加 type 欄位（message / request / notification）
2. **request_id 關聯**：非同步操作的追蹤機制
3. **Plan approval**：高風險操作（如發佈、交易）先提交計劃給 Mickey 審核

### 不需要的
- Shutdown protocol — OpenClaw 有自己的 session 管理
- Threading model — OpenClaw 用 cron + sessions_send，不是 daemon threads

## 從 s09 到 s10 的變化

| 維度 | s09 | s10 |
|------|-----|-----|
| Tools | 9 | 12（+shutdown_req/resp +plan） |
| 關機 | 自然退出 | Request-response handshake |
| 計劃審批 | 無 | Submit/review with approval |
| 關聯追蹤 | 無 | request_id per request |
| 狀態機 | 無 | pending → approved/rejected |
