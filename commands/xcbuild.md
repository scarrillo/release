---
description: Build iOS/macOS app using xcodebuild with automatic project and simulator detection
---

# Xcode Build

Build an iOS/macOS app using xcodebuild with automatic project detection and simulator selection.

## Arguments

$ARGUMENTS

- No arguments: Use cached settings or prompt for selection on first run
- `--change`: Clear cached settings and prompt for new scheme and simulator selection
- `--scheme=<name>`: Use a specific scheme (updates cache)
- `--id=<UUID>`: Use a specific simulator UUID (one-time, doesn't update cache)

## Configuration

Plugin settings are stored in dedicated config files (Claude Code's `settings.json` has a strict schema):

| Scope | File | Use Case |
|-------|------|----------|
| `project` | `.claude/config.json` | Team defaults, version controlled |
| `local` | `.claude/config.local.json` | Personal settings, gitignored |

### Project Settings (`.claude/config.json`)
Team defaults, committed to version control:
```json
{
  "release-plugin": {
    "xcbuild": {
      "scheme": "MyApp",
      "showWarnings": true
    }
  }
}
```

### Local Settings (`.claude/config.local.json`)
Personal settings, gitignored (simulator UUIDs are machine-specific):
```json
{
  "release-plugin": {
    "xcbuild": {
      "simulatorId": "A2444326-C93A-4271-9EF0-C44AA5D4FC42",
      "scheme": "MyApp-Dev"
    }
  }
}
```

### Settings Reference

| Setting | Recommended Scope | Description |
|---------|-------------------|-------------|
| `scheme` | project | Default scheme (team default) |
| `simulatorId` | local | Default simulator UUID (machine-specific) |
| `showWarnings` | project | Show build warnings in output (default: true) |

### Precedence (highest to lowest)
1. Command-line arguments (`--scheme=`, `--id=`)
2. Local config (`.claude/config.local.json`)
3. Project config (`.claude/config.json`)
4. Session cache
5. Prompt user

## Instructions

### Step 1: Check for arguments and cached settings

Parse `$ARGUMENTS`:
- If `--id=<UUID>` is provided, use that UUID for this build only (skip to Step 5)
- If `--scheme=<name>` is provided, use that scheme and update cache (proceed to Step 3)
- If `--change` is provided, clear all cached settings and proceed to Step 1b
- If no arguments, check settings in precedence order:
  1. `.claude/config.local.json` → `release-plugin.xcbuild.scheme` and `simulatorId`
  2. `.claude/config.json` → `release-plugin.xcbuild.scheme` and `simulatorId`
  3. Session cache from earlier in this conversation
  4. If scheme is missing, proceed to Step 1b (detect project, then select scheme)
  5. If simulator is missing, proceed to Step 3 (query and select simulator)
  6. If both are found, proceed to Step 3 (query simulators to validate ID)

### Step 1b: Detect Xcode project (if needed)

Find the project or workspace in the current directory:
```bash
ls -d *.xcworkspace 2>/dev/null || ls -d *.xcodeproj 2>/dev/null
```

- Prefer `.xcworkspace` over `.xcodeproj` (workspaces handle CocoaPods/SPM dependencies)
- If multiple found, use the first one
- **Cache the project/workspace name** for subsequent builds

### Step 2: Select scheme (if needed)

Get available schemes:
```bash
xcodebuild -list -json
```

Parse the JSON `project.schemes` array. Identify a **recommended scheme**:
- Prefer scheme matching the project/workspace name exactly
- Exclude schemes containing "Tests", "UITests", "Keyboard", "Widget", "Extension"
- If only one non-test/extension scheme exists, use it directly without prompting

If multiple schemes are available, use AskUserQuestion:

**Header:** "Scheme"

**Question:** "Which scheme should be used for builds?"

**Options** (show up to 4, add "(Recommended)" suffix to first):
1. Main app scheme - description: "Main app target"
2. Other schemes with descriptive labels based on name (e.g., "ASC" → "App Store Connect", "Keyboard" → "Keyboard extension only")

Example for a project with schemes `["MyApp", "MyApp ASC Sub", "MyAppKeyboard"]`:
- Option 1: "MyApp (Recommended)" - "Main app target"
- Option 2: "MyApp ASC Sub" - "App Store Connect submission"
- Option 3: "MyAppKeyboard" - "Keyboard extension only"

After user selects:
1. **Cache the scheme name** for subsequent builds in this session
2. Ask: "Save this scheme as the project default?" (saves to `.claude/config.json`)

### Step 3: Query available simulators

First, output a message to the user: "Querying available simulators..."

Then run this command to get available simulators:
```bash
xcrun simctl list devices available --json
```

Parse the JSON to build a list of available iPhone simulators:
- Group by iOS version (runtime)
- Include device name and UUID
- Sort by iOS version (newest first), then by device name

Identify the **recommended simulator**: The newest iPhone Pro model on the highest iOS version.

### Step 4: Validate or select simulator

**If a simulator ID was loaded from config or cache:**
1. Check if the UUID exists in the available simulators list from Step 3
2. If found, proceed to Step 5 (run build)
3. If NOT found (stale/invalid UUID):
   - Inform the user: "Configured simulator not found (may have been deleted or is from a different machine)."
   - Proceed to simulator selection below

**If no simulator ID or invalid ID:**

Use AskUserQuestion to present options:

**Header:** "Simulator"

**Question:** "Which iOS Simulator should be used for builds?"

**Options** (show up to 4):
1. **Recommended** - e.g., "iPhone 17 Pro Max (iOS 26.2)" with description "Recommended - Latest Pro model"
2. Next best option - e.g., "iPhone 17 Pro (iOS 26.2)"
3. Another popular option - e.g., "iPhone 16 Pro (iOS 18.2)"
4. If user needs something else, they can select "Other" and provide a UUID

After user selects:
1. **Cache the simulator name and UUID** for subsequent builds in this session
2. Ask: "Save this simulator for this project?" (saves to `.claude/config.local.json`)

### Step 5: Run the build

For a workspace:
```bash
xcodebuild -workspace <Name>.xcworkspace -scheme <Scheme> -destination "id=<UUID>" build 2>&1 | grep -E "(warning:|error:|BUILD SUCCEEDED|BUILD FAILED)" | grep -v "export " | tail -20
```

For a project (no workspace):
```bash
xcodebuild -project <Name>.xcodeproj -scheme <Scheme> -destination "id=<UUID>" build 2>&1 | grep -E "(warning:|error:|BUILD SUCCEEDED|BUILD FAILED)" | grep -v "export " | tail -20
```

**Note:** If `release-plugin.xcbuild.showWarnings` is `false` in config, exclude `warning:` from the grep pattern.

### Step 6: Report results

Format output as:
```
Project: <Project/Workspace Name>
Scheme: <Scheme Name>
Simulator: <Device Name> (<iOS Version>)
ID: <UUID>

[Any SwiftLint warnings or build errors]

BUILD SUCCEEDED [X.X sec]
```

Or on failure:
```
Project: <Project/Workspace Name>
Scheme: <Scheme Name>
Simulator: <Device Name> (<iOS Version>)
ID: <UUID>

[Build errors]

BUILD FAILED - [summary of errors]
```

## Persisting User Choices

After selecting scheme or simulator, offer to save:

**For scheme** (team default):
"Save this scheme as the project default?"
- If yes, save to `.claude/config.json` under `release-plugin.xcbuild.scheme`
- This is version controlled and shared with the team

**For simulator** (personal/machine-specific):
"Save this simulator for this project?"
- If yes, save to `.claude/config.local.json` under `release-plugin.xcbuild.simulatorId`
- This is gitignored (machine-specific UUIDs)

### Saving to a config file

1. Create `.claude/` directory if needed
2. Read existing file content (if any) to preserve other settings
3. Parse as JSON, or start with empty object `{}`
4. Ensure `release-plugin.xcbuild` path exists
5. Add/update the specific setting
6. Write the updated JSON back to the file
6. Create parent directories if needed (e.g., `mkdir -p ~/.claude`)

## Examples

**First run (no cached settings):**
```
/release:xcbuild
```
→ Detects project, asks user to select scheme and simulator, caches choices, runs build

**Subsequent runs:**
```
/release:xcbuild
```
→ Uses cached project/scheme/simulator, runs build immediately

**Change scheme and simulator:**
```
/release:xcbuild --change
```
→ Clears cache, asks user to select new scheme and simulator, updates cache, runs build

**Use specific scheme:**
```
/release:xcbuild --scheme=FontPopKeyboard
```
→ Uses specified scheme, updates cache, uses cached simulator

**Use specific simulator UUID:**
```
/release:xcbuild --id=A2444326-C93A-4271-9EF0-C44AA5D4FC42
```
→ Uses provided UUID for this build only (doesn't update cache)
