# Changelog Generator

Generate a comprehensive changelog document based on recent changes in this project.

## Instructions

1. **Analyze recent changes** using this priority order:

   **Primary source - Current session:**
   - Review all work completed in this conversation session
   - Include files created, modified, features implemented, and bugs fixed
   - This is the most accurate and detailed source of recent work

   **Cross-reference with git to deduplicate:**
   - Check recent git commit history (`git log`)
   - Check staged changes (`git diff --staged`)
   - Check unstaged changes (`git diff`)
   - Use git data to verify session work and avoid duplicate entries
   - If work from the session has already been committed, use the commit info to enrich the changelog entry (commit hash, timestamp)

   **Handle uncommitted session work:**
   - If session work is not yet committed, ask the user:
     1. "Commit first" - Let the user commit their changes, then proceed
     2. "Include uncommitted" - Generate changelog including uncommitted session work
     3. "Skip uncommitted" - Only include work that has been committed

   **Reference existing changelogs:**
   - Check any existing changelog files for format consistency and to avoid re-documenting old changes

2. **Categorize changes** into:
   - **Features**: New functionality added
   - **Improvements**: Enhancements to existing features
   - **Bug Fixes**: Issues that were resolved
   - **Breaking Changes**: Changes that may affect existing functionality (if any)

3. **Generate two summaries** (in this order):

### Internal Developer Summary (write this first)
Create comprehensive technical notes for the development team using **Problem/Solution format**. This is the authoritative record of all changes.

For each change, include:
- **Problem**: What issue or need prompted this change?
- **Solution**: How was it solved? Include relevant code snippets.
- **How It Works**: For complex features, explain the implementation logic
- **Files Changed**: List of files modified/created with brief description of each
- **Dependencies**: Any packages added/updated
- **Migration Notes**: Steps needed if applicable
- **Known Issues**: Any limitations or issues to be aware of

### Public User-Facing Summary (derive from developer summary)
Based on the Internal Developer Summary above, create a simplified user-friendly version:
- Translate technical features into user benefits
- Rewrite improvements in plain, non-technical language
- Describe bug fixes by the problem they solve (not the technical fix)
- **Omit**: Internal refactoring, technical debt, developer tooling changes, implementation details users don't need to know

## Output Format

Create or update a changelog document with the following structure:

```markdown
# Changelog

## [Version] - YYYY-MM-DD

### Internal Developer Summary

#### [Feature/Fix Name]

**Problem**: [Description of the issue or need]

**Solution**: [How it was solved]

```swift
// Relevant code snippet showing the implementation
```

**How It Works**: [For complex features, explain the logic]

**Files Changed**:
- `path/to/file.swift` - [what changed]
- `path/to/another.swift` - [what changed]

**Dependencies**: [Any added/updated, or "None"]

**Known Issues**: [Any limitations, or "None"]

---

### Public User-Facing Summary

#### What's New
- [User-friendly feature description]

#### Improvements
- [User-friendly improvement description]

#### Bug Fixes
- [User-friendly fix description]
```

## After Changelog Generation

Once the changelog is complete, ask the user about follow-up documentation:

1. **Decisions**: "Were there any decisions or proposals discussed this session that should be documented?"
   - If yes, run /decisions to capture decisions, proposals, and trade-offs from the session
   - This includes implemented decisions, deferred ideas, and rejected approaches

2. **WhatToTest**: "Would you like me to generate TestFlight WhatToTest.txt from the public-facing summary?"
   - If yes, use the **Public User-Facing Summary** to generate WhatToTest.txt
   - Do not re-analyze - derive directly from the public summary

## Arguments

If a version number or date range is provided as an argument, scope the changelog to that specific version or time period: $ARGUMENTS
