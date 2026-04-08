---
description: "Analyze and fix a build or runtime error"
---

Analyze and fix the following error: $ARGUMENTS

1. Read the full error message and stack trace
2. Locate the source file and line causing the error
3. Understand the root cause (type error, missing import, logic bug)
4. Check .cursor/rules/ for constraints before suggesting a fix
5. Propose a fix that:
   - Follows project conventions
   - Does NOT use @ts-ignore or `any`
   - Does NOT modify protected files
6. Apply the fix and verify compilation