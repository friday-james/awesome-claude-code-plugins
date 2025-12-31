---
allowed-tools: mcp__codex-bridge__get_current_session, mcp__codex-bridge__list_sessions, mcp__codex-bridge__watch_session, mcp__codex-bridge__poll_feedback, mcp__codex-bridge__queue_feedback, mcp__codex-bridge__get_status, mcp__codex-bridge__get_recent_context
description: Start a collaborative session with another Claude instance
---

## Your task

Set up a full collaborative workflow between Claude sessions:

### Step 1: Get Your Session Info
Use `mcp__codex-bridge__get_current_session` and display:
```
YOUR SESSION ID: [id]
Share this path with the other session:
[full path]
```

### Step 2: List and Watch Partner Session
1. Use `mcp__codex-bridge__list_sessions` to find available sessions
2. Ask user which session to collaborate with (if not specified)
3. Use `mcp__codex-bridge__watch_session` to establish connection

### Step 3: Get Partner Context
Use `mcp__codex-bridge__get_recent_context` to see what the other session is working on.

### Step 4: Confirm Setup
Use `mcp__codex-bridge__get_status` and display:
```
COLLABORATION ACTIVE
====================
Your session: [id]
Partner session: [id]
Queue size: [n]

Ready to:
- Send feedback: /send-feedback <message>
- Poll feedback: /poll-feedback
- Check status: /bridge-status
```

### Continuous Mode
If user requests continuous collaboration, set up a polling loop and report any incoming feedback immediately.
