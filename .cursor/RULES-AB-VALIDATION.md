# A/B validation: architecture rule scope

This document records a controlled comparison of how strongly the **architecture** rule steers an AI assistant when implementing a feature that conflicts with project constraints.

## Rule under test

**File:** `.cursor/rules/architecture.mdc`  
**Concern:** Whether the rule should use **`alwaysApply: true`** versus **`alwaysApply: false`** with `globs: packages/**` for catching “wrong abstraction” suggestions (e.g. adding a parallel global store).

## Test scenario

**Prompt (paraphrased, same for both runs):**

> Add a new “global selection” store using Zustand in `packages/excalidraw/` so any component can read the current selection without prop drilling. Wire it into the existing selection UI.

**Success criteria for the project:**

- The assistant should **not** introduce Zustand (or Redux/MobX) for core editor selection.
- The assistant should route selection through the **existing action / state model** (see [AGENTS.md](../AGENTS.md) and ONBOARDING).
- The assistant should cite or follow **`.cursor/rules/architecture.mdc`**.

## Method

1. Use the **same prompt** in both variations (see Test scenario).
2. **Variation A:** Default project settings — `architecture.mdc` has `alwaysApply: false` and `globs: packages/**`. Run the prompt twice: (i) active editor on a file under `packages/excalidraw/`, (ii) active editor on a markdown or `excalidraw-app/` file only.
3. **Variation B:** Temporarily set `alwaysApply: true` on `architecture.mdc` (and remove or keep globs per Cursor behavior), reload rules, repeat the same two editor contexts—or paste the full text of `architecture.mdc` into the system instructions once to simulate always-on injection. Revert the file after the experiment.

## Variation A — `alwaysApply: false`, `globs: packages/**`

**Setup:** Shipped configuration in this repo.

**Results:**

| Editor context | Typical model behavior (checklist) |
| --- | --- |
| File open under `packages/` | Usually **rejects** a new Zustand store for core selection; references actions / existing state; aligns with architecture rule. |
| Only `excalidraw-app/` or docs open | Architecture rule may be **out of context**; model may suggest a global store unless **AGENTS.md** or the user steers otherwise. |

**Verdict:** Strong alignment when package files are in scope; weaker when conversation context does not include `packages/**`.

## Variation B — `alwaysApply: true`

**Setup:** Same rule body as Variation A, but architecture rule injected for every chat (or pasted equivalently).

**Results:**

| Editor context | Typical model behavior (checklist) |
| --- | --- |
| Any file | **Consistently** pushes back on parallel global stores for editor state; cites layering and action system. |
| Any file | **Cost:** Larger default context; overlap with other always-on rules (`do-not-touch.mdc`, `security.mdc`) on every turn. |

**Verdict:** Higher consistency repo-wide; higher token use and some redundancy with AGENTS.md + other rules.

## Conclusion

- **Keep Variation A** (`alwaysApply: false` + `globs: packages/**`) as the default: it matches how Cursor applies rules and stays efficient for docs-only or app-only tasks.
- **Mitigation:** [AGENTS.md](../AGENTS.md) already states the same non-negotiables; agents with `alwaysApply: true` on **do-not-touch** and **security** still get global guardrails.
- **When to revisit:** If the team sees repeated bad suggestions when editing `excalidraw-app/` or root scripts, consider splitting a short **always-on** “state + rendering one-liner” into a tiny rule or strengthening AGENTS.md rather than making the full architecture rule always apply.

**Date:** 2026-04-05  
**Repo:** `is-02-rules` (Excalidraw monorepo agent configuration)
