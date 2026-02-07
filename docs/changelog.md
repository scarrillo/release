# Changelog

## [1.3.3] - 2026-02-06

#### WhatToTest Default Output and Prepend Behavior

**Problem**: WhatToTest command used `WhatToTest.txt` as the default filename and didn't respect the `onExisting` config setting for prepend/replace behavior.

**Solution**: Updated WhatToTest to default to `TestFlight/WhatToTest.en-US.txt` and respect the `release-plugin.whattotest.onExisting` config.

**How It Works**:
- Default output is now `./TestFlight/WhatToTest.en-US.txt` (localized)
- Removed non-localized `WhatToTest.txt` option and project root location
- `onExisting` config: `"prepend"` (auto), `"replace"` (auto), or `"ask"` (default, with prepend recommended)
- All doc commands (changelog, decisions, whattotest) now default to prepend

**Files Changed**:
- `commands/whattotest.md` - Updated default path, removed WhatToTest.txt, added onExisting config support
- `commands/changelog.md` - Fixed stale WhatToTest.txt references in config section

**Dependencies**: None

**Known Issues**: None

---

#### Xcode Build Command

**Problem**: No way to build iOS/macOS projects from within the plugin workflow.

**Solution**: Added `/release:xcbuild` command for building with xcodebuild, with auto-detection of projects and simulators.

**How It Works**:
- Auto-detects `.xcworkspace` or `.xcodeproj` in current directory
- Lists available schemes and recommends main app target
- Queries available simulators and recommends latest iPhone Pro
- Caches scheme to `.claude/config.json` and simulator to `.claude/config.local.json`
- Arguments: `--change` (re-select), `--scheme=<name>`, `--id=<UUID>`

**Files Changed**:
- `commands/xcbuild.md` - New build command
- `README.md` - Added xcbuild to commands table and config section

**Dependencies**: Requires Xcode and xcodebuild CLI

**Known Issues**: None

---

#### Plugin Metadata and Contributor Docs

**Problem**: Plugin was missing marketplace metadata, had incorrect author field (`url` instead of `email`), limited keywords, and no contributor documentation.

**Solution**: Added marketplace metadata, expanded keywords, fixed author fields, and added CONTRIBUTING.md and GitHub issue templates.

**Files Changed**:
- `.claude-plugin/plugin.json` - Fixed author.email, expanded keywords (22 total)
- `.claude-plugin/marketplace.json` - Added metadata section, expanded keywords, added tags
- `CONTRIBUTING.md` - New contributor guidelines
- `.github/ISSUE_TEMPLATE/bug_report.md` - New bug report template
- `.github/ISSUE_TEMPLATE/feature_request.md` - New feature request template
- `README.md` - Updated title, version badge

**Dependencies**: None

**Known Issues**: None

---

## [1.2.0] - 2025-12-23

#### Claude Plugin Release Support

**Problem**: The `/release:release` command only supported Xcode projects, so the plugin couldn't release itself since it's a Claude marketplace plugin.

**Solution**: Added Claude Plugin as a supported project type for version management.

**How It Works**:
- Detects `.claude-plugin/plugin.json` in project root (checked before Xcode)
- Reads current version from `plugin.json`
- Updates version in both `plugin.json` and `marketplace.json` (plugins[0].version)
- Detection is scoped to project root only to avoid finding installed plugins in subdirectories

**Files Changed**:
- `commands/release.md` - Added Claude Plugin detection, version reading, and update instructions
- `README.md` - Updated feature list to show Claude Plugin support

**Dependencies**: None

**Known Issues**: None

---

## [1.1.0] - 2025-12-23

#### Plugin Renamed to "Release"

**Problem**: The plugin started as a changelog generator but has grown to include version management, decisions tracking, and TestFlight integration. The name "changelog" no longer reflected its full capabilities.

**Solution**: Renamed plugin from "changelog" to "release" - a comprehensive release automation toolkit.

**Breaking Changes**:
- Plugin name: `changelog` → `release`
- Command namespace: `/changelog:*` → `/release:*`
- Config namespace: `changelog-plugin` → `release-plugin`
- Marketplace: `scarrillo/changelog` → `scarrillo/release`

**Migration**:
1. Uninstall old plugin: `/plugin uninstall changelog@scarrillo`
2. Remove old marketplace: `/plugin marketplace remove scarrillo`
3. Add new marketplace: `/plugin marketplace add scarrillo/release`
4. Install new plugin: `/plugin install release@scarrillo`
5. Update config namespace in `.claude/config.json` from `changelog-plugin` to `release-plugin`

**How It Works**:
- Commands remain the same: changelog, decisions, whattotest, release
- New namespace: `/release:changelog`, `/release:decisions`, `/release:whattotest`, `/release:release`
- Default command when running `/release` is now `/release:release`

**Files Changed**:
- `.claude-plugin/plugin.json` - Renamed to "release"
- `.claude-plugin/marketplace.json` - Updated marketplace and plugin names
- `.claude/config.json` - Namespace changed to `release-plugin`
- `README.md` - All references updated
- `commands/*.md` - Config references updated
- `TestFlight/WhatToTest.txt` - Command references updated

**Dependencies**: None

**Known Issues**: Users must manually migrate from the old plugin.

---

#### Release Command Enhancements

**Problem**: The release command needed better integration with documentation and git permission handling.

**Solution**: Added documentation prompt before commits and configurable git execution mode.

**How It Works**:
- Before committing, prompts to run `/release:changelog` to include docs in release
- Asks user preference for git command execution (auto vs manual)
- Verifies git permissions when auto mode selected
- Persists git mode preference to config

**Files Changed**:
- `commands/release.md` - Added documentation prompt and git mode configuration

**Dependencies**: None

**Known Issues**: None

---

## [1.0.6] - 2025-12-23

#### Release Command

**Problem**: Releasing iOS apps requires manually incrementing version numbers, updating Xcode project files, committing, and creating git tags. This is repetitive and error-prone.

**Solution**: Added `/release:release` command that automates semantic versioning for Xcode projects with support for future platforms.

**How It Works**:
- Detects Xcode project (`.xcodeproj`) automatically
- Reads current `MARKETING_VERSION` from `project.pbxproj`
- Prompts for increment type: patch, minor, major, or tag
- Updates all version occurrences in project file
- Commits with message "Increment version to X.Y.Z"
- Creates git tag `rel.vX.Y.Z`
- `tag` option creates or updates tag without version change (useful for CI/CD triggers)

**Files Changed**:
- `commands/release.md` - New release command
- `README.md` - Added release command documentation

**Dependencies**: None

**Known Issues**: Currently only supports Xcode projects. Next.js, Android, Flutter support planned.

---

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
- `/release:changelog` now generates two files in the same location
- `changelog.md` contains Problem/Solution format with code snippets
- `changelog-public.md` contains user-friendly release notes
- `/release:whattotest` derives content from `changelog-public.md`

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
- `/release:changelog` - Analyzes current session context as primary source, cross-references with git to deduplicate, generates Problem/Solution format with code snippets
- `/release:decisions` - Captures architectural decisions regardless of implementation status (Implemented, Proposed, Deferred, Rejected)
- `/release:whattotest` - Derives TestFlight WhatToTest.txt from the changelog's public-facing summary

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
- Commands are namespaced as `release:*` (e.g., `/release:changelog`) when installed via marketplace

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
