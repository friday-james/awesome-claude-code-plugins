# Session Bridge Plugin

Bridge between Claude Code sessions for real-time collaboration and feedback.

## Prerequisites

This plugin requires the `codex-bridge` MCP server to be installed and configured.

### Install codex-bridge MCP Server

1. Install the server globally:
   ```bash
   npm install -g codex-bridge
   ```

2. Add to your Claude Code MCP configuration (`~/.claude/settings.json` or project `.claude/settings.json`):
   ```json
   {
     "mcpServers": {
       "codex-bridge": {
         "command": "codex-bridge",
         "args": []
       }
     }
   }
   ```

3. Restart Claude Code to load the MCP server.

## Plugin Installation

### Option 1: Install from awesome-claude-code-plugins

```bash
# Clone the plugins repository
git clone https://github.com/ccplugins/awesome-claude-code-plugins.git

# The session-bridge plugin is at:
# awesome-claude-code-plugins/plugins/session-bridge/
```

### Option 2: Manual Installation

1. Create the plugin directory:
   ```bash
   mkdir -p ~/.claude/plugins/session-bridge/.claude-plugin
   mkdir -p ~/.claude/plugins/session-bridge/commands
   ```

2. Copy the plugin files:
   - `.claude-plugin/plugin.json`
   - `commands/watch-session.md`
   - `commands/poll-feedback.md`
   - `commands/send-feedback.md`
   - `commands/bridge-status.md`
   - `commands/collaborate.md`

## Commands

### /watch-session
Watch another Claude session for collaboration.

```
/watch-session
/watch-session 0          # Watch session at index 0
/watch-session <path>     # Watch session at specific path
```

### /poll-feedback
Poll for feedback from watched Claude sessions.

```
/poll-feedback
```

When feedback is received, it will be displayed as:
```
FEEDBACK RECEIVED:
Type: suggestion
Message: Your feedback message here
```

### /send-feedback
Send feedback to a watched Claude session.

```
/send-feedback Hello from the other session!
/send-feedback --type warning Check this potential issue
```

Feedback types: `suggestion`, `warning`, `error`, `approval`

### /bridge-status
Show comprehensive bridge status and connection info.

```
/bridge-status
```

Displays:
- Your session ID (share with collaborators)
- Currently watched sessions
- Queue sizes
- Available sessions to watch

### /collaborate
Start a full collaborative session with another Claude instance.

```
/collaborate
/collaborate 0    # Collaborate with session at index 0
```

This command sets up the complete workflow:
1. Displays your session ID for sharing
2. Lists available sessions
3. Watches the partner session
4. Shows partner's recent context
5. Confirms collaboration is active

## Usage Example

### Session A (Developer)
```
> /collaborate
YOUR SESSION ID: abc123...
Share this path with the other session:
/home/user/.claude/projects/.../abc123.jsonl

Available sessions:
[0] project-name - Session: def456... - Modified: 2m ago

Which session to collaborate with? 0

COLLABORATION ACTIVE
====================
Your session: abc123...
Partner session: def456...
Ready for collaboration!
```

### Session B (Reviewer)
```
> /watch-session /home/user/.claude/projects/.../abc123.jsonl
Now watching session abc123...

> /send-feedback Looks good! Consider adding error handling.
Feedback queued successfully.
```

### Session A (receives feedback)
```
> /poll-feedback
FEEDBACK RECEIVED:
Type: suggestion
Message: Looks good! Consider adding error handling.
```

## How It Works

The session bridge uses Claude Code's session files (`.jsonl`) to enable communication:

1. **Session Files**: Each Claude Code session writes to a JSONL file
2. **Watching**: One session can "watch" another's file for changes
3. **Feedback Queue**: Messages are queued and polled between sessions
4. **Real-time**: Poll frequently to get near real-time feedback

## Troubleshooting

### "No sessions being watched"
Run `/watch-session` first to establish a connection.

### "MCP server not found"
Ensure `codex-bridge` is installed and configured in your MCP settings.

### Can't find partner session
- Make sure both Claude Code instances are running
- Use `/bridge-status` to see available sessions
- Share the full session path with your collaborator

## License

MIT
