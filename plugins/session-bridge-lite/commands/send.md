---
allowed-tools: Read, Write, Bash(mkdir:*), Bash(date:*)
description: Send feedback to a session via shared file
---

## Your task

Send feedback to another session using a file-based queue.

### Feedback file location

Feedback is stored at: `~/.claude-bridge-lite/feedback/<session-id>.json`

### Step 1: Parse the message

Extract from user input:
- **message**: The feedback text
- **type**: suggestion (default), warning, error, or approval

### Step 2: Create feedback directory

```bash
mkdir -p ~/.claude-bridge-lite/feedback
```

### Step 3: Read existing feedback file (if any)

Try to read `~/.claude-bridge-lite/feedback/<target-session-id>.json`

If it exists, parse the JSON array. If not, start with empty array.

### Step 4: Append new feedback

Add to the array:
```json
{
  "timestamp": "<ISO timestamp>",
  "type": "<feedback-type>",
  "message": "<the message>",
  "from": "<sender-session-id or 'unknown'>"
}
```

### Step 5: Write back

Write the updated array to the feedback file.

### Step 6: Confirm

Display:
```
FEEDBACK SENT
=============
To: <session-id>
Type: <type>
Message: <message>
Queue size: <n>
```

### Target session

If no target specified:
1. Check `~/.claude-bridge-lite/watched.json` for watched sessions
2. Use the first watched session as target
3. Or ask user to specify
