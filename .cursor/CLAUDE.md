# Claude / cross-tool agent note

This repository’s primary agent orientation document is **[AGENTS.md](../AGENTS.md)** at the repository root. Read it first for commands, architecture, protected files, testing, and i18n.

## Cursor rules (`.cursor/rules/`)

| Rule | Role |
| --- | --- |
| `architecture.mdc` | State, rendering, dependency layering (`packages/**`) |
| `conventions.mdc` | TypeScript and React conventions (`packages/**/*.ts(x)`) |
| `do-not-touch.mdc` | Protected files (always apply) |
| `testing.mdc` | Vitest and test layout (test files and config globs) |
| `security.mdc` | Secrets, user content, dependencies (always apply) |
| `excalidraw-app.mdc` | App-only collab/env boundaries (`excalidraw-app/**`) |

## Custom slash commands

Markdown prompts live in [commands](./commands/). Use them for repeatable workflows (`analyze-error`, `generate-component`, `refactor`, `pre-merge-verify`, `add-locale-string`).

## Extra documentation

- [RULES-AB-VALIDATION.md](./RULES-AB-VALIDATION.md) — A/B validation notes for rule scope.
- [ONBOARDING.md](../ONBOARDING.md) — Deep monorepo and product context.

When implementing code in this repo, follow **AGENTS.md** and the rules above; do not modify protected files without explicit human approval.
