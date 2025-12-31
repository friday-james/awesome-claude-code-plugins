---
description: Start watching a Claude session for cross-session collaboration
author: njamez
version: 1.0.0
---

# Watch Command

Start watching a Claude Code session to receive feedback and collaborate across sessions.

## Usage

Run `/watch <index>` where `<index>` is the session number from `/sessions`.

## Instructions

When this command is invoked:

1. Parse the index argument provided by the user
2. Call `mcp__codex-bridge__watch_session` with the index parameter
3. Confirm the session is now being watched

## Example

```
/watch 0
```

This will start watching session at index 0 from the sessions list.
