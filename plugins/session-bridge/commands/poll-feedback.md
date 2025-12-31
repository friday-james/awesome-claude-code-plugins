---
allowed-tools: mcp__codex-bridge__poll_feedback, mcp__codex-bridge__get_status, mcp__codex-bridge__watch_session, mcp__codex-bridge__list_sessions
description: Poll for feedback from watched Claude sessions
---

## Context

- Check bridge status first to see what sessions are being watched

## Your task

Poll for feedback from watched sessions and report any messages received:

1. **Check status** using `mcp__codex-bridge__get_status` to verify sessions are being watched
2. If no sessions are watched, help the user set one up first
3. **Poll for feedback** using `mcp__codex-bridge__poll_feedback`
4. **Report feedback** clearly when received, formatted as:
   ```
   FEEDBACK RECEIVED:
   Type: [suggestion/warning/error/approval]
   Message: [the feedback content]
   ```
5. If no feedback pending, inform the user and offer to poll again

For continuous polling, the user can run this command multiple times or ask for repeated polling.
