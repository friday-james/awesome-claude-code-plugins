---
allowed-tools: mcp__codex-bridge__list_sessions, mcp__codex-bridge__watch_session, mcp__codex-bridge__get_status, mcp__codex-bridge__get_current_session
description: Watch another Claude session for collaboration
---

## Context

- Current session: Use `mcp__codex-bridge__get_current_session` to get your session ID
- Available sessions: Use `mcp__codex-bridge__list_sessions` to see other sessions

## Your task

Help the user establish a session watch for collaboration between Claude instances:

1. **List available sessions** using `mcp__codex-bridge__list_sessions`
2. **Display current session ID** so others can connect to this session
3. **Watch a session** when the user specifies which one (by index or path)
4. **Confirm status** using `mcp__codex-bridge__get_status`

If the user provides a session index or path, watch it immediately. Otherwise, list available sessions and ask which one to watch.
