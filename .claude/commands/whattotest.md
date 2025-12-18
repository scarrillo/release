# TestFlight WhatToTest Generator

Generate a WhatToTest.txt document for TestFlight beta releases based on recent changelog documents.

## Instructions

1. **Locate recent changelogs** by:
   - Reading existing changelog files in the project
   - Checking git history for recent changes if no changelog exists
   - Looking for release notes or version documentation

2. **Create TestFlight folder structure** if it doesn't exist:
   ```
   ProjectRoot/
   └── TestFlight/
       └── WhatToTest.txt
   ```

3. **Generate WhatToTest.txt content** that is:
   - Concise and actionable for testers
   - Focused on what testers should specifically test
   - Written in plain language (non-technical)
   - Organized by feature area or priority

## Output Format

Create `TestFlight/WhatToTest.txt` with this structure:

```
What to Test
============

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
```

## Notes

- The WhatToTest.txt file is automatically included with your TestFlight build when placed in a `TestFlight` folder at your project root
- Keep content under 4000 characters (TestFlight limit)
- Focus on NEW or CHANGED functionality that needs testing
- You can also create localized versions like `WhatToTest.en-US.txt` for specific locales

## Arguments

If a version number is provided, generate WhatToTest content for that specific version: $ARGUMENTS
