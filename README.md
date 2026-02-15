# IVI Studio — Private Claude Code Marketplace

Private plugin marketplace for IVI Studio game development tools.

## Setup

Add this marketplace to Claude Code:

```bash
/plugin marketplace add iVintik/private_claude_marketplace
```

## Available Plugins

| Plugin | Description |
|--------|-------------|
| `game-test` | AI-driven Unity game testing — state inspection, action scripting, autonomous exploration, visual regression |

## Installing a Plugin

```bash
/plugin install game-test@ivi-studio
```

### Post-Install: game-test

The plugin installs skills and MCP server config automatically. You still need to:

1. **Build the MCP server** (one-time, after install):
   ```bash
   cd ~/.claude/plugins/game-test/mcp-server
   npm install && npm run build
   ```

2. **Add Bridge SDK to your Unity project** — in `Packages/manifest.json`:
   ```json
   {
     "dependencies": {
       "com.theone.game-test-bridge": "file:<path-to-plugin>/bridge-sdk/unity"
     }
   }
   ```

3. **Register game-specific providers/executors** in your game code (see plugin README).

4. **Run game in Play Mode** — the bridge starts automatically.

## Adding New Plugins

Edit `.claude-plugin/marketplace.json` and add an entry to the `plugins` array.

For plugins in their own repo, use the external source pattern:
```json
{
  "name": "my-plugin",
  "description": "What it does",
  "version": "1.0.0",
  "source": {
    "source": "url",
    "url": "https://github.com/iVintik/my-plugin.git"
  },
  "category": "development"
}
```

For plugins bundled in this repo, use a relative path:
```json
{
  "name": "my-plugin",
  "source": "./plugins/my-plugin"
}
```
