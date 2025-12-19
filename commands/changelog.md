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

## Output Location

**If changelog.md doesn't exist**, ask the user:
"Where should I create the changelog?"
1. Project root (`./changelog.md`)
2. Documentation folder (`./docs/changelog.md`)
3. Custom location (let user specify)

**If changelog.md already exists**, use its current location.

**If file exists:** Prepend new entries after the `# Changelog` header (newest entries at top). Read existing content first to avoid duplicates and maintain format consistency.

## Output Format

Structure:

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

2. **WhatToTest** (Xcode projects only):
   - Check config for `changelog-plugin.whattotest.enabled` setting
   - If not set, detect if this is an Xcode project by checking for: `.xcodeproj`, `.xcworkspace`, `Package.swift`, or `Podfile`
   - If Xcode project detected (or `enabled: true` in config):
     - Ask: "Would you like me to generate TestFlight WhatToTest.txt from the public-facing summary?"
     - Offer to save preference: "Save this as default for this project?"
   - If not an Xcode project (and no config override), skip this prompt
   - If yes, use the **Public User-Facing Summary** to generate WhatToTest.txt
   - Do not re-analyze - derive directly from the public summary

## Arguments

$ARGUMENTS

**Version/date range**: Scope changelog to specific version or time period
- `/changelog v1.2.0`
- `/changelog 2024-01-01`

**Follow-up behavior flags**:
- `--auto` - Automatically run both /decisions and /whattotest without asking
- `--ask` - Ask before running each follow-up (default)
- `--skip` - Skip all follow-ups, only generate changelog
- `--decisions` - Auto-run /decisions only
- `--whattotest` - Auto-run /whattotest only
- `--no-decisions` - Skip /decisions prompt
- `--no-whattotest` - Skip /whattotest prompt

**Examples**:
- `/changelog --auto` - Generate all docs without prompts
- `/changelog --skip` - Only changelog, no follow-ups
- `/changelog v1.2.0 --decisions --no-whattotest` - Version 1.2.0, auto-run decisions, skip whattotest

## Configuration File

Check for `.claude/config.json` at project root. Look for settings under the **`changelog-plugin`** namespace:

```json
{
  "changelog-plugin": {
    "changelog": {
      "outputPath": "./changelog.md",
      "followUp": {
        "decisions": "ask",
        "whattotest": "ask"
      }
    },
    "decisions": {
      "outputPath": "./decisions.md"
    },
    "whattotest": {
      "onExisting": "ask"
    }
  }
}
```

### Config Options

**changelog-plugin.changelog.outputPath**: `"./changelog.md"` | `"./docs/changelog.md"` | custom path
**changelog-plugin.changelog.followUp.decisions**: `"always"` | `"ask"` | `"never"`
**changelog-plugin.changelog.followUp.whattotest**: `"always"` | `"ask"` | `"never"`
**changelog-plugin.decisions.outputPath**: `"./decisions.md"` | `"./docs/decisions.md"` | custom path
**changelog-plugin.whattotest.enabled**: `"auto"` (detect Xcode project) | `true` | `false`
**changelog-plugin.whattotest.outputPath**: `"./TestFlight/WhatToTest.txt"` | `"./WhatToTest.txt"` | custom path
**changelog-plugin.whattotest.onExisting**: `"prepend"` | `"replace"` | `"ask"`

### Persisting User Choices

When prompting the user for a location or preference, offer to save their choice:
"Would you like to save this preference to `.claude/config.json` for future runs?"

If yes:
1. Check if `.claude/config.json` exists in the project root
2. If not, create the `.claude/` directory and `config.json` file
3. Read existing config (if any) to preserve other settings
4. Add/update the setting under the `changelog-plugin` namespace
5. Write the updated config back to the file

Example - saving changelog outputPath:
```bash
# Create .claude directory if needed
mkdir -p .claude

# Write config with the user's preference
```
```json
{
  "changelog-plugin": {
    "changelog": {
      "outputPath": "./docs/changelog.md"
    }
  }
}
```

Command-line arguments override config file settings.
