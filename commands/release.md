---
description: Increment version (SemVer), commit, and tag for release
---

# Release Version Manager

Interactive version management for software projects. Automates [semantic versioning (SemVer)](https://semver.org), project file updates, and git tag creation.

**Model**: Uses the model configured in `changelog-plugin.model` (see /changelog for model selection).

## Instructions

### 1. Detect Project Type

Check for project files in order:

| Project Type | Detection | Version File |
|--------------|-----------|--------------|
| **Xcode (iOS/macOS)** | `*.xcodeproj` directory | `project.pbxproj` → `MARKETING_VERSION` |
| *(Future: Next.js)* | `package.json` + `next.config.*` | `package.json` → `version` |
| *(Future: Android)* | `build.gradle` or `build.gradle.kts` | `build.gradle` → `versionName` |

If no supported project type found:
"No supported project detected. Currently supports: Xcode (iOS/macOS)"

### 2. Read Current Version

**For Xcode projects:**
1. Find `.xcodeproj` directory in project root
2. Read `<project>.xcodeproj/project.pbxproj`
3. Extract first `MARKETING_VERSION = X.Y.Z;` value
4. Parse SemVer components (MAJOR.MINOR.PATCH)

Display: "Project: [ProjectName].xcodeproj"
Display: "Current version: X.Y.Z"

### 3. Prompt for Increment Type

If no argument provided, ask the user:

"Select version increment (SemVer):"

| Option | Result | Description |
|--------|--------|-------------|
| 1. patch | X.Y.(Z+1) | Bug fixes, backwards compatible |
| 2. minor | X.(Y+1).0 | New features, backwards compatible |
| 3. major | (X+1).0.0 | Breaking changes |
| 4. tag | (no change) | Create or update tag only |

### 4. Check Git Execution Mode

Check config for `changelog-plugin.release.gitMode` setting.

**If not set**, ask the user:

"How should git commands be handled?"
1. **Run automatically** - Claude executes git commands (showing progress)
2. **Show commands only** - Display commands for you to run manually

Then ask: "Save this preference for future runs?"
- If yes, persist to `.claude/config.json` under `changelog-plugin.release.gitMode`
  - Option 1 → `"auto"`
  - Option 2 → `"manual"`

### 5. Execute Version Increment

**For patch/minor/major:**

1. Calculate new version per SemVer:
   - patch: `MAJOR.MINOR.(PATCH+1)`
   - minor: `MAJOR.(MINOR+1).0`
   - major: `(MAJOR+1).0.0`

2. Show: "New version: X.Y.Z → A.B.C"

3. Confirm: "Proceed with version increment?"

4. Update version file:
   - **Xcode**: Replace ALL `MARKETING_VERSION = X.Y.Z;` with `MARKETING_VERSION = A.B.C;` in `project.pbxproj`

5. Verify update succeeded by reading back the version

6. **If gitMode is "auto"** - Execute git commands (showing each command as it runs):
   ```
   Running: git add <version-file>
   Running: git commit -m "Increment version to A.B.C"
   Running: git tag rel.vA.B.C
   ```

   Then display:
   ```
   Done! Version incremented to A.B.C
   Committed and tagged as 'rel.vA.B.C'

   To push to remote:
     git push && git push origin rel.vA.B.C
   ```

7. **If gitMode is "manual"** - Display commands for user to run:
   ```
   Version file updated to A.B.C

   Run these commands to complete the release:
     git add <version-file>
     git commit -m "Increment version to A.B.C"
     git tag rel.vA.B.C
     git push && git push origin rel.vA.B.C
   ```

**For tag (create or update tag):**

1. Determine tag name: `rel.vX.Y.Z` (current version)

2. Check if tag exists: `git tag -l rel.vX.Y.Z`

3. **If gitMode is "auto"**:
   - If tag exists:
     - Show: "Tag 'rel.vX.Y.Z' exists. Update to current commit?"
     - If confirmed, run: `git tag -f rel.vX.Y.Z`
   - If tag doesn't exist:
     - Show: "Create tag 'rel.vX.Y.Z'?"
     - If confirmed, run: `git tag rel.vX.Y.Z`
   - Display success and push command

4. **If gitMode is "manual"**:
   - If tag exists:
     - Display: `git tag -f rel.vX.Y.Z` and `git push origin -f rel.vX.Y.Z`
   - If tag doesn't exist:
     - Display: `git tag rel.vX.Y.Z` and `git push origin rel.vX.Y.Z`

## Arguments

$ARGUMENTS

**Direct increment type:**
- `/release patch` - Increment patch version (bug fixes)
- `/release minor` - Increment minor version (new features)
- `/release major` - Increment major version (breaking changes)
- `/release tag` - Create or update tag (no version change)

If no argument, show interactive menu.

## Configuration

Check for `.claude/config.json` at project root. Look for settings under `changelog-plugin.release`:

```json
{
  "changelog-plugin": {
    "release": {
      "gitMode": "auto"
    }
  }
}
```

| Option | Values | Description |
|--------|--------|-------------|
| `gitMode` | `"auto"` | Claude executes git commands automatically |
| | `"manual"` | Claude displays commands for user to run |

## The `tag` Option and CI/CD Triggers

The `tag` option is useful for triggering CI builds without incrementing the version:

**Xcode Cloud workflow:**
- Configure Xcode Cloud to build on tags matching `rel.v*`
- Use `tag` to iterate on a release candidate
- Force-push updated tag to trigger new build

**Example:**
```bash
# Initial release
/release patch              # Creates rel.v1.2.4, commits, tags

# Quick fix needed in same version
git commit -m "Fix critical bug"
/release tag                # Updates rel.v1.2.4 to current commit
git push origin -f rel.v1.2.4   # Triggers new CI build
```

## Project-Specific Notes

### Xcode (iOS/macOS)
- Only modifies `MARKETING_VERSION` in project.pbxproj
- `BUILD_NUMBER` is typically auto-incremented by Xcode Cloud
- Supports projects with multiple targets (updates all occurrences)

## Future Project Types

This command is designed to support additional project types:

- **Next.js**: Update `version` in `package.json`
- **Android**: Update `versionName` in `build.gradle`
- **Flutter**: Update `version` in `pubspec.yaml`
- **React Native**: Update both `package.json` and native projects

Contributions welcome for additional project type support.
