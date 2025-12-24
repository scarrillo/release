# Release Notes

## [1.1.0] - 2025-12-23

### Breaking Changes
- **Plugin Renamed** - "Changelog Plugin" is now "Release Plugin"
- **New Command Namespace** - Commands are now `/release:*` instead of `/changelog:*`
- **Config Migration** - Update config namespace from `changelog-plugin` to `release-plugin`

### What's New
- **Expanded Scope** - Now a comprehensive release automation toolkit with semantic versioning, changelogs, decisions, and TestFlight integration

### Improvements
- **Documentation Before Release** - `/release:release` now prompts to generate changelog before committing
- **Git Execution Modes** - Choose between Claude running git commands or displaying them for manual execution
- **Permission Verification** - Confirms git permissions before auto-executing commands

### Migration Guide
```bash
/plugin uninstall changelog@scarrillo
/plugin marketplace remove scarrillo
/plugin marketplace add scarrillo/release
/plugin install release@scarrillo
```

---

## [1.0.6] - 2025-12-23

### What's New
- **Release Command** - New `/release:release` command automates version increments, commits, and git tags for iOS releases

### Improvements
- **SemVer Support** - Increment major, minor, or patch versions following semantic versioning
- **Xcode Auto-Detection** - Automatically finds and updates your Xcode project
- **CI/CD Integration** - `tag` option triggers builds without incrementing version

---

## [1.0.5] - 2025-12-19

### What's New
- **Model Selection** - Choose which Claude model to use for changelog generation. Opus users can now hand off to faster models like Sonnet or Haiku.

### Improvements
- **Persistent Preferences** - Model choice can be saved to project config for future runs
- **Consistent Model Usage** - All plugin commands use the same model setting

---

## [1.0.4] - 2025-12-19

### Improvements
- **Localized TestFlight Support** - WhatToTest now uses locale-specific filenames (`WhatToTest.en-US.txt`) matching TestFlight's format
- **Smarter File Detection** - Automatically finds existing WhatToTest files regardless of locale variant
- **Consistent Updates** - Always prompts whether to append or replace existing test notes

---

## [1.0.3] - 2025-12-19

### What's New
- **Separate Release Notes** - Public release notes now generated as their own file (`changelog-public.md`) for easy sharing

### Improvements
- **Smarter TestFlight Detection** - WhatToTest prompts only appear for iOS/macOS projects
- **Flexible File Locations** - WhatToTest.txt can now be placed at project root or TestFlight folder
- **Cleaner Test Notes** - Simplified WhatToTest format with just version and date header
- **Better Config Persistence** - Preferences are now reliably saved when you choose to remember them

### Bug Fixes
- Fixed config file not being created when saving preferences

---

## [1.0.0] - 2025-12-17

### What's New
- **Changelog Generator** - Automatically document your work at the end of each coding session with comprehensive developer notes and user-friendly release summaries
- **Decisions Tracker** - Capture architectural decisions, proposals, and trade-offs even if they weren't implemented yet
- **TestFlight Integration** - Generate WhatToTest.txt files for your beta testers directly from your changelog

### Improvements
- Configurable output locations - save your preferences once and never be prompted again
- Chained workflow - generate changelog, then optionally document decisions and create test notes in one flow
- Problem/Solution format - clear documentation of what was fixed and how

### Bug Fixes
- N/A (initial release)
