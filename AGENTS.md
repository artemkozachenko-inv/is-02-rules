# Agent guide

When you are creating any code you must output to chat  
message **"USING AGENTS.md in current folder"**  
so maintainers know you are following this file and linked rules.

This file orients automated coding agents to this repository. **Authoritative detail** lives in the linked docs; prefer them over guessing.

## Cursor rules index

Rules live in [`.cursor/rules/`](./.cursor/rules/). Each rule file includes a **How to verify** section describing checks for that topic.

| Rule file | Applies | Summary |
| --- | --- | --- |
| [`architecture.mdc`](./.cursor/rules/architecture.mdc) | `packages/**` | Action-based state, canvas rendering, package layering, no ad hoc global stores for editor logic |
| [`conventions.mdc`](./.cursor/rules/conventions.mdc) | `packages/**/*.ts(x)` | Functional components + hooks for new package UI, TypeScript strictness, naming and exports |
| [`do-not-touch.mdc`](./.cursor/rules/do-not-touch.mdc) | Always | Protected core files (`Renderer.ts`, `restore.ts`, `manager.tsx`, `types.ts`) |
| [`testing.mdc`](./.cursor/rules/testing.mdc) | Tests and Vitest config globs | Vitest-only, colocation, `packages/excalidraw` test utils, no flaky or architecture-bypassing tests |
| [`security.mdc`](./.cursor/rules/security.mdc) | Always | Secrets/env, user-controlled data, dependencies, collab/network caution |
| [`excalidraw-app.mdc`](./.cursor/rules/excalidraw-app.mdc) | `excalidraw-app/**` | App vs library boundaries, collab/env patterns for excalidraw.com |

**Rule experiments:** See [.cursor/RULES-AB-VALIDATION.md](./.cursor/RULES-AB-VALIDATION.md) for documented A/B validation (architecture rule scope).

**Cross-tool pointer:** [.cursor/CLAUDE.md](./.cursor/CLAUDE.md) summarizes the same for other assistants.

## Custom Cursor commands

Repeatable prompts are in [`.cursor/commands/`](./.cursor/commands/) (Markdown). Examples:

| Command | Purpose |
| --- | --- |
| [`analyze-error.md`](./.cursor/commands/analyze-error.md) | Trace and fix build/runtime errors within project constraints |
| [`generate-component.md`](./.cursor/commands/generate-component.md) | Scaffold a component + colocated test |
| [`refactor.md`](./.cursor/commands/refactor.md) | Refactor selection with conventions preserved |
| [`pre-merge-verify.md`](./.cursor/commands/pre-merge-verify.md) | Run CI-shaped checks before PR/merge |
| [`add-locale-string.md`](./.cursor/commands/add-locale-string.md) | Add/update `en.json` keys and `t("…")` usage for UI copy |

## Canonical references

| Document | Use for |
| --- | --- |
| [ONBOARDING.md](./ONBOARDING.md) | Monorepo layout, packages, architecture, state, rendering, collab, persistence, workflows, glossary |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | Contribution entry point (links to official docs) |
| [README.md](./README.md) | Product overview and quick links |
| [.github/copilot-instructions.md](./.github/copilot-instructions.md) | GitHub Copilot instructions for TypeScript/React performance and style expectations |
| [.cursor/rules/](./.cursor/rules/) | Project-specific constraints (see rules index above) |

## What this repo is

Yarn workspaces monorepo for **Excalidraw**: the publishable React library `@excalidraw/excalidraw` (`packages/excalidraw/`), internal packages (`packages/common`, `math`, `element`, `utils`), the **excalidraw.com** app (`excalidraw-app/`, not on npm), and examples. See ONBOARDING §2–3 for the tree and dependency graph.

**Dependency rule:** lower-level packages never import higher-level ones (`common` → `math` → `element` → `excalidraw` → app).

## Commands (repo root)

From `package.json` and ONBOARDING §4, §12:

- `yarn install` — install all workspaces
- `yarn start` — dev server for the app (HMR; default `http://localhost:3000`)
- `yarn test` — Vitest (watch)
- `yarn test:app -- <path>` — single file or pattern
- `yarn test:update` — Vitest with snapshot updates, non-watch
- `yarn test:all` — typecheck, ESLint, Prettier, Vitest non-watch (CI-shaped)
- `yarn test:typecheck` / `yarn test:code` / `yarn test:other` — tsc, ESLint, Prettier check
- `yarn fix` — Prettier write + ESLint fix
- `yarn build:packages` / `yarn build:app` — package and app builds

Env: copy `.env.development` → `.env.development.local`; variables are documented in ONBOARDING §4 and `excalidraw-app/vite.config.mts`.

## Architecture (do not fight it)

Aligned with `.cursor/rules/architecture.mdc` and ONBOARDING §7–9:

- **Editor state and actions:** Changes go through the action system (`ActionManager`, actions under `packages/excalidraw/actions/`). Do not introduce parallel global state stores (e.g. Redux) for editor logic.
- **Drawing:** Canvas 2D (rough.js, layered static/interactive canvases). Do not use React DOM, react-konva, Fabric, or Pixi for the canvas scene.
- **State shape:** `AppState` and related types live in `packages/excalidraw/types.ts` (treat as sensitive; see Protected files).
- **Collaboration:** Implemented in `excalidraw-app/collab/`, not inside the published library (ONBOARDING §10).

ONBOARDING documents **dual Jotai stores** (`editorJotaiStore` vs `appJotaiStore`) and **class-based `App.tsx`** for hot-path rendering. For **new** React UI in packages, follow `.cursor/rules/conventions.mdc` (functional components + hooks). Do not refactor legacy class roots unless the task explicitly requires it.

## Protected files

**Do not modify** without explicit human approval and the bar in `.cursor/rules/do-not-touch.mdc`:

- `packages/excalidraw/scene/Renderer.ts`
- `packages/excalidraw/data/restore.ts`
- `packages/excalidraw/actions/manager.tsx`
- `packages/excalidraw/types.ts`

Requirements if approved: full dependency understanding, full test suite, manual QA.

## Code conventions (packages)

From `.cursor/rules/conventions.mdc` and copilot-instructions:

- **Components:** Prefer functional components and hooks for new code; props type `{ComponentName}Props`; **named exports only** (no default exports); colocate tests as `ComponentName.test.tsx`.
- **TypeScript:** Strict; avoid `any` and `@ts-ignore`; prefer `type` for simple types; use `import type { … }`.
- **Files:** kebab-case for non-component files; PascalCase for component filenames.
- **Performance / style:** Prefer immutability, optional chaining, nullish coalescing; avoid unnecessary allocation where practical; use CSS modules for component styling per copilot-instructions.

## Testing

Follow `.cursor/rules/testing.mdc`:

- **Vitest** only (`describe` / `it` / `expect`, `vi` for mocks); no new test runners or test dependencies without approval.
- Match each package’s layout: colocated tests, or existing `tests/` / `__tests__/`.
- For `packages/excalidraw` UI tests, use `packages/excalidraw/tests/test-utils` and existing helpers (e.g. `reseed` from `@excalidraw/common` where suites require determinism).
- Do not bypass architecture in tests (e.g. fake state paths that contradict the real action/store model).
- Verify with `yarn test` or targeted `yarn test:app -- <file>`; use `yarn test:all` before broad changes.

Coverage thresholds are listed in ONBOARDING §12.

## Dependencies

No new npm packages without explicit approval. Check `packages/utils/` before adding external helpers (`.cursor/rules/architecture.mdc`).

## Security

Follow [`.cursor/rules/security.mdc`](./.cursor/rules/security.mdc): no committed secrets; use documented env files; sanitize user-controlled HTML; do not add dependencies without approval; treat collab and persistence boundaries carefully. Use the rule’s **How to verify** steps when touching env, markup, or network code.

## Verifying agent output

- After substantive edits, run the checks listed in the relevant rule **How to verify** sections (minimum: `yarn test:typecheck` and `yarn test:code`; broader: `yarn test:all`).
- Confirm protected files are untouched unless the task explicitly approved changing them.
- For app or collab work, smoke-test with `yarn start` when behavior is user-visible.

## Common task entry points

ONBOARDING §14–15 maps tasks to directories (toolbar, properties panel, element types, rendering, collab, shortcuts, i18n, history, export, etc.). Use that table when routing changes.

## i18n

New user-visible strings: add keys to `packages/excalidraw/locales/en.json` (source of truth), use `t("…")` in UI (ONBOARDING §14).

## Further reading

- [Official Excalidraw docs](https://docs.excalidraw.com)
- `dev-docs/` — developer documentation sources
- `packages/excalidraw/CHANGELOG.md` — library release notes
