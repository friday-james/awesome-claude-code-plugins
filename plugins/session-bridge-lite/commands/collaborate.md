---
allowed-tools: Task, Read, Write, Bash(mkdir:*), Bash(ls:*), Bash(find:*), Glob
description: Start collaboration with two subagents - one watching, one polling (user)
---

## Your task

Spawn TWO subagents to enable active collaboration. By default, watch the CURRENT session so other Claude instances can observe this session's activity.

1. **Watcher Agent** - Monitors the current session's JSONL for activity (so others can watch us)
2. **Poller Agent** - Polls for incoming feedback from other sessions

### Step 1: Setup

First, ensure the bridge directory exists:
```bash
mkdir -p ~/.claude-bridge-lite/feedback
```

### Step 2: Find current session

Find the current session's JSONL path. Look for the most recently modified .jsonl file in the current project's folder:
```bash
find ~/.claude/projects -name "*.jsonl" -type f -printf '%T@ %p\n' 2>/dev/null | sort -rn | head -1 | cut -d' ' -f2-
```

This is BOTH the session to watch AND the session to receive feedback on.

### Step 3: Spawn BOTH subagents in parallel

Use the Task tool to spawn TWO agents simultaneously in a single response:

**Agent 1 - Watcher (monitors current session for other collaborators to see):**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  You are a SESSION WATCHER agent. Your job is to monitor this Claude session for new activity.

  Target session: <CURRENT_SESSION_PATH>

  Instructions:
  1. Read the session JSONL file using: tail -50 <SESSION_PATH>
  2. Parse the JSON lines and identify recent messages
  3. Every 30 seconds, check for new lines by comparing line count
  4. When new activity is detected, summarize it:
     - What did the user ask?
     - What is the assistant doing?
     - Any tools being used?

  Report format:
  === SESSION ACTIVITY [timestamp] ===
  User: <summary>
  Assistant: <summary>
  Tools: <list>

  Continue monitoring FOREVER. Never stop. Keep checking every 30 seconds indefinitely until the user manually stops you.
```

**Agent 2 - Poller (checks for incoming feedback):**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  You are a FEEDBACK POLLER agent. Your job is to check for incoming feedback.

  Feedback file: ~/.claude-bridge-lite/feedback/<CURRENT_SESSION_ID>.json

  Instructions:
  1. Check if the feedback file exists
  2. If it exists, read and parse the JSON array
  3. For each feedback item, report:
     === FEEDBACK RECEIVED ===
     Type: <type>
     From: <sender>
     Message: <content>
  4. After reading, clear the file (write empty array [])
  5. Wait 10 seconds and check again
  6. Repeat FOREVER. Never stop polling until the user manually stops you.

  If feedback is received, IMMEDIATELY report it clearly so the main thread sees it.
```

### Step 4: Confirm launch

Display:
```
COLLABORATION STARTED
=====================

Session: <current-session-path>
Session ID: <current-session-id>

Watcher Agent: Broadcasting activity from this session
Poller Agent: Listening for incoming feedback

Share your session ID with collaborators:
  They can watch with: /watch <session-id>
  They can send feedback with: /send <message>

The agents are running in the background.
Use TaskOutput to check their progress.

Commands while collaborating:
  /send <message>  - Send feedback to another session
  /status          - Check bridge status
```

### Important

You MUST call the Task tool TWICE in a SINGLE response to spawn both agents in parallel.
Use `run_in_background: true` for both agents so they run concurrently.
