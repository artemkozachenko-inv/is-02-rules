---
description: "Run local checks before opening a PR or merging"
---

Before merging or opening a PR for: $ARGUMENTS

1. Read [AGENTS.md](../../AGENTS.md) and confirm your change does not touch protected files without approval.
2. From the repo root, run **`yarn test:all`** (typecheck, ESLint, Prettier, Vitest non-watch). If that is too slow for a tiny change, run at minimum **`yarn test:typecheck`** and **`yarn test:code`**, plus targeted **`yarn test:app -- <path>`** for files you edited.
3. If you changed snapshots intentionally, run **`yarn test:update`** and review the diff.
4. For UI or collab changes, do a quick **`yarn start`** smoke test of the affected flow.
5. Summarize what you ran and the results; list any skipped checks and why.
