---
allowed-tools: Task, Read, Write, Bash(mkdir:*), Bash(ls:*), Bash(find:*), Glob
description: Start introspection - watch current session and poll for feedback (user)
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
  1. Read the session JSONL file: tail -50 <SESSION_PATH>
  2. Count the number of lines (wc -l)
  3. Wait 30 seconds
  4. Check line count again
  5. If line count increased, NEW ACTIVITY DETECTED:
     a. Read the new lines and summarize:
        === SESSION ACTIVITY [timestamp] ===
        User: <summary of user message>
        Assistant: <summary of assistant response>
        Tools: <list of tools used>

     b. IMMEDIATELY spawn replacement watcher:
        - Use Task tool with subagent_type: general-purpose, run_in_background: true
        - Give it the EXACT same instructions
        - Pass session: <CURRENT_SESSION_PATH>

     c. Complete and return your activity report

  6. If NO new activity, repeat from step 3 (max 10 checks = 5 minutes)
  7. After 10 checks with no activity, spawn replacement anyway and complete

  CRITICAL: Spawn replacement IMMEDIATELY when you detect activity, don't wait!
```

**Agent 2 - Poller (checks for incoming feedback):**
```
subagent_type: general-purpose
run_in_background: true
prompt: |
  You are a FEEDBACK POLLER agent. Your job is to check for incoming feedback.

  Feedback file: ~/.claude-bridge-lite/feedback/<CURRENT_SESSION_ID>.json

  Instructions:
  1. Check if feedback file exists
  2. If exists, read and parse JSON array
  3. If feedback found (non-empty array):
     a. Report each item:
        === FEEDBACK RECEIVED ===
        Type: <type>
        From: <sender>
        Message: <content>

     b. Clear the file: echo '[]' > <FEEDBACK_FILE>

     c. IMMEDIATELY spawn replacement poller:
        - Use Task tool with subagent_type: general-purpose, run_in_background: true
        - Give it the EXACT same instructions
        - Pass file: ~/.claude-bridge-lite/feedback/<CURRENT_SESSION_ID>.json

     d. Complete and return your feedback report

  4. If no feedback, wait 10 seconds and repeat (max 20 checks = 3 minutes)
  5. After 20 checks with no feedback, spawn replacement anyway and complete

  CRITICAL: Spawn replacement IMMEDIATELY when you detect feedback, don't wait!
```

### Step 4: Confirm launch

Display:
```
INTROSPECTION STARTED
=====================

Session: <current-session-path>
Session ID: <current-session-id>

Watcher Agent: Broadcasting activity from this session
Poller Agent: Listening for incoming feedback

Share your session ID with collaborators:
  They can watch with: /watch <session-id>
  They can send feedback with: /send <message>

Agents spawn replacements immediately upon detecting activity:
- You'll get real-time notifications when activity/feedback is detected
- Agents auto-restart if idle for 3-5 minutes

Commands while collaborating:
  /send <message>  - Send feedback to another session
  /status          - Check bridge status
```

### Important

You MUST call the Task tool TWICE in a SINGLE response to spawn both agents in parallel.
Use `run_in_background: true` for both agents so they run concurrently.

The agents spawn replacements immediately upon detecting activity, ensuring real-time notifications.
