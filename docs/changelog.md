# Changelog

## [1.0.5] - 2025-12-19

#### Model Selection Feature

**Problem**: Users running Opus may want to use a faster/cheaper model for changelog generation tasks, but there was no way to configure which model the plugin uses.

**Solution**: Added model selection prompt on first run with option to persist choice. Supports all Claude models with "inherit" (current model) as default.

**How It Works**:
- On first run of `/changelog`, prompts user to select model
- Options: Current model (inherit), Opus 4.5, Sonnet 4 (recommended), Haiku 3.5
- Offers to save preference to `.claude/config.json`
- All plugin commands (changelog, decisions, whattotest) use the same model setting
- Config setting: `changelog-plugin.model`

**Files Changed**:
- `commands/changelog.md` - Added Model Selection section and frontmatter
- `commands/decisions.md` - Added frontmatter and model note
- `commands/whattotest.md` - Added frontmatter and model note
- `README.md` - Added Model Selection documentation
- `.claude/config.json` - Added `model` option

**Dependencies**: None

**Known Issues**: None

---

## [1.0.4] - 2025-12-19

#### Localized WhatToTest Filename Support

**Problem**: TestFlight uses locale-specific filenames like `WhatToTest.en-US.txt` for US English, not just `WhatToTest.txt` which is a fallback. The plugin was only checking for the generic filename.

**Solution**: Updated WhatToTest command to detect and support localized filenames, with `WhatToTest.en-US.txt` as the new default for new files.

**How It Works**:
- Searches for existing files: `WhatToTest.txt`, `WhatToTest.en-US.txt`, and other locale variants (`.de.txt`, `.fr.txt`, etc.)
- Checks both `TestFlight/` folder and project root
- When existing file found, uses same location and filename
- When creating new file, defaults to `WhatToTest.en-US.txt`
- Always prompts to prepend or replace when file exists

**Files Changed**:
- `commands/whattotest.md` - Added localized filename detection and updated default
- `README.md` - Updated WhatToTest output reference to `.en-US.txt`

**Dependencies**: None

**Known Issues**: None

---

## [1.0.3] - 2025-12-19

#### Split Changelog Output

**Problem**: Having both developer and public summaries in one file made it harder to share release notes with users without exposing technical details.

**Solution**: Split changelog into two separate files - `changelog.md` for developer notes and `changelog-public.md` for user-facing release notes.

**How It Works**:
- `/changelog:changelog` now generates two files in the same location
- `changelog.md` contains Problem/Solution format with code snippets
- `changelog-public.md` contains user-friendly release notes
- `/changelog:whattotest` derives content from `changelog-public.md`

**Files Changed**:
- `commands/changelog.md` - Updated output format to two files
- `commands/whattotest.md` - Now references `changelog-public.md`
- `README.md` - Updated documentation

**Dependencies**: None

**Known Issues**: None

---

#### Explicit Config Persistence

**Problem**: Config file wasn't being created when users chose to save preferences - instructions were too vague.

**Solution**: Added step-by-step instructions for creating `.claude/config.json` with explicit file operations.

**Files Changed**:
- `commands/changelog.md` - Added detailed persistence instructions with mkdir and write steps

**Dependencies**: None

**Known Issues**: None

---

#### WhatToTest Location Flexibility

**Problem**: WhatToTest.txt location was hardcoded to `TestFlight/` folder, but some projects keep it at project root.

**Solution**: Made WhatToTest location configurable with TestFlight folder as recommended default.

**How It Works**:
- First run prompts for location: TestFlight folder (recommended), project root, or custom
- Preference can be saved to config as `changelog-plugin.whattotest.outputPath`

**Files Changed**:
- `commands/whattotest.md` - Added location prompt and config option
- `.claude/config.json` - Added `outputPath` to whattotest section

**Dependencies**: None

**Known Issues**: None

---

#### Xcode Project Detection for WhatToTest

**Problem**: WhatToTest prompt appeared even for non-iOS projects where TestFlight isn't relevant.

**Solution**: Added auto-detection for Xcode projects before prompting for WhatToTest.

**How It Works**:
- Checks for `.xcodeproj`, `.xcworkspace`, `Package.swift`, or `Podfile`
- Only prompts if Xcode project detected or `enabled: true` in config
- Can override with `changelog-plugin.whattotest.enabled` setting

**Files Changed**:
- `commands/changelog.md` - Added Xcode detection logic
- `.claude/config.json` - Added `enabled` option to whattotest

**Dependencies**: None

**Known Issues**: None

---

#### Simplified WhatToTest Format

**Problem**: WhatToTest header "What to Test - App v1.0.0" was redundant since the file is already named WhatToTest.txt.

**Solution**: Simplified header to just "[App Name] v[Version] - YYYY-MM-DD".

**Files Changed**:
- `commands/whattotest.md` - Updated output format template

**Dependencies**: None

**Known Issues**: None

---

## [1.0.0] - 2025-12-17

#### Changelog Plugin - Initial Release

**Problem**: Needed a way to automatically generate comprehensive changelogs from Claude Code sessions, capturing both technical details for developers and user-friendly summaries for release notes.

**Solution**: Created a Claude Code plugin with three interconnected slash commands that work together to document releases.

```json
{
  "name": "changelog",
  "version": "1.0.0",
  "description": "Release documentation automation - generates changelogs, captures decisions, and creates TestFlight testing guides"
}
```

**How It Works**:
- `/changelog:changelog` - Analyzes current session context as primary source, cross-references with git to deduplicate, generates Problem/Solution format with code snippets
- `/changelog:decisions` - Captures architectural decisions regardless of implementation status (Implemented, Proposed, Deferred, Rejected)
- `/changelog:whattotest` - Derives TestFlight WhatToTest.txt from the changelog's public-facing summary

Commands can chain together: changelog → decisions → whattotest, or run standalone.

**Files Changed**:
- `.claude-plugin/plugin.json` - Plugin manifest with metadata
- `.claude-plugin/marketplace.json` - Marketplace definition for distribution
- `.claude/config.json` - Sample configuration with namespaced settings
- `commands/changelog.md` - Main changelog generation command
- `commands/decisions.md` - Architectural decisions capture command
- `commands/whattotest.md` - TestFlight WhatToTest generator
- `README.md` - Plugin installation and usage guide
- `LICENSE` - MIT license

**Dependencies**: None

**Known Issues**:
- Commands are namespaced as `changelog:*` (e.g., `/changelog:changelog`) when installed via marketplace

---

#### Configuration System

**Problem**: Users need persistent preferences for output locations and follow-up behavior without being prompted every time.

**Solution**: Implemented namespaced configuration under `changelog-plugin` key in `.claude/config.json`.

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

**How It Works**: Commands check config first, prompt if no setting exists, offer to save choices for future runs. Command-line flags override config.

**Files Changed**:
- `.claude/config.json` - Sample configuration
- `commands/changelog.md` - Config documentation and lookup instructions

**Dependencies**: None

**Known Issues**: None

---

#### File Location Prompts

**Problem**: Different projects organize documentation differently - some at root, some in docs/, some custom.

**Solution**: Commands prompt for location on first run, with option to persist choice.

**How It Works**:
- Changelog/Decisions: Ask for root, docs/, or custom location
- Existing files: Prepend new entries (newest at top)
- WhatToTest existing: Ask to prepend or replace

**Files Changed**:
- `commands/changelog.md` - Output location section
- `commands/decisions.md` - Output location section
- `commands/whattotest.md` - Output location section

**Dependencies**: None

**Known Issues**: None
