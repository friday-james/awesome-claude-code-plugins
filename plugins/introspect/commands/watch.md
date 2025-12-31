---
allowed-tools: Read, Glob, Bash(ls:*), Bash(tail:*), Bash(wc:*)
description: Watch a Claude session's JSONL file
---

## Your task

Watch another Claude Code session by reading its JSONL file directly.

### Step 1: Find session files

List available Claude session files:
```bash
ls -lt ~/.claude/projects/*//*.jsonl 2>/dev/null | head -20
```

Or use Glob to find them:
```
~/.claude/projects/*/*.jsonl
```

### Step 2: Read the session

When user provides a session path (or index from the list), read the JSONL file:

1. First check how many lines: `wc -l <path>`
2. Read the last 50 lines to see recent activity: `tail -50 <path>`
3. Or use the Read tool with offset for the last portion

### Step 3: Parse and display

Each line in the JSONL is a JSON object. Look for entries with:
- `"type": "user"` - User messages
- `"type": "assistant"` - Claude responses
- `"message"` field contains the actual content

Display a summary:
```
SESSION: <session-id>
LAST ACTIVITY: <timestamp>

Recent messages:
[USER]: <message preview>
[ASSISTANT]: <response preview>
...
```

### Step 4: Store watch state

Create/update a watch state file at `~/.claude-bridge-lite/watched.json`:
```json
{
  "watched": ["<session-path>"],
  "lastRead": {"<session-path>": <line-number>}
}
```

This lets other commands know what we're watching.
