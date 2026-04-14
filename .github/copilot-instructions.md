<claude-mem-context>
# claude-mem: Cross-Session Memory

*No context yet. Complete your first session and context will appear here.*

Use claude-mem's MCP search tools for manual memory queries.
</claude-mem-context>

## Shared memory tagging protocol

When writing manual/shared-memory notes, always prepend:

`[agent:copilot] [source:copilot-cli] [action:<action>] [by:copilot] [scope:<area>] [ref:<id-or-none>]`

Allowed `action` values:
- `plan`
- `fix`
- `refactor`
- `decision`
- `research`
- `release`
- `note`

Example:

`[agent:copilot] [source:copilot-cli] [action:fix] [by:copilot] [scope:auth] [ref:issue-42] Updated redirect callback handling and docs.`
