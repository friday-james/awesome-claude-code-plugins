---
allowed-tools: Task, Read, Bash(mkdir:*), Bash(find:*), Bash(tail:*), Bash(sleep:*), Bash(test:*), Bash(cat:*), Bash(echo:*)
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

### Step 3: Spawn BOTH subagents in parallel (use model: haiku for efficiency)

**Agent 1 - Watcher:**
```
subagent_type: general-purpose
model: haiku
run_in_background: true
prompt: |
  SESSION WATCHER

  Session: <SESSION_PATH>

  Loop 10 times (every 30 seconds):
  1. Run: tail -100 <SESSION_PATH>
  2. Briefly summarize what you see (last user message, last assistant action)
  3. Sleep 30 seconds
  4. Repeat

  After 10 iterations (~5 min), complete with final summary.
```

**Agent 2 - Poller:**
```
subagent_type: general-purpose
model: haiku
run_in_background: true
prompt: |
  FEEDBACK POLLER

  Feedback file: ~/.claude-bridge-lite/feedback/<SESSION_ID>.json

  Loop 20 times (every 10 seconds):
  1. Check if file exists and read it
  2. If non-empty array: report content, clear file with echo '[]' > <file>, complete
  3. If empty/missing: sleep 10 seconds, repeat

  After 20 checks (~3 min), complete with "No feedback received"
```

### Step 4: Display

```
INTROSPECTION STARTED (haiku)
=============================
Session ID: <id>

Watcher: tail -100 every 30s for ~5 min
Poller: Checking every 10s for ~3 min

Run /introspect again when agents complete.
```

### Important

Spawn BOTH agents in parallel with `run_in_background: true` and `model: haiku`.
