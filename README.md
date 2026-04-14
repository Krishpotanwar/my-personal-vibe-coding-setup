# My Personal Vibe Coding Setup

A production-ready personal AI coding environment combining:

- **Claude Code** setup (`.claude/`)
- **Copilot CLI** instructions and MCP config
- **Gemini CLI** hooks/context integration
- **Codex CLI** transcript-aware memory integration
- **Qwen CLI** manual MCP wiring
- **Shared memory across tools** with [`thedotmack/claude-mem`](https://github.com/thedotmack/claude-mem)

This repo is a **sanitized, portable setup template** focused on multi-agent workflows, memory continuity, and MCP-first tooling.

---

## What’s inside

| Path | Purpose |
|---|---|
| `.claude/` | Claude agents, commands, hooks, helper scripts, skills |
| `.github/copilot-instructions.md` | Copilot behavior/instructions |
| `.qwen/settings.json` | Project-level Qwen configuration + claude-mem MCP |
| `.codex/config.toml` | Codex CLI environment configuration |
| `.mcp.json` | Workspace MCP servers (`claude-flow`, `code-review-graph`) |
| `SHARED_MEMORY_SETUP.md` | End-to-end setup for shared memory across CLIs |
| `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` | Agent guidance and context files |

---

## Core idea: one memory layer, multiple CLIs

`claude-mem` is used as the shared memory backbone:

1. **Copilot CLI** uses MCP integration (`~/.github/copilot/mcp.json`)
2. **Gemini CLI** uses hook integration (`~/.gemini/settings.json`)
3. **Codex CLI** uses transcript watching (`~/.claude-mem/transcript-watch.json`)
4. **Qwen CLI** uses manual MCP entry (`~/.qwen/settings.json` + repo `.qwen/settings.json`)
5. **Claude ecosystem** uses the same worker/service and memory store

Result: context collected in one tool can be discovered/used by others.

---

## Quick start

## 1) Clone this repo

```bash
git clone https://github.com/Krishpotanwar/my-personal-vibe-coding-setup.git
cd my-personal-vibe-coding-setup
```

## 2) Install shared memory integrations

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
```

## 3) Start the memory worker

```bash
npx claude-mem start
npx claude-mem status
```

Default viewer: `http://localhost:37777`

## 4) Add Qwen (manual)

Add `mcpServers.claude-mem` to:

- `~/.qwen/settings.json`
- `<this-repo>/.qwen/settings.json` (already included here)

See exact block in [`SHARED_MEMORY_SETUP.md`](./SHARED_MEMORY_SETUP.md).

## 5) Restart CLIs

Restart Copilot CLI, Gemini CLI, Qwen CLI, and Codex CLI so configs/hooks are reloaded.

---

## Detailed setup guide

Use the full guide:

- **[`SHARED_MEMORY_SETUP.md`](./SHARED_MEMORY_SETUP.md)**

It includes:

- installer behavior by CLI
- verification checklist
- troubleshooting (`Unknown IDE: qwen`, worker issues, MCP visibility)
- operational/security notes

---

## Recommended local prerequisites

- Node.js **18+**
- `npx`
- `gh` (optional, for GitHub automation)
- `uv` / `uvx` (for `code-review-graph` in `.mcp.json`)
- `bun` (installed automatically by `claude-mem` when needed)

---

## Security model for this repo

This repository is intentionally sanitized:

- excludes local runtime logs/checkpoints
- excludes machine-local sensitive config (`.claude/settings.local.json`)
- keeps reusable setup/config docs only

Sensitive values should always stay in:

- local env vars
- local untracked config
- secret managers

Never commit real API keys/tokens to this repo.

---

## Maintenance workflow

## Update claude-mem

```bash
npx claude-mem update
```

Then restart worker:

```bash
npx claude-mem restart
```

## Validate worker health

```bash
npx claude-mem status
```

## Re-apply integration after CLI changes

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
```

---

## Notes on included toolchain

- `.mcp.json` includes `claude-flow` + `code-review-graph`
- `.claude/` includes a large prebuilt agent/skill/command ecosystem for orchestration-heavy workflows
- `.codex/config.toml` and `.qwen/settings.json` are tuned for this stack and can be adapted per machine

---

## License and dependencies

- This setup repo contains your configuration and documentation files.
- `claude-mem` itself is licensed separately under **AGPL-3.0** by its author.
- Review third-party tool licenses before using this setup in org/commercial environments.

