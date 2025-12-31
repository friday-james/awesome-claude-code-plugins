---
allowed-tools: mcp__codex-bridge__queue_feedback, mcp__codex-bridge__get_status, mcp__codex-bridge__watch_session, mcp__codex-bridge__list_sessions
description: Send feedback to a watched Claude session
---

## Context

- The target session must be watched before sending feedback

## Your task

Send feedback to another Claude session:

1. **Check status** using `mcp__codex-bridge__get_status` to verify a session is being watched
2. If no sessions are watched, help the user watch one first
3. **Queue feedback** using `mcp__codex-bridge__queue_feedback` with the user's message
4. **Confirm delivery** by checking the queue size increased

Feedback types available:
- `suggestion` - A helpful suggestion or idea
- `warning` - A caution or concern
- `error` - An error or critical issue
- `approval` - Approval or agreement

If the user doesn't specify a type, default to `suggestion`.
