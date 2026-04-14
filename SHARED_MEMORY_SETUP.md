# Shared Memory Setup (Claude + Copilot CLI + Gemini CLI + Qwen)

This guide documents how to use [`thedotmack/claude-mem`](https://github.com/thedotmack/claude-mem) as a shared memory layer across multiple coding assistants.

## What this setup gives you

- Cross-session memory for Claude ecosystem tools
- Memory search via MCP tools
- Shared context usable by:
  - Claude Code
  - Copilot CLI
  - Gemini CLI
  - Codex CLI (transcript watcher integration)
  - Qwen CLI (manual MCP wiring)

## Prerequisites

1. Node.js 18+ and `npx`
2. CLI tools installed (as needed): Copilot CLI, Gemini CLI, Codex CLI, Qwen CLI
3. A writable home directory (`~`) for tool configs

---

## 1) Install claude-mem integrations

Run these commands:

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
```

What each command configures:

- `copilot-cli`
  - Writes MCP config at: `~/.github/copilot/mcp.json`
  - Adds project context file: `.github/copilot-instructions.md`
- `gemini-cli`
  - Adds hooks in: `~/.gemini/settings.json`
  - Adds memory context section in: `~/.gemini/GEMINI.md`
- `codex-cli`
  - Enables transcript watch in: `~/.claude-mem/transcript-watch.json`
  - Watches: `~/.codex/sessions/**/*.jsonl`
  - Adds context section in: `~/.codex/AGENTS.md`

---

## 2) Add Qwen manually (no native installer yet)

`claude-mem` currently does not provide `--ide qwen`. Add the same MCP server manually.

### User-level config (`~/.qwen/settings.json`)

Add this block:

```json
{
  "mcpServers": {
    "claude-mem": {
      "command": "node",
      "args": [
        "/Users/<YOUR_USER>/.claude/plugins/marketplaces/thedotmack/plugin/scripts/mcp-server.cjs"
      ]
    }
  }
}
```

### Project-level config (`<repo>/.qwen/settings.json`)

Add the same `mcpServers.claude-mem` entry so project sessions also see it.

> Keep existing settings; merge this key in, don’t replace the whole file.

---

## 3) Start memory worker

```bash
npx claude-mem start
```

Check status:

```bash
npx claude-mem status
```

Expected: worker running (default port `37777`).

---

## 4) Verify integration files

Check these files exist and contain `claude-mem` references:

- `~/.github/copilot/mcp.json`
- `~/.gemini/settings.json`
- `~/.claude-mem/transcript-watch.json`
- `~/.qwen/settings.json`
- `<repo>/.qwen/settings.json`

Optional web viewer:

- `http://localhost:37777`

---

## 5) Restart CLIs

After config updates, restart:

- Copilot CLI
- Gemini CLI
- Qwen CLI
- Codex CLI (if used)

This ensures each tool reloads MCP/hook settings.

---

## 6) Common troubleshooting

### `Unknown IDE: qwen`

Expected. Qwen requires manual MCP config (section 2).

### `Worker is not running`

Start it:

```bash
npx claude-mem start
```

### MCP tool not visible in CLI

1. Confirm config file path is correct
2. Confirm `mcp-server.cjs` path exists
3. Restart the CLI process
4. Re-check worker status

### Node path differences

Some installers write absolute Node paths (for example Homebrew Node). This is fine. For manual Qwen config, `command: "node"` is typically sufficient if Node is on `PATH`.

---

## 7) Security and operations notes

- `claude-mem` stores memory locally; review your local config before team-wide rollout.
- If using this in an org repo, document expected local setup in `README.md` and keep machine-specific absolute paths out of committed configs where possible.
- `claude-mem` is AGPL-3.0 licensed; verify compliance requirements for your environment.

---

## Quick setup summary

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
npx claude-mem start
```

Then manually add `mcpServers.claude-mem` to both:

- `~/.qwen/settings.json`
- `<repo>/.qwen/settings.json`

