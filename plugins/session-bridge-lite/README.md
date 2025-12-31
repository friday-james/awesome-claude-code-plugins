# Session Bridge Lite

Lightweight session bridge for Claude Code - **no MCP server required**.

Uses only Claude Code's built-in tools (Read, Write, Bash) to enable collaboration between sessions.

## How It Works

1. **Session Files**: Claude Code writes each session to a JSONL file at `~/.claude/projects/<project>/<session-id>.jsonl`
2. **Direct Reading**: This plugin reads those JSONL files directly using the Read tool
3. **File-Based Queue**: Feedback is exchanged via simple JSON files at `~/.claude-bridge-lite/feedback/`

## Installation

### Option 1: Install from marketplace (Recommended)

```bash
# Add the marketplace (if not already added)
/install-marketplace https://github.com/friday-james/awesome-claude-code-plugins

# Install the plugin
/install session-bridge-lite@awesome-claude-code-plugins
```

### Option 2: Copy commands to ~/.claude/commands/

```bash
# Clone the repo
git clone https://github.com/friday-james/awesome-claude-code-plugins.git

# Copy commands
cp awesome-claude-code-plugins/plugins/session-bridge-lite/commands/*.md ~/.claude/commands/
```

### Option 3: Manual plugin install

```bash
# Create cache directory
mkdir -p ~/.claude/plugins/cache/awesome-claude-code-plugins/session-bridge-lite/1.0.0

# Copy plugin files
cp -r plugins/session-bridge-lite/* ~/.claude/plugins/cache/awesome-claude-code-plugins/session-bridge-lite/1.0.0/

# Add to installed_plugins.json (requires manual edit)
```

## Commands

### /introspect
Start introspection mode - spawns background agents to watch the current session and poll for feedback. This is the easiest way to get started.

```
/introspect              # Watch current session, poll for feedback
```

This spawns two background agents:
1. **Watcher** - Monitors current session activity (~5 min)
2. **Poller** - Checks for incoming feedback (~3 min)

When agents complete, you'll get a notification. Run `/introspect` again to restart.

### /watch
Watch another Claude session by reading its JSONL file.

```
/watch                    # List available sessions
/watch 0                  # Watch session at index 0
/watch ~/.claude/projects/.../session.jsonl
```

### /send
Send feedback to a session via the shared file queue.

```
/send Great progress on the refactor!
/send --type warning Check the error handling
/send --type error Build is failing
/send --type approval LGTM!
```

### /poll
Check for incoming feedback.

```
/poll
```

Output:
```
=== SUGGESTION [2025-01-15T10:30:00Z] ===
From: abc123...
Consider adding tests for edge cases.
```

### /status
Show bridge status and available sessions.

```
/status
```

### /context
Get recent activity from a watched session.

```
/context              # Show context from first watched session
/context <path>       # Show context from specific session
```

## Usage Example

### Terminal 1 (Developer Session)

```
> /status
SESSION BRIDGE LITE - STATUS
============================
Current Session:
  ID: dev-session-123
  Path: ~/.claude/projects/.../dev-session-123.jsonl

Available Sessions:
  [0] my-project - reviewer-456 - Modified: 2m ago

> /watch 0
Now watching: reviewer-456
Recent activity: Working on code review...
```

### Terminal 2 (Reviewer Session)

```
> /watch ~/.claude/projects/.../dev-session-123.jsonl
Now watching: dev-session-123

> /context
RECENT CONTEXT: dev-session-123
===============================
[10:25] USER: Implement the login feature
[10:26] ASSISTANT: I'll create the login component...

> /send Consider adding rate limiting to the login endpoint
FEEDBACK SENT
To: dev-session-123
Message: Consider adding rate limiting to the login endpoint
```

### Terminal 1 (receives feedback)

```
> /poll
=== SUGGESTION [10:30:00Z] ===
From: reviewer-456
Consider adding rate limiting to the login endpoint
```

## File Structure

```
~/.claude-bridge-lite/
├── watched.json           # Currently watched sessions
└── feedback/
    ├── <session-id>.json  # Feedback queue for each session
    └── ...
```

### watched.json
```json
{
  "watched": ["/path/to/session.jsonl"],
  "lastRead": {"/path/to/session.jsonl": 150}
}
```

### feedback/<session-id>.json
```json
[
  {
    "timestamp": "2025-01-15T10:30:00Z",
    "type": "suggestion",
    "message": "Consider adding tests",
    "from": "reviewer-session-id"
  }
]
```

## Comparison with session-bridge (MCP version)

| Feature | session-bridge-lite | session-bridge (MCP) |
|---------|---------------------|----------------------|
| Dependencies | None | codex-bridge MCP server |
| Setup | Just install plugin | Install MCP server + configure |
| Real-time | Manual polling | Can be more responsive |
| Complexity | Simple | More features |
| Portability | Works anywhere | Needs MCP support |

## Limitations

- Agents run for 3-5 minutes then complete (run `/introspect` again to restart)
- Session ID detection may require user input for `/watch` and `/send`
- Large JSONL files may be slow to parse

## License

MIT
