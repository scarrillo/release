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

Check `.claude/config.json` for persistent settings under `release-plugin.xcbuild`:

```json
{
  "release-plugin": {
    "xcbuild": {
      "scheme": "MyApp",
      "simulatorId": "A2444326-C93A-4271-9EF0-C44AA5D4FC42",
      "showWarnings": true
    }
  }
}
```

| Setting | Description |
|---------|-------------|
| `scheme` | Default scheme to build |
| `simulatorId` | Default simulator UUID |
| `showWarnings` | Show build warnings in output (default: true) |

## Instructions

### Step 1: Check for arguments and cached settings

Parse `$ARGUMENTS`:
- If `--id=<UUID>` is provided, use that UUID for this build only (skip to Step 6)
- If `--scheme=<name>` is provided, use that scheme and update cache (skip to Step 4 for simulator selection if needed)
- If `--change` is provided, clear all cached settings and proceed to Step 2
- If no arguments:
  1. Check `.claude/config.json` for `release-plugin.xcbuild.scheme` and `release-plugin.xcbuild.simulatorId`
  2. If both are set, skip to Step 6
  3. If you have cached project/scheme/simulator from this session, skip to Step 6
  4. Otherwise proceed to Step 2

### Step 2: Detect Xcode project (first run only)

Find the project or workspace in the current directory:
```bash
ls -d *.xcworkspace 2>/dev/null || ls -d *.xcodeproj 2>/dev/null
```

- Prefer `.xcworkspace` over `.xcodeproj` (workspaces handle CocoaPods/SPM dependencies)
- If multiple found, use the first one
- **Cache the project/workspace name** for subsequent builds

### Step 3: Select scheme (first run only)

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
2. Ask: "Save this scheme as default for this project?"
   - If yes, persist to `.claude/config.json` under `release-plugin.xcbuild.scheme`

### Step 4: Query available simulators (first run or --change)

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

### Step 5: Ask user to select simulator (first run or --change)

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
2. Ask: "Save this simulator as default for this project?"
   - If yes, persist to `.claude/config.json` under `release-plugin.xcbuild.simulatorId`

### Step 6: Run the build

For a workspace:
```bash
xcodebuild -workspace <Name>.xcworkspace -scheme <Scheme> -destination "id=<UUID>" build 2>&1 | grep -E "(warning:|error:|BUILD SUCCEEDED|BUILD FAILED)" | grep -v "export " | tail -20
```

For a project (no workspace):
```bash
xcodebuild -project <Name>.xcodeproj -scheme <Scheme> -destination "id=<UUID>" build 2>&1 | grep -E "(warning:|error:|BUILD SUCCEEDED|BUILD FAILED)" | grep -v "export " | tail -20
```

**Note:** If `release-plugin.xcbuild.showWarnings` is `false` in config, exclude `warning:` from the grep pattern.

### Step 7: Report results

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

When prompting for scheme or simulator, offer to save the choice:
"Would you like to save this preference to `.claude/config.json` for future runs?"

If yes:
1. Check if `.claude/config.json` exists in the project root
2. If not, create the `.claude/` directory and `config.json` file
3. Read existing config (if any) to preserve other settings
4. Add/update the setting under the `release-plugin.xcbuild` namespace
5. Write the updated config back to the file

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
