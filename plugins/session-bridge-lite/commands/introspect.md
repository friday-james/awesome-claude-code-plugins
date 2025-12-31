---
allowed-tools: Task, Read, Bash(mkdir:*), Bash(find:*), Bash(tail:*), Bash(sleep:*), Bash(test:*), Bash(cat:*), Bash(echo:*)
description: Start introspection - watch current session and poll for feedback (user)
---

## Your task

Spawn TWO subagents to monitor this session.

### Step 1: Setup

```bash
mkdir -p ~/.claude-bridge-lite/feedback
```

### Step 2: Find current session

```bash
find ~/.claude/projects -name "*.jsonl" -type f -printf '%T@ %p\n' 2>/dev/null | sort -rn | head -1 | cut -d' ' -f2-
```

### Step 3: Spawn BOTH subagents in parallel (use model: haiku)

**Agent 1 - Watcher:**
```
subagent_type: general-purpose
model: haiku
run_in_background: true
prompt: |
  SESSION WATCHER - Stream with tail -f

  Session: <SESSION_PATH>

  Run: tail -f <SESSION_PATH>

  This streams new lines as they're added. Analyze each line as it comes in.
  Summarize activity (user messages, assistant actions, tool calls).
  Run forever.
```

**Agent 2 - Poller:**
```
subagent_type: general-purpose
model: haiku
run_in_background: true
prompt: |
  FEEDBACK POLLER - Run forever

  Feedback file: ~/.claude-bridge-lite/feedback/<SESSION_ID>.json

  Loop forever:
  1. Read the feedback file
  2. If non-empty array found:
     - Print: === FEEDBACK RECEIVED === with content
     - Clear file: echo '[]' > <file>
  3. sleep 10
  4. Repeat forever (never exit)
```

### Step 4: Display

```
INTROSPECTION STARTED (haiku, forever)
======================================
Session ID: <id>

Watcher: tail -f (streaming)
Poller: Checking every 10s

Use /tasks to see agents, kill to stop.
```

### Important

Spawn BOTH agents with `run_in_background: true` and `model: haiku`.
They run forever until manually killed.
