---
allowed-tools: Task, Read, Write, Bash(mkdir:*), Bash(ls:*), Glob
description: Start collaboration with two subagents - one watching, one polling
---

## Your task

Spawn TWO subagents to enable active collaboration:

1. **Watcher Agent** - Continuously monitors a session's JSONL for new activity
2. **Poller Agent** - Continuously polls the feedback queue and reports messages

### Step 1: Setup

First, ensure the bridge directory exists:
```bash
mkdir -p ~/.claude-bridge-lite/feedback
```

### Step 2: Find target session

If user provided a session path or index, use that.
Otherwise, list available sessions:
```bash
ls -t ~/.claude/projects/*/*.jsonl 2>/dev/null | head -10
```

Ask user which session to collaborate with.

### Step 3: Get current session info

Find the current session's JSONL path to know where to receive feedback.

### Step 4: Spawn BOTH subagents in parallel

Use the Task tool to spawn TWO agents simultaneously in a single response:

**Agent 1 - Watcher:**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  You are a SESSION WATCHER agent. Your job is to monitor a Claude session for new activity.

  Target session: <SESSION_PATH>

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

  Continue monitoring for 5 minutes or until 10 updates, then report final summary.
```

**Agent 2 - Poller:**
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
  6. Repeat for 5 minutes or until 20 checks

  If feedback is received, IMMEDIATELY report it clearly so the main thread sees it.

  When done, provide summary:
  - Total feedback received: N
  - Types: suggestion (x), warning (y), etc.
```

### Step 5: Confirm launch

Display:
```
COLLABORATION STARTED
=====================

Watcher Agent: Monitoring <session-path>
Poller Agent: Listening for feedback

Your session ID: <current-session-id>
Share this with collaborators so they can send you feedback.

The agents are running in the background.
Use TaskOutput to check their progress, or wait for notifications.

Commands while collaborating:
  /send <message>  - Send feedback to watched session
  /status          - Check bridge status
```

### Important

You MUST call the Task tool TWICE in a SINGLE response to spawn both agents in parallel.
Use `run_in_background: true` for both agents so they run concurrently.
