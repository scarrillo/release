# Decisions Document Generator

Capture decisions and proposals from the current session for future reference. Includes both implemented decisions and proposals still under consideration.

## How to Run

- **Standalone**: Run `/decisions` directly at any point to capture decisions from the current session
- **From /changelog**: After generating a changelog, you'll be prompted to run this command

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

## Output Format

Create or update `decisions.md` at the project root with this structure:

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
