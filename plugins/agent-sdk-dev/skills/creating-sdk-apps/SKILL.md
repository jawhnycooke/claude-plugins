---
name: creating-sdk-apps
description: This skill should be used when the user asks to "create an agent SDK app", "build a Claude Agent SDK project", "set up a new SDK application", "scaffold an agent app", "create a new agent", or needs guidance on initializing, configuring, and building applications with the Claude Agent SDK in TypeScript or Python.
---

# Creating Claude Agent SDK Applications

Guide the creation of new Claude Agent SDK applications from initial requirements gathering through project setup, implementation, verification, and getting-started documentation.

## Reference Documentation

Before starting, review the official documentation for accurate, up-to-date guidance:

1. **Overview**: https://docs.claude.com/en/api/agent-sdk/overview
2. **SDK reference** (based on language choice):
   - TypeScript: https://docs.claude.com/en/api/agent-sdk/typescript
   - Python: https://docs.claude.com/en/api/agent-sdk/python
3. **Relevant guides**: Streaming vs Single Mode, Permissions, Custom Tools, MCP integration, Subagents, Sessions

Use WebFetch to read these pages. Always check for and use the latest package versions via WebSearch before installation.

## Requirements Gathering

Ask these questions one at a time, waiting for each response before asking the next. Skip any already answered via arguments.

1. **Language**: TypeScript or Python?
2. **Project name**: What should the project be called?
3. **Agent type** (skip if #2 was detailed enough): What kind of agent? Examples:
   - Coding agent (SRE, security review, code review)
   - Business agent (customer support, content creation)
   - Custom agent (user describes their use case)
4. **Starting point**: Minimal "Hello World", basic agent with common features, or specific example?
5. **Tooling choice**: Confirm package manager and tools (npm/pnpm/bun for TypeScript; pip/poetry for Python)

## Setup Plan

Based on answers, create a plan covering:

### Project Initialization
- Create project directory (if it does not exist)
- Initialize package manager:
  - TypeScript: `npm init -y` with `type: "module"`, scripts including "typecheck"
  - Python: `requirements.txt` or `poetry init`
- Add configuration files:
  - TypeScript: `tsconfig.json` with proper SDK settings
  - Python: Config files if needed

### Version Check
- Check npm/PyPI for the latest version before installing
- TypeScript: https://www.npmjs.com/package/@anthropic-ai/claude-agent-sdk
- Python: https://pypi.org/project/claude-agent-sdk/
- Inform the user which version is being installed

### SDK Installation
- TypeScript: `npm install @anthropic-ai/claude-agent-sdk@latest`
- Python: `pip install claude-agent-sdk`
- Verify installed version after installation

### Starter Files
- TypeScript: Create `index.ts` or `src/index.ts` with a basic query example
- Python: Create `main.py` with a basic query example
- Include proper imports and basic error handling
- Use modern syntax from the latest SDK version

### Environment Setup
- Create `.env.example` with `ANTHROPIC_API_KEY=your_api_key_here`
- Add `.env` to `.gitignore`
- Explain how to get an API key from https://console.anthropic.com/

### Optional .claude Directory
- Offer to create `.claude/` directory for agents, commands, and settings
- Ask if example subagents or slash commands are desired

## Implementation

After gathering requirements and getting user confirmation:

1. Check for latest package versions
2. Execute setup steps
3. Create all necessary files
4. Install dependencies (always use latest stable versions)
5. Verify installed versions and inform the user
6. Create a working example based on agent type
7. Add helpful comments explaining each part

## Verification

**VERIFY THE CODE WORKS BEFORE FINISHING.**

- TypeScript: Run `npx tsc --noEmit` and fix ALL type errors until clean
- Python: Verify imports and check for syntax errors
- Do NOT consider setup complete until verification passes

After manual verification, launch the appropriate verifier agent:
- TypeScript: **agent-sdk-verifier-ts**
- Python: **agent-sdk-verifier-py**

Review the verification report and address any issues.

## Getting Started Guide

Provide the user with:

1. **How to run**: `npm start` or `python main.py`
2. **How to set API key**: Environment variable setup
3. **Useful resources**: Links to SDK documentation
4. **Common next steps**: Customize system prompt, add custom tools via MCP, configure permissions, create subagents
