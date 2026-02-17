---
name: developing-features
description: This skill should be used when the user asks to "build a feature", "implement a feature", "add new functionality", "develop a feature", "create a new feature", or when embarking on a multi-phase feature development workflow that requires codebase understanding, architecture design, clarifying questions, implementation, and quality review.
---

# Feature Development Workflow

Guide feature development through a systematic multi-phase process: understand the codebase deeply, identify and ask about all underspecified details, design elegant architectures, implement, and review.

## Core Principles

- **Ask clarifying questions**: Identify all ambiguities, edge cases, and underspecified behaviors. Ask specific, concrete questions rather than making assumptions. Wait for answers before proceeding.
- **Understand before acting**: Read and comprehend existing code patterns first.
- **Read files identified by agents**: When launching agents, ask them to return lists of the most important files to read. After agents complete, read those files to build detailed context.
- **Simple and elegant**: Prioritize readable, maintainable, architecturally sound code.
- **Use TodoWrite**: Track all progress throughout.

## Phase 1: Discovery

**Goal**: Understand what needs to be built.

1. Create a todo list with all phases
2. If feature is unclear, ask the user:
   - What problem are they solving?
   - What should the feature do?
   - Any constraints or requirements?
3. Summarize understanding and confirm with the user

## Phase 2: Codebase Exploration

**Goal**: Understand relevant existing code and patterns at both high and low levels.

1. Launch 2-3 **code-explorer** agents in parallel, each targeting a different aspect:
   - Find features similar to the target feature and trace through their implementation
   - Map the architecture and abstractions for the feature area
   - Analyze the current implementation of related existing features
   - Identify UI patterns, testing approaches, or extension points
2. Each agent should include a list of 5-10 key files to read
3. Read all files identified by agents to build deep understanding
4. Present a comprehensive summary of findings and patterns

## Phase 3: Clarifying Questions

**Goal**: Fill in gaps and resolve all ambiguities before designing.

**CRITICAL**: This is one of the most important phases. DO NOT SKIP.

1. Review the codebase findings and original feature request
2. Identify underspecified aspects: edge cases, error handling, integration points, scope boundaries, design preferences, backward compatibility, performance needs
3. **Present all questions to the user in a clear, organized list**
4. **Wait for answers before proceeding to architecture design**

If the user says "whatever you think is best", provide a recommendation and get explicit confirmation.

## Phase 4: Architecture Design

**Goal**: Design multiple implementation approaches with different trade-offs.

1. Launch 2-3 **code-architect** agents in parallel with different focuses:
   - Minimal changes: smallest change, maximum reuse
   - Clean architecture: maintainability, elegant abstractions
   - Pragmatic balance: speed + quality
2. Review all approaches and form an opinion on which fits best (consider: small fix vs large feature, urgency, complexity, team context)
3. Present to the user: brief summary of each approach, trade-offs comparison, recommendation with reasoning, concrete implementation differences
4. **Ask the user which approach they prefer**

## Phase 5: Implementation

**Goal**: Build the feature.

**DO NOT START WITHOUT USER APPROVAL.**

1. Wait for explicit user approval
2. Read all relevant files identified in previous phases
3. Implement following the chosen architecture
4. Follow codebase conventions strictly
5. Write clean, well-documented code
6. Update todos as progress is made

## Phase 6: Quality Review

**Goal**: Ensure code is simple, DRY, elegant, easy to read, and functionally correct.

1. Launch 3 **code-reviewer** agents in parallel with different focuses:
   - Simplicity, DRY, elegance
   - Bugs, functional correctness
   - Project conventions, abstractions
2. Consolidate findings and identify highest severity issues
3. **Present findings to the user and ask what they want to do** (fix now, fix later, or proceed as-is)
4. Address issues based on user decision

## Phase 7: Summary

**Goal**: Document what was accomplished.

1. Mark all todos complete
2. Summarize:
   - What was built
   - Key decisions made
   - Files modified
   - Suggested next steps
