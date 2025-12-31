---
allowed-tools: mcp__codex-bridge__get_status, mcp__codex-bridge__get_current_session, mcp__codex-bridge__list_sessions
description: Show session bridge status and connection info
---

## Your task

Display comprehensive bridge status information:

1. **Get current session** using `mcp__codex-bridge__get_current_session`
2. **Get bridge status** using `mcp__codex-bridge__get_status`
3. **List available sessions** using `mcp__codex-bridge__list_sessions`

Format the output clearly:

```
SESSION BRIDGE STATUS
=====================

Your Session:
  ID: [session-id]
  Path: [session-path]
  (Share this with others to let them watch your session)

Watched Sessions: [count]
  [For each watched session:]
  - Path: [path]
    Queue Size: [size]
    Last Read Position: [position]

Available Sessions to Watch:
  [List from list_sessions]
```

This gives the user a complete overview of their bridge configuration.
