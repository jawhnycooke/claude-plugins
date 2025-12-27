---
allowed-tools: Bash(gh issue view:*), Bash(gh search:*), Bash(gh issue list:*), Bash(gh pr comment:*), Bash(gh pr diff:*), Bash(gh pr view:*), Bash(gh pr list:*)
description: Code review a pull request
disable-model-invocation: false
---

Provide a comprehensive code review for the given pull request.

To do this, follow these steps precisely:

## Step 1: Eligibility Check
Use a Haiku agent to check if the pull request (a) is closed, (b) is a draft, (c) does not need a code review (eg. because it is an automated pull request, or is very simple and obviously ok), or (d) already has a code review from you from earlier. If so, do not proceed.

## Step 2: CLAUDE.md Discovery
Use another Haiku agent to give you a list of file paths to (but not the contents of) any relevant CLAUDE.md files from the codebase: the root CLAUDE.md file (if one exists), as well as any CLAUDE.md files in the directories whose files the pull request modified.

## Step 3: PR Summary + Change Detection
Use a Haiku agent to view the pull request and return:
- A summary of the change
- **Change detection flags** (analyze the diff):
  - `hasErrorHandling`: true if try/catch/except/finally/.catch blocks are modified
  - `hasTestFiles`: true if files match *test*, *spec*, __tests__/*, tests/*
  - `hasNewTypes`: true if new interface/type/class/struct/enum/@dataclass definitions are introduced
  - `hasCommentChanges`: true if docstrings, JSDoc comments, or significant inline comments are added/modified

## Step 4: Parallel Review Agents
Launch parallel Sonnet agents to independently code review. Each agent returns issues with description, reason, and **suggested_severity** (CRITICAL/HIGH/MEDIUM/LOW).

**Core Agents (always run):**
a. Agent #1: Audit changes for CLAUDE.md compliance. Not all instructions apply during code review.
b. Agent #2: Shallow scan for obvious bugs in changes only. Focus on large bugs, avoid nitpicks.
c. Agent #3: Read git blame and history to identify bugs in light of historical context.
d. Agent #4: Check previous PRs that touched these files for relevant comments.
e. Agent #5: Ensure changes comply with guidance in code comments.

**Specialized Agents (conditional, run in parallel with core agents):**
f. Agent #6 (if hasErrorHandling): Use the **silent-failure-hunter** agent to identify silent failures, inadequate error handling, empty catch blocks, and inappropriate fallback behavior.
g. Agent #7 (if hasTestFiles): Use the **pr-test-analyzer** agent to review test coverage quality, identify critical gaps, and evaluate test quality.
h. Agent #8 (if hasNewTypes): Use the **type-design-analyzer** agent to analyze type encapsulation, invariant expression, and rate type design quality.
i. Agent #9 (if hasCommentChanges): Use the **comment-analyzer** agent to verify comment accuracy, identify misleading documentation, and prevent comment rot.

## Step 5: Confidence + Severity Scoring
For each issue found in Step 4, launch a parallel Haiku agent that returns:
- **Confidence score** (0-100): How certain the issue is real
- **Severity classification**: CRITICAL, HIGH, MEDIUM, or LOW

**Confidence Scale** (give this rubric to the agent verbatim):
- 0: Not confident at all. False positive or pre-existing issue.
- 25: Somewhat confident. Might be real, might be false positive. Stylistic issues not in CLAUDE.md.
- 50: Moderately confident. Real issue but might be a nitpick. Not very important relative to the PR.
- 75: Highly confident. Very likely real and will be hit in practice. Important or mentioned in CLAUDE.md.
- 100: Absolutely certain. Definitely real and will happen frequently.

**Severity Definitions:**
- CRITICAL: Security vulnerabilities, data loss, crashes, breaking API changes
- HIGH: Logic bugs, broken functionality, significant performance issues, missing critical error handling
- MEDIUM: Code quality, minor performance, incomplete error messages, explicit CLAUDE.md style violations
- LOW: Minor style, documentation, refactoring suggestions, nitpicks not in CLAUDE.md

## Step 6: Filter Low-Confidence Issues
Filter out any issues with a confidence score less than 80. If there are no issues that meet this criteria, skip to Step 7.

## Step 6a: Consensus Scoring (Borderline Issues)
For any issues with confidence scores between 60-85 (borderline):
1. Launch 2 additional independent Haiku agents to re-evaluate each borderline issue
2. Each scorer receives the PR diff, issue description, and CLAUDE.md files
3. Scorers should NOT be told the original score
4. **Consensus rules:**
   - Collect all 3 scores (original + 2 new)
   - If 2 or more scores >= 80: Issue passes (include in output)
   - If 2 or more scores < 80: Issue fails (exclude from output)
   - Final reported score = median of all 3 scores
   - Note "(consensus X/3)" in output if consensus was used

## Step 7: Re-Eligibility Check
Use a Haiku agent to repeat the eligibility check from Step 1, to make sure that the pull request is still eligible for code review.

## Step 8: Post GitHub Comment
Use the gh bash command to comment back on the pull request. Follow this format precisely:

---

### Code review

Found X issues (Y CRITICAL, Z HIGH, W MEDIUM):

**CRITICAL** [95% confidence]
1. Brief description (category: security/bug/CLAUDE.md/error-handling/test-coverage/type-design)

https://github.com/owner/repo/blob/FULL_SHA/path/file.ext#L10-L15

**HIGH** [88% confidence]
2. Brief description (category)

https://github.com/owner/repo/blob/FULL_SHA/path/file.ext#L25-L30

**MEDIUM** [82% confidence, consensus 3/3]
3. Brief description (category)

https://github.com/owner/repo/blob/FULL_SHA/path/file.ext#L45-L50

---

Or, if no issues found:

---

### Code review

No issues found. Checked for bugs, CLAUDE.md compliance, and specialized analysis:
- Error handling review ✓
- Test coverage analysis ✓
- Type design review ✓
- Comment accuracy review ✓

---

## Step 9: Simplification Suggestion (Optional)
After posting the review comment, if the review passed with **no CRITICAL or HIGH issues**:

1. Inform the user: "The code review found no critical issues. Would you like me to run the **code-simplifier** agent to improve code clarity and maintainability?"
2. If the user agrees, launch the **code-simplifier** agent on the changed files
3. The code-simplifier will:
   - Analyze the changed code for clarity improvements
   - Suggest simplifications while preserving functionality
   - Apply project coding standards
   - Remove redundancy and improve readability

Note: This step is optional and requires user confirmation before proceeding.

## Examples of False Positives

- Pre-existing issues
- Something that looks like a bug but is not actually a bug
- Pedantic nitpicks that a senior engineer wouldn't call out
- Issues a linter/typechecker/compiler would catch (CI handles these)
- General code quality issues, unless explicitly required in CLAUDE.md
- Issues called out in CLAUDE.md but explicitly silenced in code
- Intentional functionality changes related to the broader change
- Real issues on lines the user did not modify

## Notes

- Do not check build signal or attempt to build/typecheck. CI handles this.
- Use `gh` for all GitHub interactions, not web fetch
- Make a todo list first
- You must cite and link each issue with full SHA and line range
- Group issues by severity in the final comment (CRITICAL first)
- When linking: `https://github.com/owner/repo/blob/FULL_SHA/file.ext#L10-L15`
  - Requires full git SHA (not abbreviated)
  - Line range format is L[start]-L[end]
  - Provide at least 1 line of context before and after
