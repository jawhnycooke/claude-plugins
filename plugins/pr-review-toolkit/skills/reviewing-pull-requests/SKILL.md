---
name: reviewing-pull-requests
description: This skill should be used when the user asks to "review my PR", "check my code before merging", "run a PR review", "analyze this pull request", "review my changes", or needs a comprehensive multi-agent pull request review covering code quality, test coverage, error handling, type design, comments, and code simplification.
---

# Comprehensive PR Review Workflow

Run multi-agent pull request reviews where each specialized agent focuses on a different aspect of code quality, then aggregate results into an actionable summary.

## Review Aspects

Six specialized review agents are available:

| Agent | Focus | When Applicable |
|-------|-------|-----------------|
| **code-reviewer** | CLAUDE.md compliance, bugs, general quality | Always |
| **pr-test-analyzer** | Test coverage quality, critical gaps | Test files changed |
| **silent-failure-hunter** | Silent failures, catch blocks, error logging | Error handling changed |
| **type-design-analyzer** | Type encapsulation, invariant expression | New types added/modified |
| **comment-analyzer** | Comment accuracy, documentation completeness | Comments/docs added |
| **code-simplifier** | Code clarity, readability, project standards | After passing review |

## Review Workflow

### Step 1: Determine Review Scope

1. Check git status to identify changed files: `git diff --name-only`
2. Check if a PR already exists: `gh pr view`
3. Parse any user-specified review aspects
4. Default: run all applicable reviews

### Step 2: Identify Applicable Reviews

Based on the changes:
- **Always applicable**: code-reviewer (general quality)
- **If test files changed**: pr-test-analyzer
- **If comments/docs added**: comment-analyzer
- **If error handling changed**: silent-failure-hunter
- **If types added/modified**: type-design-analyzer
- **After passing review**: code-simplifier (polish and refine)

### Step 3: Launch Review Agents

**Sequential approach** (default):
- One agent at a time for easier understanding and action
- Each report is complete before the next
- Good for interactive review

**Parallel approach** (on user request):
- Launch all agents simultaneously
- Faster for comprehensive review
- Results come back together

### Step 4: Aggregate Results

After agents complete, organize findings:

```markdown
# PR Review Summary

## Critical Issues (X found)
- [agent-name]: Issue description [file:line]

## Important Issues (X found)
- [agent-name]: Issue description [file:line]

## Suggestions (X found)
- [agent-name]: Suggestion [file:line]

## Strengths
- What's well-done in this PR

## Recommended Action
1. Fix critical issues first
2. Address important issues
3. Consider suggestions
4. Re-run review after fixes
```

## Agent Details

### code-reviewer
Reviews code against project guidelines in CLAUDE.md with confidence-based filtering (only reports issues with confidence >= 80). Checks for CLAUDE.md compliance, bug detection, and code quality. Groups issues by severity (Critical: 90-100, Important: 80-89).

### pr-test-analyzer
Focuses on behavioral coverage rather than line coverage. Identifies critical code paths, edge cases, and error conditions that must be tested. Rates criticality from 1-10 and maps to severity (9-10: CRITICAL, 7-8: HIGH, 5-6: MEDIUM, 1-4: LOW).

### silent-failure-hunter
Analyzes for silent failures, empty catch blocks, broad exception catching, inadequate error messages, and unjustified fallback behavior. Checks logging quality, user feedback, catch block specificity, and error propagation.

### type-design-analyzer
Evaluates new types on four dimensions (each rated 1-10): encapsulation, invariant expression, invariant usefulness, and invariant enforcement. Flags anti-patterns like anemic domain models, exposed mutable internals, and missing construction validation.

### comment-analyzer
Verifies comment factual accuracy against code, assesses completeness, evaluates long-term value, and identifies misleading elements. Categorizes findings as critical issues, improvement opportunities, and recommended removals.

### code-simplifier
Simplifies code for clarity and maintainability while preserving all functionality. Applies project standards, reduces unnecessary complexity, eliminates redundant code, and improves naming. Avoids nested ternaries and over-compact solutions.

## Usage Patterns

**Full review (default):**
Run all applicable review agents based on changed files.

**Targeted review:**
Specify aspects to focus on: `tests`, `errors`, `comments`, `types`, `code`, `simplify`.

**Parallel review:**
Request all agents run simultaneously for faster results.

## Workflow Integration

**Before committing:**
1. Write code
2. Run code and error reviews
3. Fix critical issues
4. Commit

**Before creating PR:**
1. Stage all changes
2. Run all reviews
3. Address critical and important issues
4. Re-run specific reviews to verify
5. Create PR

**After PR feedback:**
1. Make requested changes
2. Run targeted reviews based on feedback
3. Verify issues are resolved
4. Push updates

## Tips

- Run reviews early, before creating the PR
- Agents analyze git diff by default
- Address critical issues before lower priority ones
- Re-run reviews after fixes to verify resolution
- Use targeted reviews when the concern area is known
- All agents return actionable results with specific file:line references
