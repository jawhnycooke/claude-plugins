---
name: coauthoring-documents
description: Guides collaborative creation of structured documents through a three-stage workflow of context gathering, iterative refinement, and reader testing. Activates when the user drafts documentation, proposals, technical specs, or decision documents that benefit from structured co-authoring.
---

# Document Co-Authoring Workflow

A structured approach to collaborative document creation that produces high-quality, reader-tested content.

## When to Offer This Workflow

Suggest this workflow when users mention:
- Writing documentation or guides
- Creating proposals or pitches
- Drafting technical specifications
- Preparing decision documents
- Developing any structured content

**Give users agency** - they can skip stages or work freeform if preferred.

## The Three Stages

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  STAGE 1        │     │  STAGE 2        │     │  STAGE 3        │
│  Context        │ ──► │  Refinement     │ ──► │  Reader         │
│  Gathering      │     │  & Structure    │     │  Testing        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

---

## Stage 1: Context Gathering

**Goal**: Close knowledge gaps through meta-questions before writing.

### Questions to Cover

1. **Document type**: What kind of document is this?
2. **Audience**: Who will read this? What do they already know?
3. **Desired impact**: What should readers think/feel/do after reading?
4. **Format constraints**: Length, style guide, template requirements?
5. **Success criteria**: How will you know if it's good?

### Information Gathering

Encourage users to "information dump":
- Background context and history
- Previous discussions and decisions
- Constraints and requirements
- Existing materials to reference
- Stakeholder concerns

### Exit Criteria

Move to Stage 2 when you have sufficient context to:
- Understand edge cases
- Anticipate reader questions
- Make informed structural decisions

---

## Stage 2: Refinement & Structure

**Goal**: Build the document section by section with iterative refinement.

### Section-by-Section Process

For each section:

1. **Ask clarifying questions** about this section's content
2. **Brainstorm options** (5-20 possibilities)
3. **Curate selections** - identify best approaches
4. **Check for gaps** - what's missing?
5. **Draft content** - write the section
6. **Iterate** - refine based on feedback

### Working Strategy

- **Start with unknowns**: Begin with sections that have the most uncertainty
- **Use scaffolding**: Create structure first, then fill in details
- **Make surgical edits**: Use str_replace for targeted changes, not full rewrites
- **Track preferences**: Note what the user likes/dislikes for consistency

### Editing Approach

```
DON'T: Reprint entire document for small changes
DO: Make targeted edits to specific sections

Example:
"I'll update the introduction paragraph. Here's the change:

Old: The system handles user authentication...
New: The authentication system provides secure user verification..."
```

### Exit Criteria

Move to Stage 3 when:
- All sections are drafted
- User is satisfied with overall structure
- Major content decisions are finalized

---

## Stage 3: Reader Testing

**Goal**: Verify the document works for readers without author context.

### Testing Methods

#### Option A: Fresh Claude Instance

Use a sub-agent to read the document cold:

```
"Read this document as if you're the target audience.
What questions do you have?
What's confusing or unclear?
What assumptions does it make?"
```

#### Option B: Manual Testing

User can:
1. Open a new conversation
2. Paste the document
3. Ask for critique from target audience perspective

### What to Check

- [ ] **Ambiguity**: Are any statements open to misinterpretation?
- [ ] **False assumptions**: Does it assume knowledge readers may not have?
- [ ] **Contradictions**: Do any parts conflict with each other?
- [ ] **Missing context**: Are there unexplained references?
- [ ] **Unclear conclusions**: Are takeaways explicit and actionable?

### Predict Reader Questions

For each section, ask:
- What will readers want to know next?
- What might they disagree with?
- What needs more evidence or examples?

### Iteration Loop

```
Reader Testing → Identify Issues → Targeted Fixes → Re-test
```

Repeat until:
- No major questions remain
- Document stands alone without author explanation

---

## Key Principles

### Quality Over Speed

- Take time to explore options
- Don't rush through stages
- Better to iterate more than publish prematurely

### Meaningful Improvements

Each iteration should make substantial improvements:
- Not just wordsmithing
- Address actual content gaps
- Improve clarity and structure

### User Agency

- Respect user's preferred pace
- Allow skipping or combining stages
- Adapt to their working style

### Context Accumulation

- Don't let gaps accumulate
- Address uncertainties when they arise
- Build understanding incrementally

---

## Document Types & Templates

### Technical Specification

```markdown
# [Feature Name] Technical Specification

## Overview
[1-2 sentence summary]

## Background
[Context and motivation]

## Requirements
### Functional Requirements
### Non-Functional Requirements

## Proposed Solution
### Architecture
### Implementation Details
### Alternatives Considered

## Timeline & Milestones

## Open Questions
```

### Decision Document

```markdown
# [Decision Title]

## Status
[Proposed/Accepted/Deprecated]

## Context
[What is the issue we're addressing?]

## Decision
[What was decided]

## Consequences
### Positive
### Negative

## Alternatives Considered
```

### User Documentation

```markdown
# [Feature/Product] Guide

## Overview
[What this does and why it matters]

## Getting Started
[Quick start steps]

## Core Concepts
[Key ideas to understand]

## How-To Guides
[Task-oriented instructions]

## Reference
[Detailed specifications]

## Troubleshooting
[Common issues and solutions]
```

---

## Tips for Success

1. **Front-load context gathering** - invest time upfront to save iterations later
2. **Be explicit about stage transitions** - "Ready to move to refinement?"
3. **Encourage rough drafts** - perfect is the enemy of good
4. **Test with real scenarios** - use concrete examples in reader testing
5. **Document decisions** - note why certain approaches were chosen
