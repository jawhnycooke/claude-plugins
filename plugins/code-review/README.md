# Code Review Plugin

Automated code review for pull requests using multiple specialized agents with confidence-based scoring, severity classification, and consensus validation to filter false positives.

## Overview

The Code Review Plugin automates pull request review by launching multiple agents in parallel to independently audit changes from different perspectives. It uses confidence scoring combined with severity classification to deliver actionable, prioritized feedback. Specialized agents are automatically triggered based on the type of changes detected.

## Commands

### `/code-review`

Performs automated code review on a pull request using multiple specialized agents.

**What it does:**
1. Checks if review is needed (skips closed, draft, trivial, or already-reviewed PRs)
2. Gathers relevant CLAUDE.md guideline files from the repository
3. Summarizes the PR and **detects change types** (error handling, tests, new types, comments)
4. Launches **5 core agents + up to 4 specialized agents** in parallel:
   - **Agent #1**: Audit for CLAUDE.md compliance
   - **Agent #2**: Shallow scan for obvious bugs
   - **Agent #3**: Git blame/history context analysis
   - **Agent #4**: Check previous PR comments on touched files
   - **Agent #5**: Verify compliance with code comments
   - **Agent #6** (conditional): Silent failure detection (if error handling modified)
   - **Agent #7** (conditional): Test coverage analysis (if test files modified)
   - **Agent #8** (conditional): Type design analysis (if new types introduced)
   - **Agent #9** (conditional): Comment accuracy analysis (if comments modified)
5. Scores each issue for **confidence (0-100)** and **severity (CRITICAL/HIGH/MEDIUM/LOW)**
6. Filters out issues below 80 confidence threshold
7. **Consensus scoring** for borderline issues (60-85): 3 independent scorers vote
8. Re-checks eligibility before posting
9. Posts review comment grouped by severity with high-confidence issues only
10. **Suggests code simplification** if no critical issues found (optional)

**Usage:**
```bash
/code-review
```

**Example workflow:**
```bash
# On a PR branch, run:
/code-review

# Claude will:
# - Detect change types (error handling, tests, new types, comments)
# - Launch 5-9 review agents in parallel
# - Score issues for confidence AND severity
# - Use consensus voting for borderline issues
# - Post comment grouped by CRITICAL > HIGH > MEDIUM
# - Offer to simplify code if review passes
```

## Specialized Agents

The plugin automatically triggers specialized agents based on what changed in the PR:

### Silent Failure Hunter
**Triggered when:** Error handling code is modified (try/catch/except/finally blocks)

Analyzes:
- Empty catch blocks (absolutely forbidden)
- Silent failures without logging or user feedback
- Overly broad exception catching
- Inappropriate fallback behavior
- Missing error context in logs

### PR Test Analyzer
**Triggered when:** Test files are modified (*test*, *spec*, __tests__/*, tests/*)

Analyzes:
- Behavioral coverage vs line coverage
- Critical untested code paths
- Missing edge case coverage
- Test quality and resilience to refactoring
- Anti-patterns (excessive mocking, weak assertions)

### Type Design Analyzer
**Triggered when:** New types are introduced (interface/type/class/struct/enum/@dataclass)

Analyzes:
- Encapsulation quality (are invariants protected?)
- Invariant expression (is the design self-documenting?)
- Invariant usefulness (do they prevent real bugs?)
- Invariant enforcement (are all mutation points guarded?)

### Comment Analyzer
**Triggered when:** Docstrings, JSDoc comments, or significant inline comments are modified

Analyzes:
- Factual accuracy (do comments match actual code behavior?)
- Completeness (are critical assumptions documented?)
- Long-term value (will comments help future maintainers?)
- Misleading elements (outdated references, ambiguous language)
- Comment rot prevention (flag comments likely to become stale)

### Code Simplifier
**Triggered when:** Review passes with no CRITICAL or HIGH issues (user confirmation required)

Provides:
- Clarity improvements while preserving functionality
- Project coding standard enforcement
- Redundancy removal and readability enhancements
- Nested ternary elimination
- Import sorting and consistent patterns

## Severity Classification

Each issue is classified by severity, independent of confidence:

| Severity | Definition | Examples |
|----------|------------|----------|
| **CRITICAL** | Security vulnerabilities, data loss, crashes, breaking API changes | SQL injection, unhandled null causing crash |
| **HIGH** | Logic bugs, broken functionality, significant performance issues | Race condition, missing validation |
| **MEDIUM** | Code quality, minor performance, incomplete error messages | Poor error message, CLAUDE.md style violation |
| **LOW** | Minor style, documentation, refactoring suggestions | Nitpicks not in CLAUDE.md |

## Confidence Scoring

Each issue is independently scored 0-100 for confidence:

| Score | Meaning |
|-------|---------|
| **0** | Not confident at all. False positive or pre-existing issue. |
| **25** | Somewhat confident. Might be real, might be false positive. |
| **50** | Moderately confident. Real issue but might be a nitpick. |
| **75** | Highly confident. Very likely real and will be hit in practice. |
| **100** | Absolutely certain. Definitely real and will happen frequently. |

### Consensus Scoring

For borderline issues (confidence 60-85):
1. Two additional independent scorers evaluate the issue
2. Each scorer receives the PR diff, issue description, and CLAUDE.md files
3. Scorers are NOT told the original score
4. **Consensus rules:**
   - If 2/3 score ≥80: Issue passes (included in output)
   - If 2/3 score <80: Issue fails (excluded from output)
   - Final score = median of all 3 scores

## Review Comment Format

```markdown
### Code review

Found 3 issues (1 CRITICAL, 1 HIGH, 1 MEDIUM):

**CRITICAL** [95% confidence]
1. SQL injection vulnerability (security)

https://github.com/owner/repo/blob/abc123.../src/db.ts#L67-L72

**HIGH** [88% confidence]
2. Missing validation allows invalid state (bug)

https://github.com/owner/repo/blob/abc123.../src/auth.ts#L88-L95

**MEDIUM** [82% confidence, consensus 3/3]
3. Empty catch block swallows errors (error-handling)

https://github.com/owner/repo/blob/abc123.../src/utils.ts#L23-L28
```

If no issues found:
```markdown
### Code review

No issues found. Checked for bugs, CLAUDE.md compliance, and specialized analysis:
- Error handling review ✓
- Test coverage analysis ✓
- Type design review ✓
- Comment accuracy review ✓

Would you like me to run the code-simplifier to improve clarity and maintainability?
```

## False Positives Filtered

The plugin filters out:
- Pre-existing issues not introduced in PR
- Code that looks like a bug but isn't
- Pedantic nitpicks a senior engineer wouldn't call out
- Issues linters/typecheckers/compilers catch (CI handles these)
- General quality issues (unless in CLAUDE.md)
- Issues explicitly silenced in code
- Intentional functionality changes related to the broader change
- Real issues on lines the user did not modify

## Installation

This plugin is included in the Claude Code repository. The command is automatically available when using Claude Code.

## Best Practices

### Using `/code-review`
- Maintain clear CLAUDE.md files for better compliance checking
- Trust the 80+ confidence threshold - false positives are filtered
- Pay attention to severity: CRITICAL and HIGH need immediate action
- Consensus scoring ensures borderline issues are validated
- Run on all non-trivial pull requests

### When to use
- All pull requests with meaningful changes
- PRs touching critical code paths
- PRs with error handling changes (triggers silent-failure-hunter)
- PRs modifying tests (triggers pr-test-analyzer)
- PRs introducing new types (triggers type-design-analyzer)
- PRs with documentation changes (triggers comment-analyzer)

### When not to use
- Closed or draft PRs (automatically skipped anyway)
- Trivial automated PRs (automatically skipped)
- Urgent hotfixes requiring immediate merge
- PRs already reviewed (automatically skipped)

## Workflow Integration

### Standard PR review workflow:
```bash
# Create PR with changes
/code-review

# Review the automated feedback (grouped by severity)
# Address CRITICAL and HIGH issues first
# Make any necessary fixes
# Merge when ready
```

### As part of CI/CD:
```bash
# Trigger on PR creation or update
# Automatically posts review comments
# Skip if review already exists
```

## Requirements

- Git repository with GitHub integration
- GitHub CLI (`gh`) installed and authenticated
- CLAUDE.md files (optional but recommended for guideline checking)

## Troubleshooting

### Review takes too long

**Issue**: Agents are slow on large PRs

**Solution**:
- Normal for large changes - agents run in parallel
- Up to 9 independent agents ensure thoroughness
- Specialized agents only run when relevant (detected automatically)
- Consider splitting large PRs into smaller ones

### Too many false positives

**Issue**: Review flags issues that aren't real

**Solution**:
- Default threshold is 80 (already filters most false positives)
- Borderline issues (60-85) go through consensus scoring
- Make CLAUDE.md more specific about what matters
- Consider if the flagged issue is actually valid

### No review comment posted

**Issue**: `/code-review` runs but no comment appears

**Solution**:
Check if:
- PR is closed (reviews skipped)
- PR is draft (reviews skipped)
- PR is trivial/automated (reviews skipped)
- PR already has review (reviews skipped)
- No issues scored ≥80 (no comment needed)

### Link formatting broken

**Issue**: Code links don't render correctly in GitHub

**Solution**:
Links must follow this exact format:
```
https://github.com/owner/repo/blob/[full-sha]/path/file.ext#L[start]-L[end]
```
- Must use full SHA (not abbreviated)
- Must use `#L` notation
- Must include line range with at least 1 line of context

### GitHub CLI not working

**Issue**: `gh` commands fail

**Solution**:
- Install GitHub CLI: `brew install gh` (macOS) or see [GitHub CLI installation](https://cli.github.com/)
- Authenticate: `gh auth login`
- Verify repository has GitHub remote

## Tips

- **Write specific CLAUDE.md files**: Clear guidelines = better reviews
- **Include context in PRs**: Helps agents understand intent
- **Use severity for prioritization**: CRITICAL > HIGH > MEDIUM > LOW
- **Trust consensus scoring**: Borderline issues are validated by 3 scorers
- **Review automatically**: Set up as part of PR workflow
- **Trust the filtering**: Threshold and consensus prevent noise

## Configuration

### Adjusting confidence threshold

The default threshold is 80. To adjust, modify the command file at `commands/code-review.md`:
```markdown
Filter out any issues with a confidence score less than 80.
```

Change `80` to your preferred threshold (0-100).

### Customizing review focus

Edit `commands/code-review.md` to add or modify agent tasks:
- Add security-focused agents
- Add performance analysis agents
- Add accessibility checking agents
- Add documentation quality checks

## Technical Details

### Agent architecture

**Core Agents (always run):**
- **CLAUDE.md compliance agent**: Verifies guideline adherence
- **Bug detector**: Focused on obvious bugs in changes only
- **History analyzer**: Context from git blame and history
- **Previous PR checker**: Relevant comments from past PRs
- **Code comment verifier**: Compliance with inline guidance

**Specialized Agents (conditional):**
- **Silent Failure Hunter**: Activated when error handling is modified
- **PR Test Analyzer**: Activated when test files are modified
- **Type Design Analyzer**: Activated when new types are introduced
- **Comment Analyzer**: Activated when comments/docstrings are modified
- **Code Simplifier**: Suggested after review passes (user opt-in)

### Scoring system
- Each issue independently scored 0-100 for confidence
- Each issue classified as CRITICAL/HIGH/MEDIUM/LOW for severity
- Threshold (default 80) filters low-confidence issues
- Borderline issues (60-85) validated via 3-scorer consensus
- For CLAUDE.md issues: verifies guideline explicitly mentions it

### Change detection
The plugin automatically detects:
- `hasErrorHandling`: try/catch/except/finally/.catch blocks modified
- `hasTestFiles`: Files matching *test*, *spec*, __tests__/*, tests/*
- `hasNewTypes`: New interface/type/class/struct/enum/@dataclass definitions
- `hasCommentChanges`: Docstrings, JSDoc, or significant inline comments modified

### GitHub integration
Uses `gh` CLI for:
- Viewing PR details and diffs
- Fetching repository data
- Reading git blame and history
- Checking previous PR comments
- Posting review comments

## Author

Jawhny Cooke (plugins@jawhnycooke.ai)

## Version

2.1.0
