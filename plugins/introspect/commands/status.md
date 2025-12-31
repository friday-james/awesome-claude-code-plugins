---
allowed-tools: Read, Glob, Bash(ls:*), Bash(wc:*), Bash(cat:*)
description: Show bridge status and session info
---

## Your task

Display comprehensive introspect status.

### Step 1: Find current session

Find the current session's JSONL file:
```bash
ls -t ~/.claude/projects/*/*.jsonl 2>/dev/null | head -5
```

The current project path can be inferred from the working directory.

### Step 2: Check watched sessions

Read `~/.claude-bridge-lite/watched.json` if it exists.

### Step 3: Check feedback queues

List all feedback files:
```bash
ls ~/.claude-bridge-lite/feedback/*.json 2>/dev/null
```

For each, count pending items.

### Step 4: Display status

```
INTROSPECT - STATUS
===================

Current Session:
  Path: <current-session-path>
  ID: <session-id>

Watched Sessions: <count>
  - <session-path-1>
  - <session-path-2>

Feedback Queues:
  <session-id>: <n> pending
  <session-id>: <n> pending

Available Sessions:
  [0] <project> - <session-id> - Modified: <time>
  [1] <project> - <session-id> - Modified: <time>
  ...

Commands:
  /introspect          - Start introspection (watcher + poller)
  /watch <index|path>  - Watch a session
  /send <message>      - Send feedback
  /poll                - Check for feedback
```
