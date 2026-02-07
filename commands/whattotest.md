---
description: Generate TestFlight WhatToTest document from changelog
---

# TestFlight WhatToTest Generator

Generate a WhatToTest document for TestFlight beta releases.

**Model**: Uses the model configured in `release-plugin.model` (see /release:changelog for model selection).

## Instructions

1. **Locate source content** (in priority order):
   - **If called from /release:changelog**: Use the `changelog-public.md` that was just generated in this session. Do not re-analyze - derive directly from that file.
   - **If called directly**: Look for source content in this order:
     1. `changelog-public.md` in the project
     2. Recent changelog files in the project
     3. Git commit history for recent changes
     4. Release notes or version documentation

2. **Check for existing WhatToTest files**:

   Search for existing WhatToTest files in `./TestFlight/`, checking in this order:
   1. `WhatToTest.en-US.txt` (default)
   2. Other locale variants (e.g., `.de.txt`, `.fr.txt`)

   **If existing file(s) found**, use the same location and filename.

   Check config for `release-plugin.whattotest.onExisting`:
   - If `"prepend"`: Prepend new entry without asking
   - If `"replace"`: Replace content without asking
   - If `"ask"` (default): Prompt the user:
     "WhatToTest file already exists. How should I handle it?"
     1. Prepend new entry (keep history, newest at top) - Recommended
     2. Replace all (start fresh with only this release)

   **If no existing file found**, create `./TestFlight/WhatToTest.en-US.txt`.
   Create the `TestFlight/` folder if it doesn't exist.

3. **Generate WhatToTest content** that is:
   - Concise and actionable for testers
   - Focused on what testers should specifically test
   - Written in plain language (non-technical)
   - Organized by feature area or priority

## Output Format

Structure for each release entry:

```
[App Name] v[Version] - YYYY-MM-DD

[Brief overview of this beta release - 1-2 sentences]

Key Areas to Test:

1. [Feature/Area Name]
   - [Specific test case or scenario]
   - [Another test case]

2. [Feature/Area Name]
   - [Specific test case or scenario]
   - [Another test case]

Known Issues:
- [Any known issues testers should be aware of]

Feedback Focus:
- [Specific questions or areas where you want tester feedback]

---
```

When prepending, each release gets its own section separated by `---`.

## Notes

- The WhatToTest file is automatically included with your TestFlight build when placed in a `TestFlight` folder at your project root
- Keep content under 4000 characters (TestFlight limit)
- Focus on NEW or CHANGED functionality that needs testing
- Localized filenames: `WhatToTest.en-US.txt` (US English), `WhatToTest.de.txt` (German), etc.

## Arguments

If a version number is provided, generate WhatToTest content for that specific version: $ARGUMENTS
