---
description: List available Claude sessions that can be watched for cross-session collaboration
author: njamez
version: 1.0.0
---

# Sessions Command

List all available Claude Code sessions that can be watched and collaborated with using the codex-bridge MCP server.

## Usage

Run `/sessions` to list available sessions.

## Instructions

When this command is invoked:

1. Call `mcp__codex-bridge__list_sessions` to get available sessions
2. Display the results in a clear table format showing:
   - Index number (for use with watch_session)
   - Project path
   - Session ID (truncated)
   - Last modified time
   - File size

## Next Steps

After listing sessions, the user can:
- Watch a session: `watch_session index=N`
- Poll for feedback: `poll_feedback index=N`
- Get recent context: `get_recent_context index=N`
