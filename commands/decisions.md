---
description: Capture architectural decisions and proposals from session
---

# Decisions Document Generator

Capture decisions and proposals from the current session for future reference. Includes both implemented decisions and proposals still under consideration.

**Model**: Uses the model configured in `release-plugin.model` (see /release:changelog for model selection).

## How to Run

- **Standalone**: Run `/decisions` directly at any point to capture decisions from the current session
- **From /release:changelog**: After generating a changelog, you'll be prompted to run this command

## When to Use

- After discussing architectural approaches
- When a decision was made (implemented or not)
- When proposals were explored but deferred
- After comparing solutions or libraries
- When trade-offs were evaluated
- After a research or exploration session (even without code changes)

## Instructions

1. **Review current session** for:
   - Decisions made (with rationale)
   - Proposals discussed (implemented or not)
   - Approaches compared
   - Trade-offs evaluated
   - Ideas deferred for later

2. **Capture regardless of implementation status**:
   - **Implemented** → document the decision and link to changelog
   - **Not implemented** → document as proposal for future review
   - **Rejected** → document why it was rejected (valuable context)

3. **Organize by topic** for easy future reference

## Output Location

**If decisions.md doesn't exist**, ask the user:
"Where should I create the decisions document?"
1. Project root (`./decisions.md`)
2. Documentation folder (`./docs/decisions.md`)
3. Same location as changelog (if it exists)
4. Custom location (let user specify)

**If decisions.md already exists**, use its current location.

**If file exists:** Prepend new entries after the `# Decisions & Proposals` header (newest entries at top). Read existing content first to avoid duplicates.

## Output Format

Structure:

```markdown
# Decisions & Proposals

## [Topic/Feature Area] - YYYY-MM-DD

### Status: [Implemented | Proposed | Rejected | Deferred]

### Context
[What problem or question prompted this decision/proposal?]

### Proposal
[What was proposed or decided?]

### Options Considered
1. **[Option A]**: [Brief description]
   - Pros: [advantages]
   - Cons: [disadvantages]

2. **[Option B]**: [Brief description]
   - Pros: [advantages]
   - Cons: [disadvantages]

### Decision
[What was decided, or "Pending review" if still a proposal]

### Rationale
[Why this approach was chosen/proposed]

### Trade-offs Accepted
[What compromises come with this decision]

### References
- [Links to docs, samples, related PRs]

---
```

## Status Definitions

- **Implemented**: Decision was made and code was written (see changelog)
- **Proposed**: Idea discussed, not yet implemented, worth revisiting
- **Deferred**: Good idea, but intentionally postponed
- **Rejected**: Considered and decided against (document why)

## Arguments

If a specific topic is provided, scope to that area: $ARGUMENTS
