# Release Notes

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
