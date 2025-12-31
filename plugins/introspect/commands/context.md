---
allowed-tools: Read, Bash(tail:*), Bash(wc:*)
description: Get recent context from a watched session
---

## Your task

Read and summarize recent activity from a watched session's JSONL file.

### Step 1: Get session path

Either from user input or from `~/.claude-bridge-lite/watched.json`

### Step 2: Read recent entries

Use tail to get last N lines (default 20):
```bash
tail -20 <session-path>
```

Or use Read tool with offset if the file is very large.

### Step 3: Parse JSONL

Each line is a JSON object. Extract:
- `type`: "user" or "assistant"
- `message.content`: The actual message content
- `timestamp`: When it occurred
- `uuid`: Message ID

### Step 4: Display formatted context

```
RECENT CONTEXT: <session-id>
============================
Last updated: <most-recent-timestamp>

[<time>] USER:
<message-preview, first 200 chars>

[<time>] ASSISTANT:
<response-preview, first 200 chars>

[<time>] USER:
<message-preview>

...
```

### Step 5: Identify what they're working on

Based on the context, provide a brief summary:
```
SUMMARY:
The session appears to be working on: <brief description>
Recent tools used: <list of tools>
```
