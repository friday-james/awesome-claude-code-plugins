---
allowed-tools: Task, Read, Bash(mkdir:*), Bash(find:*), Bash(wc:*), Bash(sleep:*), Bash(test:*), Bash(cat:*), Bash(echo:*)
description: Start introspection - watch current session and poll for feedback (user)
---

## Your task

Spawn TWO subagents to monitor this session. When agents complete, you'll get a notification - run `/introspect` again to restart monitoring.

### Step 1: Setup

```bash
mkdir -p ~/.claude-bridge-lite/feedback
```

### Step 2: Find current session

```bash
find ~/.claude/projects -name "*.jsonl" -type f -printf '%T@ %p\n' 2>/dev/null | sort -rn | head -1 | cut -d' ' -f2-
```

### Step 3: Spawn BOTH subagents in parallel

**Agent 1 - Watcher:**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  SESSION WATCHER - Monitor for new activity

  Session: <SESSION_PATH>

  1. Get initial line count: wc -l <SESSION_PATH>
  2. Wait 30 seconds
  3. Get new line count
  4. If count increased:
     - Report: === ACTIVITY DETECTED === with summary of new content
     - Complete immediately
  5. If no change, repeat (max 10 times = 5 min)
  6. After 10 checks, complete with "No new activity"
```

**Agent 2 - Poller:**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  FEEDBACK POLLER - Check for incoming messages

  Feedback file: ~/.claude-bridge-lite/feedback/<SESSION_ID>.json

  1. Check if file exists and read it
  2. If non-empty array found:
     - Report: === FEEDBACK RECEIVED === with message content
     - Clear file: echo '[]' > <file>
     - Complete immediately
  3. If empty/missing, wait 10 seconds and repeat (max 20 times = 3 min)
  4. After 20 checks, complete with "No feedback received"
```

### Step 4: Display

```
INTROSPECTION STARTED
=====================
Session ID: <id>

Watcher: Monitoring for ~5 min
Poller: Listening for ~3 min

Run /introspect again when agents complete.
```

### Important

Spawn BOTH agents in parallel with `run_in_background: true`.
