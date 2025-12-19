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
| `/changelog` | Generate changelog from session work | `changelog.md` + `changelog-public.md` |
| `/decisions` | Capture decisions and proposals | `decisions.md` |
| `/whattotest` | Generate TestFlight testing guide | `TestFlight/WhatToTest.en-US.txt` |

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
- Generates two files:
  - `changelog.md` - Technical developer notes
  - `changelog-public.md` - User-friendly release notes

### `/decisions`
- Documents decisions regardless of implementation status
- Tracks: Implemented, Proposed, Deferred, Rejected
- Records rationale and trade-offs

### `/whattotest`
- Derives content from `changelog-public.md`
- Creates `TestFlight/` folder structure
- Tester-focused, actionable format
- Stays within 4000 character limit

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

This allows Opus users to hand off changelog generation to a faster model. Your choice can be saved to config.

### Command Flags

```bash
/changelog --auto          # Run all follow-ups automatically
/changelog --skip          # No follow-ups
/changelog --decisions     # Auto-run decisions only
/changelog --no-whattotest # Skip whattotest prompt
```

### Persistent Config

Create `.claude/config.json` in your project (a sample is included). Settings are namespaced under `changelog-plugin`:

```json
{
  "changelog-plugin": {
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

When prompted for choices, you can save them to config for future runs.

Command-line flags override config settings.

## License

MIT
