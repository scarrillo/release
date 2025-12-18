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

3. **Generate two summaries**:

### Internal Developer Summary
Create a technical summary for the development team that includes:
- Detailed technical changes
- Files modified and why
- Architecture or implementation notes
- Migration steps if applicable
- Known issues or limitations

### Public User-Facing Summary
Create a user-friendly summary that includes:
- Features described in terms of user benefits
- Improvements written in plain language
- Bug fixes described by the problem they solve (not technical details)
- Skip internal refactoring or technical debt items

## Output Format

Create or update a changelog document with the following structure:

```markdown
# Changelog

## [Version] - YYYY-MM-DD

### Internal Developer Summary

#### Features
- [Technical description of feature]

#### Improvements
- [Technical description of improvement]

#### Bug Fixes
- [Technical description of fix]

---

### Public User-Facing Summary

#### What's New
- [User-friendly feature description]

#### Improvements
- [User-friendly improvement description]

#### Bug Fixes
- [User-friendly fix description]
```

## Arguments

If a version number or date range is provided as an argument, scope the changelog to that specific version or time period: $ARGUMENTS
