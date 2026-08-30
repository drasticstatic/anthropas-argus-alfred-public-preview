# Intent Workspace ↔ Local Worktree Legend
#### Compiled by Claude Code CLI (from a `resume` repo Intent-workspace session) · 2026-08-30

> **What this is:** Christopher asked for a quick-reference key mapping Intent's randomly-slugged
> workspace directories to the actual repos they contain, their branches, their spec-note
> locations, and how each relates to the separate manual `dappu/`/`code/` clone of the same repo.
> Compiled by direct filesystem/git inspection (not from memory) on the date above — re-verify
> ahead/behind counts before trusting them if you're reading this more than a few days later.

## How to read this

- **Intent workspace clone**: lives at `~/intent/workspaces/<slug>/<repo-name>/`. Its spec note
  (the Intent app's planning/coordination doc) lives at
  `~/intent/workspaces/<slug>/.workspace/notes/spec.md`.
- **Manual clone**: your day-to-day working clone under `~/dappu/` or `~/code/` — same GitHub
  repo, independent local checkout, no Intent spec framework attached. Several of these are also
  the *live/deployed* source for their repo (e.g. `dappu/resume` is what GitHub Pages serves from).
- Intent's workspace slug (e.g. `specs-sync`, `tests-config`) is assigned per-workspace and is
  unrelated to the repo name — that's the part that's genuinely hard to remember. The repo lives
  as a subdirectory inside it.
- "Sync" columns are ahead/behind counts against `origin/main`, checked 2026-08-30.

## Table

| Repo | Intent workspace path | Intent branch | Intent spec note | Manual clone | Manual branch | Manual sync |
|---|---|---|---|---|---|---|
| resume | `intent/workspaces/specs-sync/resume` | `ground-repo-context` | `intent/workspaces/specs-sync/.workspace/notes/spec.md` | `dappu/resume` (live/deployed) | `main` | 0↑/0↓ |
| gratitude-token-project | `intent/workspaces/tests-config/gratitude-token-project` | `ground-repo-context` | `intent/workspaces/tests-config/.workspace/notes/spec.md` | `dappu/gratitude-token-project` | `main` | 6↑/35↓ ⚠️ |
| gratitude-token-project_docs | `intent/workspaces/react-config/gratitude-token-project-docs` | `ground-docs-repo` | `intent/workspaces/react-config/.workspace/notes/spec.md` | `dappu/gratitude-token-project_docs` (live/deployed) | `main` | 0↑/0↓ |
| gratitude-token-project_testPublish (2026-04-06 showcase lane) | `intent/workspaces/tests-config/gratitude-token-project_testPublish_2026-04-06-showcase-lane` | n/a — static export dir, not a git repo | — | none found under `dappu/` | — | — |
| gratitude-token-project_testPublish (2026-08-28) | `intent/workspaces/tests-config/gratitude-token-project_testPublish_2026-08-28` | n/a — static export dir, not a git repo | — | `dappu/gratitude-token-project_testPublish_2026-08-28` (live/deployed, is a real git repo there) | `main` | 0↑/0↓ |
| trading-bot_arbitrage_DAPPUv3_hardhat_UNI-CAKE | `intent/workspaces/specs-sync-2/trading-bot-arbitrage-dappuv3-hardhat-uni-cake` | `main` | `intent/workspaces/specs-sync-2/.workspace/notes/spec.md` | `dappu/trading-bot_arbitrage_DAPPUv3_hardhat_UNI-CAKE` | `main` | 0↑/0↓ |
| trading-assistant | `intent/workspaces/md-sync/trading-assistant` | `main` | `intent/workspaces/md-sync/.workspace/notes/spec.md` | `code/trading-assistant` | `main` | 0↑/0↓ |
| divorce-custody-assistant | `intent/workspaces/end-update/divorce-custody-assistant` | linked git **worktree** of the code/ clone — not an independent clone (see flag below) | `intent/workspaces/end-update/.workspace/notes/spec.md` | `code/divorce-custody-assistant` | detached HEAD (`@ 9c950ef` as of this writing); other local branches: `alfred/housekeeping-v11v12`, `custody-portal`, `main` | uncommitted change present (not inspected — case-sensitive content) |

There's also an empty `intent/workspaces/background-request/` slug with no repo cloned into it —
leftover, safe to ignore or delete.

## Flags worth knowing about

- **gratitude-token-project has real, unreconciled divergence on both its worktrees** — the manual
  `dappu/` clone is 6 ahead / 35 behind `origin/main`, and the Intent-workspace clone is 13 ahead /
  36 behind. Per the repo's own role split (Auggie primary builder, Alfred/Claude Code Intent-side
  coordinator), reconciling this is that repo's call, not something touched from here — flagging
  it only so it's visible in one place.
- **divorce-custody-assistant's Intent-workspace copy is a git *worktree*, not an independent
  clone** — it shares the same `.git` as `code/divorce-custody-assistant`. There is only one
  actual working-tree checkout of case data on disk (plus whatever's staged in Intent's UI state);
  don't treat the Intent copy as a separate privacy boundary.
- The two `gratitude-token-project_testPublish_*` directories under Intent are static export
  output, not git repos — they're build artifacts, not sources of truth.

## Where the detailed handoff record lives per repo

Most of these repos keep a running coordination doc beyond just the Intent spec note:
- `resume` → `specs/KAVANAH_INTENT_SPEC.md` (session-by-session account) + `CHANGELOG.md` (dated,
  newest-first) + `AGENT-SYNC_PUBLIC/` (per-creator handoff subdirs)
- `gratitude-token-project` → `AGENT-SYNC/` (per-creator handoff subdirs: `created-by-alfred/`,
  `created-by-kavanah/`, `created-by-christopher/`, `created-by-augment-vscode-migration/`)
- `trading-assistant` → the hub for cross-repo `AGENT-SYNC/` handoffs referenced by every other repo

---
*If this doc drifts out of date, regenerate the table with a fresh pass of `git branch
--show-current` / `git remote get-url origin` / `git rev-list --left-right --count
origin/main...HEAD` across each path above rather than trusting the numbers here.*
