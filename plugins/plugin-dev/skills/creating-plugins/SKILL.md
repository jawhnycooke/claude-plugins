---
name: creating-plugins
description: This skill should be used when the user asks to "create a plugin", "build a new plugin", "scaffold a plugin", "make a Claude Code plugin", "design a plugin", or needs guidance on the end-to-end plugin creation workflow including discovery, component planning, design, implementation, validation, and testing.
---

# Plugin Creation Workflow

Guide end-to-end plugin creation from initial concept through component design, implementation, validation, and testing. Follow a systematic multi-phase approach to produce high-quality Claude Code plugins.

## Core Principles

- **Ask clarifying questions**: Identify all ambiguities about plugin purpose, triggering, scope, and components before implementing.
- **Load relevant skills**: Use the Skill tool to load plugin-dev skills when needed (plugin-structure, hook-development, agent-development, skill-development, mcp-integration, plugin-settings).
- **Use specialized agents**: Leverage agent-creator, plugin-validator, and skill-reviewer agents.
- **Follow best practices**: Apply patterns from plugin-dev's own implementation.
- **Use TodoWrite**: Track progress throughout all phases.

## Phase 1: Discovery

**Goal**: Understand the plugin's purpose and target users.

1. Create a todo list with all phases
2. If plugin purpose is clear, summarize understanding and identify plugin type (integration, workflow, analysis, toolkit)
3. If unclear, ask:
   - What problem does this plugin solve?
   - Who will use it and when?
   - What should it do?
   - Any similar plugins to reference?
4. Confirm understanding with the user before proceeding

## Phase 2: Component Planning

**Goal**: Determine which plugin components are needed.

**Load plugin-structure skill** before this phase.

Analyze requirements and determine needed components:
- **Skills**: Specialized knowledge? (hooks API, MCP patterns)
- **Commands**: User-initiated actions? (deploy, configure, analyze)
- **Agents**: Autonomous tasks? (validation, generation, analysis)
- **Hooks**: Event-driven automation? (validation, notifications)
- **MCP**: External service integration? (databases, APIs)
- **Settings**: User configuration? (.local.md files)

Present a component plan table to the user and get confirmation.

## Phase 3: Detailed Design

**Goal**: Specify each component in detail and resolve all ambiguities.

**CRITICAL**: Do not skip this phase.

For each component, identify underspecified aspects:
- **Skills**: Trigger conditions, knowledge scope, detail level
- **Commands**: Arguments, tools, interactive vs automated
- **Agents**: Trigger mode (proactive/reactive), tools, output format
- **Hooks**: Events, prompt vs command type, validation criteria
- **MCP**: Server type, authentication, tools exposed
- **Settings**: Fields, required vs optional, defaults

Present all questions organized by component type. Wait for answers.

## Phase 4: Plugin Structure Creation

**Goal**: Create the directory structure and manifest.

1. Determine plugin name (kebab-case, descriptive)
2. Choose plugin location (ask the user)
3. Create directory structure:
   ```bash
   mkdir -p plugin-name/.claude-plugin
   mkdir -p plugin-name/skills     # if needed
   mkdir -p plugin-name/commands   # if needed
   mkdir -p plugin-name/agents     # if needed
   mkdir -p plugin-name/hooks      # if needed
   ```
4. Create `plugin.json` manifest
5. Create README.md template
6. Initialize git repo if creating new directory

## Phase 5: Component Implementation

**Goal**: Create each component following best practices.

**Load relevant skills** before implementing each component type:
- Skills: Load skill-development skill
- Commands: Load command-development skill
- Agents: Load agent-development skill
- Hooks: Load hook-development skill
- MCP: Load mcp-integration skill
- Settings: Load plugin-settings skill

### Skills Implementation
- Plan resources (scripts/, references/, examples/)
- Write SKILL.md with third-person description and specific trigger phrases
- Lean body (1,500-2,000 words) in imperative form
- Create reference files for detailed content
- Validate with skill-reviewer agent

### Commands Implementation
- Write markdown with frontmatter (description, argument-hint, allowed-tools)
- Instructions written FOR Claude, not TO the user
- Reference relevant skills if applicable

### Agents Implementation
- Use agent-creator agent to generate: identifier, whenToUse with examples, systemPrompt
- Create agent markdown with frontmatter (name, description, model, color, tools)
- Validate with validate-agent.sh

### Hooks Implementation
- Create hooks/hooks.json with hook configuration
- Prefer prompt-based hooks for complex logic
- Use `${CLAUDE_PLUGIN_ROOT}` for portability
- Test with validate-hook-schema.sh and test-hook.sh

### MCP Implementation
- Create .mcp.json with server configuration
- Use `${CLAUDE_PLUGIN_ROOT}` in command paths
- Document required environment variables

## Phase 6: Validation

**Goal**: Ensure the plugin meets quality standards.

1. Run **plugin-validator** agent (checks manifest, structure, naming, components, security)
2. Fix critical issues
3. Run **skill-reviewer** for each skill
4. Run validate-agent.sh on agent files
5. Run validate-hook-schema.sh on hooks configuration
6. Present findings and ask the user whether to fix now or proceed

## Phase 7: Testing

**Goal**: Test that the plugin works correctly in Claude Code.

Provide installation instructions:
```bash
cc --plugin-dir /path/to/plugin-name
```

Verification checklist:
- Skills load when triggered (test with trigger phrases from descriptions)
- Commands appear in /help and execute correctly
- Agents trigger on appropriate scenarios
- Hooks activate on events
- MCP servers connect
- Settings files work

## Phase 8: Documentation

**Goal**: Ensure complete documentation and distribution readiness.

1. Verify README completeness (overview, features, installation, usage)
2. Add marketplace entry if publishing (marketplace.json)
3. Create summary: components created, files, structure, next steps

## Key Decision Points (Wait for User)

1. After Phase 1: Confirm plugin purpose
2. After Phase 2: Approve component plan
3. After Phase 3: Proceed to implementation
4. After Phase 6: Fix issues or proceed
5. After Phase 7: Continue to documentation
