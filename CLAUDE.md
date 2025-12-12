# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the official Claude Code Plugins Directory for Jawhny Cooke - a curated collection of high-quality plugins maintained by Anthropic and others. The repository serves as a marketplace for Claude Code extensions. Many of the plugins have been authored by or modified from their original Anthropic versions to fit Jawhny Cooke's prompting style and workflows

## Structure

```
/plugins/           # Internal plugins developed by Anthropic but in many cases heavily edited or adapted by Jawhny Cooke for his workflow
/external_plugins/  # Third-party plugins from partners
/.claude-plugin/    # Marketplace metadata (marketplace.json)
```

## Plugin Architecture

Every plugin follows a standard structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Required: name, description, author
├── .mcp.json            # Optional: MCP server configuration
├── commands/            # Slash commands (*.md files)
├── agents/              # Agent definitions (*.md files)
├── skills/              # Skills (subdirs with SKILL.md)
└── hooks/
    └── hooks.json       # Hook configuration
```

### Component Types

**Commands** (`commands/*.md`): User-invoked slash commands with YAML frontmatter:
```yaml
---
description: Short description for /help
argument-hint: <arg> [optional]
allowed-tools: [Read, Glob, Grep]
---
```
Commands can include shell execution with `!`backtick syntax` for context gathering.

**Agents** (`agents/*.md`): Autonomous subagents with YAML frontmatter:
```yaml
---
name: agent-name
description: When to trigger this agent
tools: Glob, Grep, Read, Edit
model: sonnet
color: red
---
```
The body contains the system prompt.

**Skills** (`skills/skill-name/SKILL.md`): Model-invoked capabilities with progressive disclosure:
```yaml
---
name: skill-name
description: Trigger conditions for this skill
---
```
Skills can include `references/`, `examples/`, and `scripts/` subdirectories.

**Hooks** (`hooks/hooks.json`): Event-driven automation:
```json
{
  "hooks": {
    "PreToolUse": [{ "hooks": [{ "type": "command", "command": "script.py" }] }],
    "PostToolUse": [...],
    "Stop": [...],
    "UserPromptSubmit": [...]
  }
}
```
Use `${CLAUDE_PLUGIN_ROOT}` for portable paths in commands.

**MCP Servers** (`.mcp.json`): External tool integration via Model Context Protocol:
```json
{
  "server-name": {
    "command": "npx",
    "args": ["@package/mcp-server"]
  }
}
```

## Key Plugins

| Plugin | Purpose |
|--------|---------|
| `plugin-dev` | Toolkit for developing plugins (7 skills covering hooks, MCP, commands, agents) |
| `example-plugin` | Reference implementation demonstrating all extension options |
| `feature-dev` | Feature development workflow with code-explorer, code-architect, code-reviewer agents |
| `pr-review-toolkit` | PR review agents (test analysis, type design, silent failures, comments) |
| `hookify` | Create custom hooks from conversation analysis or explicit instructions |
| `commit-commands` | Git workflow commands (commit, push, PR creation) |

## Development Commands

```bash
# Test a plugin locally
claude --plugin-dir /path/to/plugin

# Debug MCP connections
claude --mcp-debug

# Install from marketplace
/plugin install plugin-name@claude-plugins-official
```

## Adding Plugins

**Internal plugins** go in `/plugins/` and must be added to `/.claude-plugin/marketplace.json`.

**External plugins** go in `/external_plugins/` with:
- `.claude-plugin/plugin.json` (metadata)
- `.mcp.json` (MCP configuration)

## Plugin Manifest Fields (plugin.json)

```json
{
  "name": "plugin-name",
  "description": "What the plugin does",
  "author": {
    "name": "Author Name",
    "email": "email@example.com"
  }
}
```

## Marketplace Entry Fields (marketplace.json)

```json
{
  "name": "plugin-name",
  "description": "Plugin description",
  "source": "./plugins/plugin-name",
  "category": "development|productivity|security|learning|testing|database|design|monitoring|deployment",
  "homepage": "https://github.com/..."
}
```
