# Decisions & Proposals

## Split Changelog Into Two Files - 2025-12-19

### Status: Implemented

### Context
The original changelog format had both developer and public summaries in a single file, making it awkward to share release notes with users without exposing technical implementation details.

### Proposal
Split changelog output into two separate files: `changelog.md` for developer notes and `changelog-public.md` for user-facing release notes.

### Options Considered
1. **Single file with both sections**
   - Pros: Everything in one place, simpler
   - Cons: Can't share public notes without showing technical details

2. **Two separate files**
   - Pros: Clean separation, can share public notes directly
   - Cons: Two files to maintain

### Decision
Two separate files in the same location

### Rationale
User-facing release notes should be shareable without exposing internal implementation details. The public file can be directly used in app stores, release announcements, etc.

### Trade-offs Accepted
Two files instead of one, but cleaner separation of concerns

### References
- None

---

## WhatToTest Location Flexibility - 2025-12-19

### Status: Implemented

### Context
Initially assumed WhatToTest.txt must be in a `TestFlight/` folder per Apple requirements, but discovered some projects keep it at project root.

### Proposal
Make WhatToTest location configurable with TestFlight folder as the recommended default.

### Options Considered
1. **Hardcoded to TestFlight folder**
   - Pros: Follows Apple convention
   - Cons: Doesn't work for all project structures

2. **Configurable with default**
   - Pros: Flexible, works for any project
   - Cons: Extra prompt on first run

### Decision
Configurable with TestFlight folder as recommended default

### Rationale
Real-world usage showed the file location varies by project. Better to be flexible than prescriptive.

### Trade-offs Accepted
Extra configuration option, but accommodates more workflows

### References
- User feedback from testing in other projects

---

## Xcode Project Detection - 2025-12-19

### Status: Implemented

### Context
WhatToTest prompt was appearing for all projects, even non-iOS projects where TestFlight isn't relevant.

### Proposal
Auto-detect Xcode projects before showing WhatToTest prompt.

### Options Considered
1. **Always prompt**
   - Pros: Simple, user can decline
   - Cons: Annoying for non-iOS projects

2. **Auto-detect with override**
   - Pros: Smart defaults, still configurable
   - Cons: Detection might miss edge cases

### Decision
Auto-detect by checking for `.xcodeproj`, `.xcworkspace`, `Package.swift`, or `Podfile`. Allow override via config.

### Rationale
Most users won't want TestFlight prompts in non-iOS projects. Detection covers common cases, config handles edge cases.

### Trade-offs Accepted
Might miss unusual Xcode project structures, but config override available

### References
- None

---

## Plugin Structure - 2025-12-17

### Status: Implemented

### Context
When creating the changelog plugin, we needed to decide whether to structure it as a single plugin or as a marketplace containing multiple plugins.

### Proposal
Structure as a single plugin within a marketplace wrapper.

### Options Considered
1. **Single plugin repo**
   - Pros: Simpler structure, easier to maintain
   - Cons: Can't add more plugins to same repo later

2. **Marketplace with multiple plugins**
   - Pros: Can distribute multiple plugins from one repo
   - Cons: More complex structure, overkill for one plugin

### Decision
Single plugin - the repo is both a marketplace (has `marketplace.json`) and contains one plugin. Can expand later if needed.

### Rationale
Simpler to start with, and the marketplace.json wrapper allows future expansion without restructuring.

### Trade-offs Accepted
Commands are namespaced (e.g., `/release:changelog` instead of `/changelog`)

### References
- https://code.claude.com/docs/en/plugin-marketplaces

---

## Configuration Namespacing - 2025-12-17

### Status: Implemented

### Context
The plugin needs to store user preferences in `.claude/config.json`, but this file is shared across all plugins.

### Proposal
Namespace all config under `changelog-plugin` key to avoid conflicts with other plugins.

### Options Considered
1. **Flat keys** (`"changelog": {...}`)
   - Pros: Simpler, shorter paths
   - Cons: Risk of collision with other plugins

2. **Namespaced keys** (`"changelog-plugin": {"changelog": {...}}`)
   - Pros: No collision risk, clear ownership
   - Cons: Longer paths, slightly more complex

### Decision
Namespace under `changelog-plugin`

### Rationale
Safety over convenience - config conflicts would be confusing and hard to debug.

### Trade-offs Accepted
More verbose config paths

### References
- None

---

## Changelog Format - 2025-12-17

### Status: Implemented

### Context
Needed a changelog format that serves both developers (detailed technical info) and end users (simple release notes).

### Proposal
Problem/Solution format with two summaries: Internal Developer Summary and Public User-Facing Summary.

### Options Considered
1. **Standard Keep a Changelog format**
   - Pros: Industry standard, familiar
   - Cons: Lacks technical depth, single audience

2. **Problem/Solution with dual summaries**
   - Pros: Comprehensive developer notes, clean user notes, captures "why"
   - Cons: More verbose, more work to generate

### Decision
Problem/Solution with dual summaries

### Rationale
Developer notes capture institutional knowledge (why decisions were made, how things work). User-facing summary derived from developer notes ensures consistency.

### Trade-offs Accepted
Longer changelog files, but both audiences served well

### References
- User's existing documentation standards

---

## WhatToTest History - 2025-12-17

### Status: Implemented

### Context
WhatToTest.txt could either be overwritten each release or accumulate history.

### Proposal
Give user choice: prepend (keep history) or replace (fresh start).

### Options Considered
1. **Always overwrite**
   - Pros: Clean, only shows current release
   - Cons: Loses historical context

2. **Always prepend**
   - Pros: Full history preserved
   - Cons: File grows, may not be desired

3. **Ask user each time**
   - Pros: Flexibility
   - Cons: Extra prompt

### Decision
Ask user, with option to save preference to config

### Rationale
Different teams have different needs - some want clean files, some want history. Let them choose and persist the choice.

### Trade-offs Accepted
Extra prompt on first run

### References
- https://developer.apple.com/documentation/xcode/including-notes-for-testers-with-a-beta-release-of-your-app

---

## File Location Flexibility - 2025-12-17

### Status: Implemented

### Context
Different projects organize documentation differently - root, docs/, custom folders.

### Proposal
Prompt for location if file doesn't exist, with option to persist choice.

### Options Considered
1. **Fixed location (root)**
   - Pros: Simple, predictable
   - Cons: Doesn't fit all projects

2. **Configurable with prompt**
   - Pros: Flexible, remembers choice
   - Cons: Extra prompt on first run

### Decision
Configurable with prompt and persistence option

### Rationale
One-time prompt is worth the flexibility. Most users will set it once and forget.

### Trade-offs Accepted
Slightly more complex first-run experience

### References
- None

---

## Decisions Document Purpose - 2025-12-17

### Status: Implemented

### Context
Originally planned as `analysis.md` for research and deep-dives, but questioned whether Claude already tracks this.

### Proposal
Rename to `decisions.md` focused on decisions and proposals (implemented or not).

### Options Considered
1. **analysis.md** - Research, comparisons, deep-dives
   - Pros: Comprehensive technical documentation
   - Cons: Overlaps with what Claude tracks in session

2. **decisions.md** - Decisions and proposals only
   - Pros: Focused, captures what Claude doesn't persist (rationale)
   - Cons: Narrower scope

### Decision
decisions.md with status tracking (Implemented, Proposed, Deferred, Rejected)

### Rationale
Claude doesn't persist between sessions. Decisions and their rationale are the most valuable things to capture - they answer "why" months later.

### Trade-offs Accepted
Less comprehensive than full analysis docs, but more focused and useful

### References
- None
