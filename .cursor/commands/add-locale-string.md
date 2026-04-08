---
description: "Add or update a user-visible string (i18n)"
---

Add or update translations for: $ARGUMENTS

1. Read [AGENTS.md](../../AGENTS.md) (i18n section) and [.cursor/rules/conventions.mdc](../rules/conventions.mdc).
2. **Source of truth:** Add or edit the key in `packages/excalidraw/locales/en.json` (match existing key naming and nesting).
3. **Usage:** Use `t("your.key.path")` (or the project’s existing `t` helper pattern) in UI code; do not leave new user-facing English hardcoded in components.
4. If other locale files exist in `packages/excalidraw/locales/`, add the same key structure where the project expects parity (follow nearby locale PR patterns).
5. Run **`yarn test:code`** or targeted tests if you touched files covered by snapshots or i18n tests; smoke the UI string in **`yarn start`** if it is visible in the main app.
