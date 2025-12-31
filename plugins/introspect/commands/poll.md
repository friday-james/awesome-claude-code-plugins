---
allowed-tools: Read, Write, Bash(mkdir:*), Glob
description: Poll for feedback from the shared feedback file
---

## Your task

Check for incoming feedback in the file-based queue.

### Step 1: Get current session ID

The current session can be found from the conversation context or by checking:
```
~/.claude/projects/*/<current-directory-hash>/*.jsonl
```

Look for the most recently modified JSONL in the current project's claude folder.

### Step 2: Check for feedback file

Look for: `~/.claude-bridge-lite/feedback/<this-session-id>.json`

If the session ID is unknown, also check:
```
~/.claude-bridge-lite/feedback/*.json
```

### Step 3: Read and parse feedback

If file exists, read and parse the JSON array.

### Step 4: Display feedback

For each feedback item:
```
=== <TYPE> [<timestamp>] ===
From: <from-session>
<message>
```

### Step 5: Clear after reading

After displaying, either:
- Write an empty array `[]` to clear all
- Or ask user if they want to keep/clear

### Step 6: No feedback case

If no feedback file or empty array:
```
No feedback pending.

Tip: Another session can send feedback with:
/send <message> --to <your-session-id>
```
