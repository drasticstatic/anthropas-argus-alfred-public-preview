# Intent Workspace ↔ Local Worktree Legend
#### Compiled by Claude Code CLI (from a `resume` repo Intent-workspace session) · 2026-08-30

> **What this is:** Christopher asked for a quick-reference key mapping Intent's randomly-slugged
> workspace directories to the actual repos they contain, their branches, their spec-note
> locations, and how each relates to the separate manual `dappu/`/`code/` clone of the same repo.
> Compiled by direct filesystem/git inspection (not from memory) on the date above — re-verify
> ahead/behind counts before trusting them if you're reading this more than a few days later.
>
> **Related:** [`GRAPHIFY_SETUP.md`](GRAPHIFY_SETUP.md) links back here from its Integration Plan
> section, since that plan references several of these same repos by name.

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

## Guessed origin of the workspace slug names

Christopher doesn't remember why these particular words got assigned and asked for best guesses
based on creation order and repo content — these are inferences, not confirmed history:

- **`specs-sync`** (→ `resume`) — plausibly the very first Intent workspace created, using
  `resume` as a low-stakes test subject while getting familiar with Intent's spec-note workflow.
  "Specs" fits a workspace meant for exercising the spec/note system itself.
- **`specs-sync-2`** (→ `trading-bot_arbitrage...`) — likely just Intent auto-incrementing because
  `specs-sync` was already taken by the time this second workspace was created, not because of any
  thematic link to the trading bot. Order-of-creation collision, not intentional pairing.
- **`tests-config`** (→ `gratitude-token-project`) — this repo is Hardhat-heavy with a large test
  suite (`npx hardhat test`), so "tests-config" may reflect Intent sampling words related to the
  detected project type/tooling at creation time, or may be coincidental from a random word-bank.
- **`react-config`** (→ `gratitude-token-project_docs`) — Docusaurus is a React framework, so
  "react-config" plausibly reflects the same kind of stack-detection naming as `tests-config`
  above. Consistent with that theory, but not confirmed.
- **`md-sync`** (→ `trading-assistant`) — this repo is the cross-repo `AGENT-SYNC/` markdown-
  handoff hub referenced by every other repo, so "md-sync" (markdown sync) is a plausible fit for
  its actual purpose rather than a random pairing.
- **`end-update`** (→ `divorce-custody-assistant`) — no obvious thematic link found; most likely a
  coincidental random slug rather than anything meaningful, despite how it reads next to this
  repo's subject matter.
- **`background-request`** — the empty leftover slug (no repo ever cloned into it). Best guess: a
  workspace Intent spun up for a one-off request that got backgrounded or abandoned before
  Christopher (or the assigned agent) ever cloned a repo into it.

None of the above is verified against Intent's actual naming logic — treat as memory aids, not
documentation of how Intent's namer works.

## Table

"Intent sync" and "Manual sync" are each clone's own ahead/behind against `origin/main` —
they're independent numbers, not compared to each other.

| Repo | Intent workspace path | Intent branch | Intent sync | Intent spec note | Manual clone | Manual branch | Manual sync |
|---|---|---|---|---|---|---|---|
| resume | `intent/workspaces/specs-sync/resume` | `ground-repo-context` | 3↑/0↓ (local-only housekeeping commits; substance already upstream via manual clone) | `intent/workspaces/specs-sync/.workspace/notes/spec.md` | `dappu/resume` (live/deployed) | `main` | 0↑/0↓ |
| gratitude-token-project | `intent/workspaces/tests-config/gratitude-token-project` | `ground-repo-context` | 13↑/36↓ ⚠️ | `intent/workspaces/tests-config/.workspace/notes/spec.md` | `dappu/gratitude-token-project` | `main` | 6↑/35↓ ⚠️ |
| gratitude-token-project_docs | `intent/workspaces/react-config/gratitude-token-project-docs` | `ground-docs-repo` | 10↑/0↓ ⚠️ | `intent/workspaces/react-config/.workspace/notes/spec.md` | `dappu/gratitude-token-project_docs` (live/deployed) | `main` | 0↑/0↓ (but see uncommitted-changes flag below) |
| gratitude-token-project_testPublish (2026-04-06 showcase lane) | `intent/workspaces/tests-config/gratitude-token-project_testPublish_2026-04-06-showcase-lane` | n/a — static export dir, not a git repo | — | — | none found under `dappu/` | — | — |
| gratitude-token-project_testPublish (2026-08-28) | `intent/workspaces/tests-config/gratitude-token-project_testPublish_2026-08-28` | n/a — static export dir, not a git repo | — | — | `dappu/gratitude-token-project_testPublish_2026-08-28` (live/deployed, is a real git repo there) | `main` | 0↑/0↓ |
| trading-bot_arbitrage_DAPPUv3_hardhat_UNI-CAKE | `intent/workspaces/specs-sync-2/trading-bot-arbitrage-dappuv3-hardhat-uni-cake` | `main` | 0↑/0↓ | `intent/workspaces/specs-sync-2/.workspace/notes/spec.md` | `dappu/trading-bot_arbitrage_DAPPUv3_hardhat_UNI-CAKE` | `main` | 0↑/0↓ |
| trading-assistant | `intent/workspaces/md-sync/trading-assistant` | `main` | 0↑/0↓ | `intent/workspaces/md-sync/.workspace/notes/spec.md` | `code/trading-assistant` | `main` | 0↑/0↓ |
| divorce-custody-assistant | `intent/workspaces/end-update/divorce-custody-assistant` | linked git **worktree** of the code/ clone — not an independent clone (see flag below) | same repo as Manual sync — no separate number | `intent/workspaces/end-update/.workspace/notes/spec.md` | `code/divorce-custody-assistant` | detached HEAD (`@ 9c950ef` as of this writing); other local branches: `alfred/housekeeping-v11v12`, `custody-portal`, `main` | uncommitted change present (not inspected — case-sensitive content) |

There's also an empty `intent/workspaces/background-request/` slug with no repo cloned into it —
leftover, safe to ignore or delete.

## Flags worth knowing about

- **gratitude-token-project has real, unreconciled divergence on both its worktrees** — the manual
  `dappu/` clone is 6 ahead / 35 behind `origin/main`, and the Intent-workspace clone is 13 ahead /
  36 behind. Christopher is actively at work in that workspace already — no need to worry about
  resolving this, flagging it only so it's visible in one place.
- **gratitude-token-project_docs also has divergence, on the Intent-workspace side only** — that
  clone (`react-config/gratitude-token-project-docs`, branch `ground-docs-repo`) is 10 ahead / 0
  behind `origin/main`. The manual `dappu/gratitude-token-project_docs` clone (the live/deployed
  one) matches `origin/main` exactly (0↑/0↓) but has a large pile of uncommitted local changes —
  several whitepaper PDFs deleted, several new ones added, `docs/DOCUSAURUS_AGENT_INSTRUCTIONS.md`
  modified, and untracked `.augmentignore`, `.intent/`, `AGENT-SYNC/`, and `specs/` directories.
  Same as above — Christopher's aware and this isn't something to act on from here, just flagging
  it in one place.
- **divorce-custody-assistant's Intent-workspace copy is a git *worktree*, not an independent
  clone** — it shares the same `.git` as `code/divorce-custody-assistant`. There is only one
  actual working-tree checkout of case data on disk (plus whatever's staged in Intent's UI state);
  don't treat the Intent copy as a separate privacy boundary.
- The two `gratitude-token-project_testPublish_*` directories under Intent are static export
  output, not git repos — they're build artifacts, not sources of truth.
- **`gratitude-token-project_testPublish_2026-04-06-showcase-lane`, inspected directly** — a local
  Vite build snapshot timestamped Apr 6 09:14, living only under the `tests-config` Intent
  workspace with no `dappu/` counterpart and never pushed to GitHub as its own repo. It bundles
  five different 404-page variants side by side: `404.html`, `404_old.html`, `404_1stdraft.html`,
  `404_2nd draft.html`, and `404_1st+2nd.html` — plus a stray duplicate `favicon copy.svg`. This
  looks like a local A/B staging build for comparing 404-page designs before picking one, predating
  both the `2026-01-05` and `2026-08-28` published `testPublish` snapshots. Notably,
  `404_1st+2nd.html` here is the same rich, animated 404 content (glow text, mushrooms, blockchain
  cubes) that was later deleted from the live repo's `public/` in commit `73b1ea4` and just
  restored this session as `public/404-legacy.html` — so this showcase-lane folder is effectively
  the archival source for that content if it's ever needed again.

## Where the detailed handoff record lives per repo

Most of these repos keep a running coordination doc beyond just the Intent spec note:
- `resume` → `specs/KAVANAH_INTENT_SPEC.md` (session-by-session account) + `CHANGELOG.md` (dated,
  newest-first) + `AGENT-SYNC_PUBLIC/` (per-creator handoff subdirs)
- `gratitude-token-project` → `AGENT-SYNC/` (per-creator handoff subdirs: `created-by-alfred/`,
  `created-by-kavanah/`, `created-by-christopher/`, `created-by-augment-vscode-migration/`)
- `trading-assistant` → the hub for cross-repo `AGENT-SYNC/` handoffs referenced by every other repo

## Where session chat logs actually live

Added 2026-08-31, from direct inspection — useful when a UI thread gets stuck ("Awaiting tool
response" hangs) and you need to point a fresh session at the verbatim history instead.

- **Claude Code CLI**: one `.jsonl` file per session, under
  `~/.claude/projects/<cwd-path-with-slashes-as-dashes>/`. The directory name is derived from the
  *working directory the CLI was launched from*, not the repo name — so the same repo has two
  separate log directories depending on which worktree you launched from:
  - Launched from the Intent-workspace clone: `~/.claude/projects/-Users-christopherwilson-intent-workspaces-tests-config-gratitude-token-project/` (60+ session files as of this writing — this is the active one)
  - Launched from the manual `dappu/` clone: `~/.claude/projects/-Users-christopherwilson-dappu-gratitude-token-project/` (only contains `memory/`, no session `.jsonl` files yet — hasn't been launched from there directly)
  - Per-project persistent memory (separate from session transcripts) lives at
    `<same-dir>/memory/MEMORY.md` plus individual topic files.
  - Note: `/resume` inside the CLI matches on the *exact* launch directory, so if it reports "No
    conversations found," you're very likely running from a different cwd than the sessions were
    logged under (e.g. shell `cd`'d into `dappu/` instead of the Intent workspace path) — check
    `pwd` first before concluding history was lost.
- **Augment Intent (the desktop app)**: standard Electron app-support layout at
  `~/Library/Application Support/intent/` — mostly Chromium cache/storage internals, not
  human-readable chat logs. The one plausibly relevant file found is
  `~/Library/Application Support/intent/.augment/memory/memory-events.jsonl`, which is app-level
  (not scoped to a single workspace) — likely where a global agent like Chief of Staff would keep
  its own memory, separate from any single workspace's `.workspace/notes/spec.md`. Per-workspace
  UI state (panel layout, file tracking) lives at
  `~/intent/workspaces/<slug>/.workspace/workspace.json` and sibling files, not chat content.
  Nothing here was opened/read beyond directory listings — treat this as "here's where to look,"
  not a confirmed schema.
- **Practical fallback**: when a UI thread hangs and you're not sure whether work landed, the
  reliable ground-truth check is always `git log --oneline` / `git status` in the actual repo
  worktree, not the chat log — every agent in this ecosystem has been confirming state that way
  rather than trusting the UI's own "still thinking" indicator.

---
*If this doc drifts out of date, regenerate the table with a fresh pass of `git branch
--show-current` / `git remote get-url origin` / `git rev-list --left-right --count
origin/main...HEAD` across each path above rather than trusting the numbers here.*
