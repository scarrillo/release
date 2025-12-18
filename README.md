# Changelog Plugin

A Claude Code plugin for release documentation automation. Generates changelogs, captures architectural decisions, and creates TestFlight testing guides.

## Installation

### From GitHub

```bash
# Add the marketplace
/plugin marketplace add scarrillo/changelog

# Install the plugin
/plugin install changelog@scarrillo
```

### Local Development

```bash
# Add local marketplace
/plugin marketplace add /path/to/changelog

# Install
/plugin install changelog@changelog
```

## Commands

| Command | Description | Output |
|---------|-------------|--------|
| `/changelog` | Generate changelog from session work | `changelog.md` |
| `/decisions` | Capture decisions and proposals | `decisions.md` |
| `/whattotest` | Generate TestFlight testing guide | `TestFlight/WhatToTest.txt` |

## Workflow

```
/changelog          # Document completed work
    ↓
/decisions          # Capture decisions (prompted)
    ↓
/whattotest         # Generate test notes (prompted)
```

Or run any command standalone.

## Features

### `/changelog`
- Uses current session context as primary source
- Cross-references with git to deduplicate
- Problem/Solution format with code snippets
- Generates developer + user-facing summaries

### `/decisions`
- Documents decisions regardless of implementation status
- Tracks: Implemented, Proposed, Deferred, Rejected
- Records rationale and trade-offs

### `/whattotest`
- Derives content from changelog's public summary
- Creates `TestFlight/` folder structure
- Tester-focused, actionable format
- Stays within 4000 character limit

## Configuration

### Command Flags

```bash
/changelog --auto          # Run all follow-ups automatically
/changelog --skip          # No follow-ups
/changelog --decisions     # Auto-run decisions only
/changelog --no-whattotest # Skip whattotest prompt
```

### Persistent Config

Create `.claude/config.json` in your project:

```json
{
  "changelog": {
    "followUp": {
      "decisions": "ask",
      "whattotest": "always"
    }
  }
}
```

**Options**: `"always"` | `"ask"` | `"never"`

## License

MIT
