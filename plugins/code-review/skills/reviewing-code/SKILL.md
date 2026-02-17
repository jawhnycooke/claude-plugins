---
name: reviewing-code
description: This skill should be used when the user asks to "review a pull request", "code review this PR", "check this PR for bugs", "review PR #123", or when performing automated code review on a GitHub pull request. It provides a structured multi-agent review workflow with confidence scoring, severity classification, and consensus-based filtering for high-quality PR reviews posted as GitHub comments.
---

# Code Review Workflow

Perform comprehensive code reviews on GitHub pull requests using a structured multi-agent pipeline with confidence-based filtering to minimize false positives.

## Review Pipeline

### Step 1: Eligibility Check

Launch a Haiku agent to determine if the PR should be reviewed. Skip the review if the PR:
- Is closed or a draft
- Is automated or trivially simple (no logic changes)
- Already has a code review comment from a previous run

### Step 2: CLAUDE.md Discovery

Launch a Haiku agent to find relevant CLAUDE.md files:
- Root CLAUDE.md (if it exists)
- CLAUDE.md files in directories modified by the PR

Return file paths only, not contents.

### Step 3: PR Summary and Change Detection

Launch a Haiku agent to view the PR and return:
- A summary of the change
- Change detection flags from the diff:
  - `hasErrorHandling`: try/catch/except/finally/.catch blocks modified
  - `hasTestFiles`: files matching *test*, *spec*, __tests__/*, tests/*
  - `hasNewTypes`: new interface/type/class/struct/enum/@dataclass definitions

### Step 4: Parallel Review Agents

Launch parallel Sonnet agents. Each returns issues with description, reason, and `suggested_severity` (CRITICAL/HIGH/MEDIUM/LOW).

**Core agents (always run):**
1. Audit changes for CLAUDE.md compliance
2. Shallow scan for obvious bugs in changes only (avoid nitpicks)
3. Read git blame and history to identify bugs in light of historical context
4. Check previous PRs that touched these files for relevant comments
5. Ensure changes comply with guidance in code comments

**Conditional agents (run in parallel with core agents):**
6. If `hasErrorHandling`: Launch **silent-failure-hunter** agent for error handling analysis
7. If `hasTestFiles`: Launch **pr-test-analyzer** agent for test coverage review
8. If `hasNewTypes`: Launch **type-design-analyzer** agent for type design analysis

### Step 5: Confidence and Severity Scoring

For each issue from Step 4, launch a parallel Haiku agent returning:
- **Confidence score** (0-100)
- **Severity classification**: CRITICAL, HIGH, MEDIUM, or LOW

**Confidence scale:**
- 0: False positive or pre-existing issue
- 25: Might be real, might be false positive; stylistic issues not in CLAUDE.md
- 50: Real issue but might be a nitpick
- 75: Very likely real and will be hit in practice; mentioned in CLAUDE.md
- 100: Absolutely certain; will happen frequently

**Severity definitions:**
- CRITICAL: Security vulnerabilities, data loss, crashes, breaking API changes
- HIGH: Logic bugs, broken functionality, significant performance issues, missing critical error handling
- MEDIUM: Code quality, minor performance, explicit CLAUDE.md style violations
- LOW: Minor style, documentation, refactoring suggestions

### Step 6: Filter Low-Confidence Issues

Filter out issues with confidence score below 80. If no issues pass, skip to Step 7.

**Consensus scoring for borderline issues (60-85 confidence):**
1. Launch 2 additional independent Haiku agents to re-evaluate each borderline issue
2. Each scorer receives the PR diff, issue description, and CLAUDE.md files
3. Scorers must NOT be told the original score
4. Consensus rules:
   - 2 or more scores >= 80: Issue passes
   - 2 or more scores < 80: Issue fails
   - Final reported score = median of all 3 scores
   - Note "(consensus X/3)" if consensus was used

### Step 7: Re-Eligibility Check

Repeat the eligibility check from Step 1 to ensure the PR is still reviewable.

### Step 8: Post GitHub Comment

Use `gh pr comment` to post the review. Follow this format:

```markdown
### Code review

Found X issues (Y CRITICAL, Z HIGH, W MEDIUM):

**CRITICAL** [95% confidence]
1. Brief description (category: security/bug/CLAUDE.md/error-handling/test-coverage/type-design)

https://github.com/owner/repo/blob/FULL_SHA/path/file.ext#L10-L15

**HIGH** [88% confidence]
2. Brief description (category)

https://github.com/owner/repo/blob/FULL_SHA/path/file.ext#L25-L30
```

If no issues found:
```markdown
### Code review

No issues found. Checked for bugs, CLAUDE.md compliance, and specialized analysis:
- Error handling review (check mark)
- Test coverage analysis (check mark)
- Type design review (check mark)
```

## False Positive Avoidance

Do NOT report:
- Pre-existing issues
- Patterns that look like bugs but are intentional
- Pedantic nitpicks a senior engineer would not flag
- Issues a linter/typechecker/compiler would catch (CI handles these)
- General code quality issues unless explicitly required in CLAUDE.md
- Issues called out in CLAUDE.md but explicitly silenced in code
- Intentional functionality changes related to the broader change
- Real issues on lines the user did not modify

## Key Rules

- Do not check build signal or attempt to build/typecheck (CI handles this)
- Use `gh` for all GitHub interactions, not web fetch
- Create a todo list first
- Cite and link each issue with full SHA and line range
- Group issues by severity in the final comment (CRITICAL first)
- Link format: `https://github.com/owner/repo/blob/FULL_SHA/file.ext#L10-L15`
  - Use full git SHA (not abbreviated)
  - Line range format is L[start]-L[end]
  - Provide at least 1 line of context before and after
