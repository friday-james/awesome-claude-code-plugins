---
description: Poll for feedback from watched Claude sessions
author: njamez
version: 1.0.0
---

# Poll Command

Poll for feedback messages from watched Claude Code sessions.

## Usage

Run `/poll` to check for new feedback from all watched sessions.
Run `/poll <index>` to poll a specific session.

## Instructions

When this command is invoked:

1. If an index is provided, call `mcp__codex-bridge__poll_feedback` with that index
2. If no index is provided, call `mcp__codex-bridge__poll_feedback` without parameters
3. Display any feedback messages received
4. If no messages, inform the user no new feedback is available

## Example

```
/poll
/poll 0
```
