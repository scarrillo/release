# TestFlight WhatToTest Generator

Generate a WhatToTest.txt document for TestFlight beta releases.

## Instructions

1. **Locate source content** (in priority order):
   - **If called from /changelog**: Use the `changelog-public.md` that was just generated in this session. Do not re-analyze - derive directly from that file.
   - **If called directly**: Look for source content in this order:
     1. `changelog-public.md` in the project
     2. Recent changelog files in the project
     3. Git commit history for recent changes
     4. Release notes or version documentation

2. **Output location**:

   **If WhatToTest.txt doesn't exist**, ask the user:
   "Where should I create WhatToTest.txt?"
   1. TestFlight folder (`./TestFlight/WhatToTest.txt`) - Recommended
   2. Project root (`./WhatToTest.txt`)
   3. Custom location (let user specify)

   Default to TestFlight folder if no preference is set. Create the folder if needed.

   **If WhatToTest.txt already exists**, use its current location and ask:
   "WhatToTest.txt already exists. How should I handle it?"
   1. Prepend new entry (keep history, newest at top)
   2. Replace all (start fresh with only this release)

3. **Generate WhatToTest.txt content** that is:
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

- The WhatToTest.txt file is automatically included with your TestFlight build when placed in a `TestFlight` folder at your project root
- Keep content under 4000 characters (TestFlight limit)
- Focus on NEW or CHANGED functionality that needs testing
- You can also create localized versions like `WhatToTest.en-US.txt` for specific locales

## Arguments

If a version number is provided, generate WhatToTest content for that specific version: $ARGUMENTS
