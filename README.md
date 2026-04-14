# My Personal Vibe Coding Setup

You know that moment when your AI assistant is *almost* in flow, then you switch tools and it acts like you just met?

Yeah. I got tired of that too.

So I built this repo to make my Claude + Copilot CLI + Gemini CLI + Codex + Qwen setup feel like one continuous brain instead of five goldfish with keyboards.

---

## What this repo actually is

A sanitized, usable setup template for:

- Claude Code (`.claude/`)
- Copilot CLI
- Gemini CLI
- Codex CLI
- Qwen CLI
- Shared memory using [`claude-mem`](https://github.com/thedotmack/claude-mem)

“Sanitized” means useful config is here, private local junk is not.

---

## Why this exists

Most AI setup READMEs fall into one of these buckets:

1. Beautiful marketing page, zero practical details
2. 47 commands, no clue what each one changes
3. Works for one tool, chaos for the rest

I wanted a setup that’s honest, practical, and boringly repeatable.

---

## Repo map

| Path | What it does |
|---|---|
| `.claude/` | My Claude agents, commands, hooks, helper scripts, and skills |
| `.github/copilot-instructions.md` | Copilot behavior instructions |
| `.qwen/settings.json` | Project-level Qwen config + `claude-mem` MCP entry |
| `.codex/config.toml` | Codex CLI config |
| `.mcp.json` | Workspace MCP servers (`claude-flow`, `code-review-graph`) |
| `SHARED_MEMORY_SETUP.md` | Full step-by-step shared-memory setup |
| `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` | Context/instruction docs for agent behavior |

---

## The whole trick in one paragraph

Use **one memory backbone** (`claude-mem`) and connect each CLI the way it expects:

- Copilot CLI → MCP config
- Gemini CLI → hooks + context file
- Codex CLI → transcript watcher
- Qwen CLI → manual MCP entry
- Claude ecosystem → same worker/storage

So your context doesn’t die every time you switch tools.

---

## Memory tagging rule (who did what)

For shared-memory/manual memory writes, every entry must include this prefix:

```text
[agent:<agent-tag>] [source:<cli>] [action:<action>] [by:<actor>] [scope:<project-or-module>] [ref:<ticket-or-none>]
```

Tag mapping:

- `agent:claude` for Claude Code
- `agent:copilot` for Copilot CLI
- `agent:gemini` for Gemini CLI
- `agent:codex` for Codex CLI
- `agent:qwen` for Qwen CLI

Example:

```text
[agent:qwen] [source:qwen-cli] [action:research] [by:qwen] [scope:memory] [ref:none] Compared MCP hooks vs transcript watcher behavior.
```

This keeps memory searchable by tool and by actor without guessing.

---

## Quick start (the no-drama version)

### 1) Clone

```bash
git clone https://github.com/Krishpotanwar/my-personal-vibe-coding-setup.git
cd my-personal-vibe-coding-setup
```

### 2) Install shared memory integrations

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
```

### 3) Start memory worker

```bash
npx claude-mem start
npx claude-mem status
```

If it’s healthy, viewer is usually here:

`http://localhost:37777`

### 4) Add Qwen manually (important)

`claude-mem` currently doesn’t support `--ide qwen`, so add `mcpServers.claude-mem` in:

- `~/.qwen/settings.json`
- `<repo>/.qwen/settings.json` (already included here)

Use the exact JSON from [`SHARED_MEMORY_SETUP.md`](./SHARED_MEMORY_SETUP.md).

### 5) Restart all CLIs

Yes, actually restart them. Config changes won’t magically load themselves.

---

## Full guide

If you want the full “what gets written where” breakdown:

- **[`SHARED_MEMORY_SETUP.md`](./SHARED_MEMORY_SETUP.md)**

It includes:

- per-CLI install behavior
- file-level verification checklist
- troubleshooting for common failure modes
- security/ops notes

---

## Prerequisites

- Node.js 18+
- `npx`
- Optional but useful: `gh`, `uv` / `uvx`
- `bun` (installed automatically by `claude-mem` if missing)

---

## Known limitations (no fairy tales)

- Qwen is manual right now (no native `claude-mem --ide qwen`)
- Absolute Node paths can differ machine-to-machine
- CLI restart is required after config/hook changes

Not catastrophic. Just real.

---

## Security notes

This repo is designed to avoid leaking local-sensitive state, but still:

- keep secrets in env vars or a secret manager
- do not commit API keys/tokens
- audit configs before making your own fork public

Treat setup files like code. Because they are.

---

## Maintenance

Update `claude-mem`:

```bash
npx claude-mem update
npx claude-mem restart
```

Health check:

```bash
npx claude-mem status
```

If tools change behavior, re-run installers:

```bash
npx claude-mem install --ide copilot-cli
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide codex-cli
```

---

## License note

This repo is my config/docs setup.

`claude-mem` is a separate project under **AGPL-3.0**. If you use this in teams/commercial environments, do your normal license checks.

---

If this setup saves you even one afternoon of “why is this tool pretending it has amnesia,” mission accomplished.
