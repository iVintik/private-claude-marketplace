# IVI Studio — Private Claude Code Marketplace

Private plugin marketplace for IVI Studio game development tools.

## Setup

Add this marketplace to Claude Code:

```bash
/plugin marketplace add iVintik/private-claude-marketplace
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

## Automated Releases

Plugin repos publish to both npm and this marketplace via GitHub Actions on release.

### How It Works

```
Plugin repo (GitHub release created)
  ├── npm: publishes package
  └── marketplace: fires repository_dispatch to this repo
                        │
                        ▼
This repo (update-plugin.yml workflow)
  └── Updates version in marketplace.json → commits → pushes
```

### Required Secrets

Each plugin repo needs two secrets:

| Secret | Purpose | Where to create |
|--------|---------|-----------------|
| `NPM_TOKEN` | Publish to npm | https://www.npmjs.com/settings/tokens → **Automation** type |
| `MARKETPLACE_TOKEN` | Dispatch to this repo | https://github.com/settings/tokens?type=beta → Fine-grained PAT |

Set them:
```bash
gh secret set NPM_TOKEN --repo clawrig/famdeck-<plugin>
gh secret set MARKETPLACE_TOKEN --repo clawrig/famdeck-<plugin>
```

The same `MARKETPLACE_TOKEN` PAT can be shared across all plugin repos.

### Token Setup & Renewal

**NPM_TOKEN** (Automation token):
1. Go to https://www.npmjs.com/settings/tokens/create
2. Type: **Automation** (bypasses 2FA for CI)
3. No expiration by default — regenerate if compromised
4. Copy the token and set it as `NPM_TOKEN` on each plugin repo

**MARKETPLACE_TOKEN** (GitHub fine-grained PAT):
1. Go to https://github.com/settings/tokens?type=beta
2. Name: `marketplace-dispatch`
3. Expiration: pick a duration (max 1 year) — **set a calendar reminder to renew**
4. Repository access: **Only select repositories** → `iVintik/private-claude-marketplace`
5. Permissions: **Contents** → Read and write
6. Generate and set as `MARKETPLACE_TOKEN` on each plugin repo

**When tokens expire:**
- npm publish and/or marketplace update jobs will fail on the next release
- Check Actions tab on the plugin repo to see which token is the problem
- Regenerate the expired token using the steps above
- Update the secret on all affected repos:
  ```bash
  # Renew MARKETPLACE_TOKEN on all plugin repos at once
  gh secret set MARKETPLACE_TOKEN --repo clawrig/famdeck-relay
  gh secret set MARKETPLACE_TOKEN --repo clawrig/famdeck-atlas
  gh secret set MARKETPLACE_TOKEN --repo clawrig/famdeck-toolkit
  ```

### Plugin Repos Using This Workflow

| Repo | Plugin name in marketplace |
|------|---------------------------|
| `clawrig/famdeck-relay` | `famdeck-relay` |
| `clawrig/famdeck-atlas` | `famdeck-atlas` |

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
