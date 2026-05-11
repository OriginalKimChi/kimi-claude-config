# kimi-claude-config

English · [한국어](README.ko.md)

Personal Claude Code configuration for the [Kimi Code MCP](https://github.com/howardpen9/kimi-code-mcp) server.

Includes:
- **5 slash commands** (`/kimi-analyze`, `/kimi-query`, `/kimi-implement`, `/kimi-review`, `/kimi-resume`) — thin wrappers that route to the corresponding `kimi-code` MCP tool.
- **1 subagent** (`kimi-rescue`) — proactively delegates long-context analysis, adversarial review, or autonomous edits to Kimi (Moonshot K2.6, 256K context). Mirrors `codex-rescue` but for Kimi.

These files live under `~/.claude/` on the host machine and are loaded by Claude Code automatically.

## Prerequisites

```bash
# Runtimes
brew install node uv

# Kimi CLI (Python) — log in with your Moonshot API key on first run
uv tool install kimi-cli
kimi

# Kimi MCP server (from GitHub fork)
npm i -g github:OriginalKimChi/kimi-code-mcp
# or upstream:
# npm i -g github:howardpen9/kimi-code-mcp
```

Register the MCP server in `~/.claude/mcp.json`:

```json
{
  "mcpServers": {
    "kimi-code": { "command": "kimi-mcp-server" }
  }
}
```

## Install commands + agent

```bash
git clone https://github.com/OriginalKimChi/kimi-claude-config.git ~/kimi-claude-config

# Symlink (preferred — pulls updates with `git pull`)
mkdir -p ~/.claude/commands ~/.claude/agents
ln -sf ~/kimi-claude-config/commands/kimi    ~/.claude/commands/kimi
ln -sf ~/kimi-claude-config/agents/kimi-rescue.md ~/.claude/agents/kimi-rescue.md

# Or just copy
# cp -r ~/kimi-claude-config/commands/kimi    ~/.claude/commands/
# cp    ~/kimi-claude-config/agents/kimi-rescue.md ~/.claude/agents/
```

Restart Claude Code. `/kimi-analyze`, `/kimi-query`, etc. should show up in the slash-command list, and the `kimi-rescue` subagent becomes addressable via the `Agent` tool.

## Layout

```
commands/kimi/
  kimi-analyze.md     # codebase analysis
  kimi-query.md       # general programming Q&A (no codebase context)
  kimi-implement.md   # autonomous file editing (runs in a fresh worktree)
  kimi-review.md      # adversarial code review
  kimi-resume.md      # continue a previous Kimi session
agents/
  kimi-rescue.md      # routing subagent → picks one Kimi MCP tool and forwards
```

## License

MIT.
