# Claude Code Commands

Custom slash commands for project documentation and release management.

## Available Commands

### `/changelog`
Generate a changelog from completed work in the current session.

**Features:**
- Uses current session context as primary source
- Cross-references with git to deduplicate
- Problem/Solution format with code snippets
- Generates both developer and user-facing summaries
- Optionally chains to `/decisions` and `/whattotest`

**Output:** `changelog.md` at project root

---

### `/decisions`
Capture architectural decisions and proposals for future reference.

**Features:**
- Documents decisions regardless of implementation status
- Tracks: Implemented, Proposed, Deferred, Rejected
- Records rationale and trade-offs
- Can run standalone or from `/changelog`

**Output:** `decisions.md` at project root

---

### `/whattotest`
Generate TestFlight WhatToTest.txt for beta releases.

**Features:**
- Derives content from changelog's public summary
- Creates `TestFlight/` folder structure
- Tester-focused, actionable format
- Stays within 4000 character limit

**Output:** `TestFlight/WhatToTest.txt`

---

## Typical Workflow

```
/changelog          # Document completed work
    ↓
/decisions          # Capture decisions (prompted automatically)
    ↓
/whattotest         # Generate test notes (prompted automatically)
```

Or run any command standalone as needed.

---

## Configuration

### Command-line Flags

Control follow-up behavior per invocation:

```bash
/changelog --auto          # Run all follow-ups automatically
/changelog --skip          # No follow-ups
/changelog --decisions     # Auto-run decisions only
/changelog --no-whattotest # Skip whattotest prompt
```

### Persistent Config

Create `.claude/config.json` for project defaults:

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

Command-line flags override config file settings.
