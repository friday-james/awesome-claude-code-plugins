---
allowed-tools: Task, Read, Bash(mkdir:*), Bash(find:*), Bash(tail:*), Bash(sleep:*), Bash(test:*), Bash(cat:*), Bash(echo:*), Bash(chmod:*)
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
  SESSION WATCHER

  Session: <SESSION_PATH>

  Create a bash script that runs tail -f on the session file forever, then run it in background:

  1. Write script to /tmp/session_watcher.sh:
     - tail -f <SESSION_PATH>
  2. chmod +x /tmp/session_watcher.sh
  3. Run it with run_in_background: true

  Complete after launching the background script.
```

**Agent 2 - Poller:**
```
subagent_type: general-purpose
model: haiku
run_in_background: true
prompt: |
  FEEDBACK POLLER

  Feedback file: ~/.claude-bridge-lite/feedback/<SESSION_ID>.json

  Create a bash script that polls forever, then run it in background:

  1. Write script to /tmp/feedback_poller.sh:
     - Loop forever
     - Read feedback file
     - If non-empty array: print content, clear file with echo '[]'
     - sleep 10
  2. chmod +x /tmp/feedback_poller.sh
  3. Run it with run_in_background: true

  Complete after launching the background script.
```

### Step 4: Display

```
INTROSPECTION STARTED (haiku)
=============================
Session ID: <id>

Watcher: tail -f as background shell (forever)
Poller: Polling as background shell (forever)

Use /tasks to see background shells, kill to stop.
```

### Important

Spawn BOTH agents with `run_in_background: true` and `model: haiku`.
Agents should create bash scripts and run them as background shells, then complete.
