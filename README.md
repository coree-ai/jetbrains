# coree for JetBrains

Persistent memory and code intelligence for AI agents in JetBrains IDEs with
[AI Assistant](https://www.jetbrains.com/ai/).

> **Requirements**: IntelliJ IDEA 2025.1+ (or any JetBrains IDE 2025.1+) with
> AI Assistant plugin 251.26094.80.5+.

## Install

### Option A - Via UI (recommended)

1. Open your JetBrains IDE.
2. Go to **Settings | Tools | AI Assistant | Model Context Protocol (MCP)**.
3. Click **+** to add a new server.
4. Enter:
   - **Name**: `coree`
   - **Command**: `npx`
   - **Arguments**: `--yes @coree-ai/coree@0.13.0 serve`
5. Click **OK** and restart the IDE.

### Option B - Config file

Copy `mcp.json` from this repo to the IDE-version-specific config path for your OS:

| OS | Path |
|----|------|
| Linux | `~/.config/JetBrains/<IDE><version>/mcp.json` |
| macOS | `~/Library/Application Support/JetBrains/<IDE><version>/mcp.json` |
| Windows | `%APPDATA%\JetBrains\<IDE><version>\mcp.json` |

Where `<IDE>` is the product name (e.g. `IntelliJIdea`, `PyCharm`, `WebStorm`, `GoLand`)
and `<version>` is the year.major release (e.g. `2025.1`).

Example for IntelliJ IDEA 2025.1 on Linux:
```sh
cp mcp.json ~/.config/JetBrains/IntelliJIdea2025.1/mcp.json
```

The `mcp.json` content:
```json
{
  "mcpServers": {
    "coree": {
      "command": "npx",
      "args": ["--yes", "@coree-ai/coree@0.13.0", "serve"],
      "env": {
        "COREE__MEMORY__REMOTE_AUTH_TOKEN": "${COREE__MEMORY__REMOTE_AUTH_TOKEN}",
        "COREE__MEMORY__REMOTE_URL": "${COREE__MEMORY__REMOTE_URL}"
      }
    }
  }
}
```

Restart the IDE after copying.

### Context file

Copy `AGENTS.md` to your project root so AI Assistant loads coree usage instructions:

```sh
curl -fsSL https://raw.githubusercontent.com/coree-ai/jetbrains/main/AGENTS.md -o AGENTS.md
```

JetBrains AI Assistant auto-detects `AGENTS.md` and `CLAUDE.md` at the project root
and includes them in agent interactions.

## Verify

Open an AI Assistant chat in your IDE and ask: `What coree tools are available?`

The assistant should list tools like `search`, `store_memories`, `capture_note`, etc.

## Environment Variables

Set these in your shell profile (Linux/macOS) or System Environment Variables (Windows):

| Variable | Description |
|----------|-------------|
| `COREE__MEMORY__REMOTE_AUTH_TOKEN` | Auth token for remote memory sync |
| `COREE__MEMORY__REMOTE_URL` | Remote memory database URL |
| `COREE__INDEX__REMOTE_AUTH_TOKEN` | Auth token for remote index sync |
| `COREE__INDEX__REMOTE_URL` | Remote index database URL |

JetBrains IDEs inherit env vars from the shell that launched them on Linux/macOS.
On Windows, set them as System Environment Variables or in the IDE's **Help |
Edit Custom Properties** dialog.

## Limitations

- No distributable plugin format: JetBrains Plugin Marketplace hosts IDE plugins
  (Java/Kotlin), not MCP config bundles. Config is imported manually or via the UI.
- No lifecycle hooks: The JetBrains AI Assistant MCP API does not expose hook events
  for coree prompt injection. Use the `search()` tool manually at the start of each session.

## Requirements

- [Node.js](https://nodejs.org) 18+ with `npx` on `PATH`
- JetBrains IDE 2025.1+ with AI Assistant plugin 251.26094.80.5+

## Version Bumping

```sh
node scripts/bump-version.mjs 0.14.0
```

Updates version references in `mcp.json` and `README.md`.
