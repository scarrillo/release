# Release Toolkit Plugin For Claude Code

[![Version](https://img.shields.io/badge/version-1.2.0-blue.svg)](https://github.com/scarrillo/release/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A Claude Code plugin for release automation. Semantic versioning, changelogs, architectural decisions, and TestFlight integration.

## Why Use This?

Release management is tedious. This plugin automates the repetitive parts:

- **Version Management** - Increment versions per SemVer, commit, and tag in one command
- **Changelogs** - Problem/Solution format from your session, not vague commit messages
- **Decisions** - Capture the "why" behind choices, even ones you didn't implement
- **TestFlight notes** - Beta tester instructions derived directly from your changelog

## About

> **Renamed from "Changelog Plugin"** - Now a comprehensive release toolkit with semantic versioning, changelogs, and more. Commands are now namespaced under `release:` (e.g., `/release:changelog`).

## Requirements

- [Claude Code](https://claude.ai/code) CLI

## Installation

```bash
# Add the marketplace
/plugin marketplace add scarrillo/release

# Install the plugin
/plugin install release@scarrillo
```

## Commands

| Command | Description | Output |
|---------|-------------|--------|
| `/release:release` | Increment version (SemVer), commit, tag | Git commit + `rel.vX.Y.Z` tag |
| `/release:changelog` | Generate changelog from session work | `changelog.md` + `changelog-public.md` |
| `/release:decisions` | Capture decisions and proposals | `decisions.md` |
| `/release:whattotest` | Generate TestFlight testing guide | `TestFlight/WhatToTest.en-US.txt` |

> **Note**: Commands are namespaced with `release:` prefix when installed via marketplace.

## Workflow

```
/release:changelog       # Document completed work
         ↓
/release:decisions       # Capture decisions (prompted)
         ↓
/release:whattotest      # Generate test notes (prompted)
         ↓
/release:release         # Increment version, commit, tag
```

Or run any command standalone.

## Features

### `/release:release`
- Increment version per [SemVer](https://semver.org) (major, minor, patch)
- Auto-detects project type: Claude Plugin, Xcode (more platforms coming)
- Prompts to generate changelog before committing
- Updates version file(s), commits, and creates git tag
- `tag` option for CI/CD triggers without version change

### `/release:changelog`
- Uses current session context as primary source
- Cross-references with git to deduplicate
- Problem/Solution format with code snippets
- Generates two files:
  - `changelog.md` - Technical developer notes
  - `changelog-public.md` - User-friendly release notes

### `/release:decisions`
- Documents decisions regardless of implementation status
- Tracks: Implemented, Proposed, Deferred, Rejected
- Records rationale and trade-offs

### `/release:whattotest`
- Derives content from `changelog-public.md`
- Creates `TestFlight/` folder structure
- Tester-focused, actionable format
- Stays within 4000 character limit

## Supported Project Types

| Project Type | Detection | Version Location |
|--------------|-----------|------------------|
| **Claude Plugin** | `.claude-plugin/plugin.json` | `plugin.json` + `marketplace.json` |
| **Xcode (iOS/macOS)** | `*.xcodeproj` | `project.pbxproj` → `MARKETING_VERSION` |

More platforms coming: Next.js, Android, Flutter, React Native.

> **Fun fact**: This plugin can release itself. We used `/release:release` to publish v1.2.0.

## Sample Output

This plugin documents itself. View real output in this repo:

| File | Description |
|------|-------------|
| [`docs/changelog.md`](docs/changelog.md) | Developer changelog with Problem/Solution format |
| [`docs/changelog-public.md`](docs/changelog-public.md) | User-facing release notes |
| [`docs/decisions.md`](docs/decisions.md) | Architectural decisions and proposals |
| [`TestFlight/WhatToTest.txt`](TestFlight/WhatToTest.txt) | Beta tester instructions |

## Configuration

### Model Selection

On first run, you'll be prompted to choose which model to use:

| Model | ID | Use Case |
|-------|-----|----------|
| Current | `inherit` | Use conversation's model (default) |
| Opus 4.5 | `claude-opus-4-5-20251101` | Most capable |
| Sonnet 4 | `claude-sonnet-4-20250514` | Balanced (recommended) |
| Haiku 3.5 | `claude-3-5-haiku-20241022` | Fastest |

This allows Opus users to hand off tasks to a faster model. Your choice can be saved to config.

### Command Flags

```bash
/release:changelog --auto          # Run all follow-ups automatically
/release:changelog --skip          # No follow-ups
/release:changelog --decisions     # Auto-run decisions only
/release:changelog --no-whattotest # Skip whattotest prompt
```

### Persistent Config

Create `.claude/config.json` in your project (a sample is included). Settings are namespaced under `release-plugin`:

```json
{
  "release-plugin": {
    "model": "claude-sonnet-4-20250514",
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
    },
    "release": {
      "gitMode": "auto"
    }
  }
}
```

| Option | Values |
|--------|--------|
| `model` | `"inherit"`, `"claude-opus-4-5-20251101"`, `"claude-sonnet-4-20250514"`, `"claude-3-5-haiku-20241022"` |
| `outputPath` | `"./file.md"`, `"./docs/file.md"`, custom |
| `followUp.*` | `"always"`, `"ask"`, `"never"` |
| `onExisting` | `"prepend"`, `"replace"`, `"ask"` |
| `gitMode` | `"auto"` (Claude runs git), `"manual"` (show commands only) |

When prompted for choices, you can save them to config for future runs.

Command-line flags override config settings.

## License

MIT
