---
description: "Refactor selected code following project patterns"
---

Refactor the selected code or the file $ARGUMENTS:

1. Read the current implementation
2. Identify code smells: duplication, deep nesting, large functions
3. Check .cursor/rules/ for project conventions
4. Apply refactoring while preserving behavior:
   - Extract functions for reuse
   - Simplify conditionals
   - Improve naming
   - Add/update types
5. Verify the refactored code compiles
6. List all changes made and why