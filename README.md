# Claude Code Plugins

A collection of plugins for Claude Code. Some I built, some are from partners.

> **Heads up:** Make sure you trust a plugin before installing it. I don't control what's in third-party MCP servers or external code - check the plugin's homepage if you're unsure.

## Install a Plugin

```
/plugin install {plugin-name}@claude-plugins-official
```

Or browse what's available in `/plugin > Discover`.

## What's Here

- **`/plugins`** - Plugins I've built and maintain
- **`/external_plugins`** - Third-party plugins from partners

## Plugin Structure

Every plugin follows this layout:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Metadata (required)
├── .mcp.json            # MCP server config (optional)
├── commands/            # Slash commands
├── agents/              # Agent definitions
├── skills/              # Skills
└── README.md            # Docs
```

## Contributing

Want to add a plugin? Check out `/plugins/example-plugin` for a reference implementation.

For the full docs on building plugins, see the [official documentation](https://docs.anthropic.com/en/docs/claude-code/plugins).
