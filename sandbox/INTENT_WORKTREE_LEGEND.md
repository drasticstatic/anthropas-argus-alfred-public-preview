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
> [`DETACHED_HEAD_GUIDE.md`](../../divorce-custody-assistant/DETACHED_HEAD_GUIDE.md) (in
> `code/divorce-custody-assistant/`) explains that repo's intentional dual-checkout setup — read it
> before assuming its detached HEAD is a problem.

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
| mystarch_chief-of-staff | `intent/workspaces/__chief__` | git-initialized in place (option b, 2026-09-01) — see below, this row is the exception to "not a git repo" | 0↑/0↓ vs `origin/main`, but has uncommitted/untracked scaffolding work pending auto-commit as of this writing | `intent/workspaces/__chief__/.workspace/notes/spec.md` | `code/mystarch_chief-of-staff` (added 2026-09-01, fresh `git clone` from GitHub — for native-terminal fallback when Intent's UI hangs, see § below) | `main` | 0↑/0↓ against `origin/main`, but **stale relative to `__chief__` itself** — cloned at `9c8ca88`, missing whatever `__chief__`'s uncommitted working tree has that auto-commit hasn't pushed yet; `git pull` before trusting it as current |

There's also an empty `intent/workspaces/background-request/` slug with no repo cloned into it —
leftover, safe to ignore or delete.

## The `__chief__` workspace — Intent's built-in Chief of Staff seat

Added 2026-08-31. Unlike every other row above, `intent/workspaces/__chief__` is not a per-repo
clone — it's Intent's **reserved, fixed slug** (not one of the random word-pair names like
`tests-config`) for the app-level Chief of Staff agent, identified in this ecosystem as
**Mystarch**. Confirmed by direct inspection:

- Directory created 2026-08-26 14:51, predates this doc and every handoff referencing Mystarch's
  elevation to app-level on 2026-08-31 — the seat existed before it was actively used.
- Contains only `.workspace/` (notes, agents, logs, events.jsonl, panel-layout-history.json) — no
  git repo, no `origin` remote, nothing to clone into it by design.
- Its spec note lives at the same relative path as any other workspace:
  `intent/workspaces/__chief__/.workspace/notes/spec.md`.
- What makes it different functionally: from this seat, `ws.app.workspaces.list(...)` resolves
  and returns *every* workspace across every repo (confirmed live) — the cross-workspace reach
  that repo-scoped workspaces (like `tests-config`) don't have. That's the whole point of the
  elevation effort documented in `gratitude-token-project`'s
  `AGENT-SYNC/created-by-mystarch/HANDOFF_20260831_*.md` handoffs.
- To reopen this workspace's panel in the Intent UI after navigating away from it, use the app's
  workspace switcher/navigate-to-workspace action targeting route `/workspace/__chief__` — same
  mechanism as opening any other workspace, just with this fixed slug instead of a random one.

**Correction/addition (2026-08-31, later same day):** Christopher reports `__chief__` does **not**
appear in Intent's workspace list or search UI, and the only way he found to reopen it after
navigating away was the `/workspace/__chief__` nav-link chip above — not a normal "File → New" or
workspace-switcher entry. Confirmed from the seat itself: `ws.app.workspaces.list(...)` — the same
call that returns every other repo-backed workspace — does **not** include `__chief__` in its
results (checked live, only the 6 repo-backed workspaces come back). So this seat has full
*outbound* cross-workspace reach (it can list/see all 6 repo workspaces) but is itself invisible to
that same listing — asymmetric, not a bug in the recon, an actual property of how this reserved
slug is exposed. Practical implication: don't rely on Intent's normal workspace list/search to find
this seat again; keep the `/workspace/__chief__` nav-link (or just remember the literal route)
somewhere durable, since re-deriving it requires already knowing the fixed slug.

**Also confirmed live (2026-08-31):** this seat's own spec note is empty — `ws.note.read("spec")`
returns no content, task status `not_started`, created 2026-08-26 and never written to since. And
`ws.agent.list()` from this seat shows only itself plus two `New thread` agents with 0 messages
each (idle placeholders, not agents actually dispatched to do work) — i.e. despite `ws.agent.*`
tools being available from this seat, no subagents have actually been delegated/deployed from here
yet. Everything done from `__chief__` so far (repo file reads/edits, terminal diagnostics, the
coordinator handoffs) was done directly by the seat's own single agent thread, not via delegation.

## 🤔 Pondering — Anthropic login vs. Augment-native persistence (2026-08-31)

Christopher's working hypothesis, recorded here rather than settled: the Chief of Staff agents in
this ecosystem (Mystarch at `__chief__`, and by extension the per-workspace Kavanah coordinators)
are currently authenticating through **Anthropic's own login** for Claude Code CLI inside Intent,
not through an **Augment-native subscription** login. If true, that's a plausible (additional, not
alternative) explanation for why `__chief__`'s spec note and agent roster are empty even after the
2026-08-31 elevation to `ws.app.*` tool access confirmed above: Claude Code acting as the model
underneath is a substitute for the surface, but it doesn't bring in Augment's own Context Engine /
shared codebase-understanding layer ("AugmentMagic") the way a native Auggie session would. This is
a distinct hypothesis from the tool-scope explanation already recorded in
[`AGENT_IDENTITY_REFERENCE.md`](AGENT_IDENTITY_REFERENCE.md)'s "How the four relate" section
(repo-scoped `ws.*` vs. app-level `ws.app.*`, before elevation) — that one is about which *tools*
were reachable; this one is about which *underlying capability* is powering the session regardless
of tool reach. Both could be true at once and compound each other.

**If Augment-native login becomes available and persists** (something Christopher has wanted to
test but hasn't been able to under current constraints), the expectation is Mystarch and the
Kavanah coordinators would resume operating with Intent's full native capability set — the agent
roles and specialist factory laid out in
[`INTENT_AGENT_ROLE_REFERENCE.md`](INTENT_AGENT_ROLE_REFERENCE.md) (mirrored here 2026-08-31;
canonical source is `trading-assistant/AGENT-SYNC/created-by-kavanah/INTENT_AGENT_ROLE_REFERENCE.md`)
— rather than the current ClaudeCode-as-substitute mode.

**Longer-term aspiration, not yet actionable:** run Augment-native agents (Auggie) and Claude-based
agents inside Intent *simultaneously* once this is sorted out, with Claude-based agents ideally kept
scoped to the native-terminal `code/`/`dappu/` worktrees (Alfred/Fortuna's lane) rather than
competing with Auggie inside Intent workspaces — harnessing both at once. Christopher's own read:
this becomes easy once the graphify knowledge-graph exports are wired up and something can point to
them at that stage, giving both agent families a shared reference layer to work from even while
authenticating differently. Not started; recorded here as a marker for when auth/subscription state
changes.

**Concrete edge case confirming this, found 2026-09-01:** Intent's Settings surfaced
`Please authenticate with Augment first (run auggie login)` when Christopher tried to use Intent's
**GitHub-connect capability** (the same feature behind the create-workspace proposal card's base-branch
picker — see the `gratitude-token-project_astro` branch-resolution failure logged under Active/Next Up
in `pending-tasks.md`). This is a strong, concrete data point for the hypothesis above, not just a new
guess: GitHub-connect is an **Auggie-CLI-backed Intent capability**, and this session is running as
**Claude Code CLI** under the hood rather than a native `auggie login` session — so the capability
straightforwardly isn't available here regardless of `ws.app.*` tool reach. In other words, the
branch-picker bug from the astro workspace attempt and this explicit auth prompt are likely **the same
root cause** surfacing two different ways: one silent (card just can't resolve a branch), one explicit
(Settings names the missing `auggie login` outright). A native Auggie-authenticated Chief of Staff
session would plausibly not hit either failure.

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
- **`trading-assistant` and `divorce-custody-assistant` both moved from `~/ClaudeCodeCLI/` to
  `~/code/` at some point** (confirmed by Christopher 2026-08-31) — any older doc, handoff, or
  memory file referencing `~/ClaudeCodeCLI/trading-assistant/` or
  `~/ClaudeCodeCLI/divorce-custody-assistant/` is stale; the live path is `~/code/` for both.
- **Correction (2026-08-31, same day as first written above):** an earlier pass of this file
  claimed divorce-custody-assistant's linked worktree was broken with a stale `ClaudeCodeCLI/`
  gitdir pointer (`fatal: not a git repository` on `git status`). Re-verified directly and that
  claim was **wrong** — `git status`, `git log`, and `git worktree list` all work cleanly on both
  `code/divorce-custody-assistant` and the Intent-workspace copy at
  `end-update/divorce-custody-assistant`. The likely explanation: a transient/misread state during
  an earlier session-recovery gap, not a real fault. **What's actually true, per
  `code/divorce-custody-assistant/DETACHED_HEAD_GUIDE.md`:** this repo runs an intentional
  dual-checkout setup. `code/divorce-custody-assistant` is deliberately kept in **detached HEAD**
  (currently `33eafbf`) — that's where real casework commits actually land and push straight to
  `origin/main`; it is the primary/current checkout, not a stray state to fix. The Intent-workspace
  worktree (`end-update/`) is attached to `main` but is the secondary, less-current track. Read that
  guide before touching either checkout — it also explains why some git UIs (VS Code's push flow
  included) can fail or hang pushing from a detached checkout, and the fix (`git push origin
  HEAD:main`) when that happens.
  **Also as of 2026-08-31**: Christopher will no longer be prompting through Intent for either of
  these two repos going forward — Fortuna/Alfred's native-terminal lane against `code/` is now the
  primary way of working in both.
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

## Related private references (witness-only — filenames only, not content)

These live inside `trading-assistant`, a private repo/hub — listed here so the *names* are
discoverable from this public-facing legend, even though reading the actual content requires
private repo access. Do not copy their content into this or any other public-facing file.

- `trading-assistant/specs/INTENT_STARTUP_INIT.md` — the original Kavanah Fleet bootstrap prompt
  (historical, preserved as-is; superseded by `INTENT_STARTUP_NOW.md` as the live startup doc).
- `trading-assistant/AGENT-SYNC/CROSS_REPO_RULES.md` — the hub-of-spokes cross-repo coordination
  rules referenced by every other repo's `CLAUDE.md`.
- `trading-assistant/AGENT-SYNC/created-by-kavanah/INTENT_AGENT_ROLE_REFERENCE.md` — quick-chooser
  guide for which Intent specialist (Coordinator/UI Designer/Developer/Verifier) fits a given task.
- `trading-assistant/AGENT-SYNC/created-by-kavanah/WORKSPACE_WELCOME_PROMPTS.md` — per-workspace
  welcome/bootstrap prompts Kavanah hands a fresh Intent session.

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
  - **`__chief__` (Mystarch, app-level seat)**: `~/.claude/projects/-Users-christopherwilson-intent-workspaces---chief--/` — this seat's own session `.jsonl` files and its `memory/` dir both live there. A session launched instead from `~/code/mystarch_chief-of-staff` (added 2026-09-01, see table above) logs under a *different* projects dir (derived from that cwd), so the two don't share transcript history automatically — the code/ clone is a git-level fallback for getting unstuck when Intent's UI hangs, not a mirror of this seat's chat log. Read the `__chief__`-launched `.jsonl` directly (or point a fresh session's `/resume` at that exact path) if you need this seat's verbatim history from outside Intent.
  - **`created-by-*` attribution convention for `mystarch_chief-of-staff`'s own `AGENT-SYNC/`
    (established 2026-09-02):** follows the same surface-decides-authorship rule as everywhere else
    in this ecosystem, applied explicitly since this repo's content and its usual persona share a
    name:
    - Work/handoffs from a session launched at `~/intent/workspaces/__chief__` (through Intent's own
      UI/ACP) → `created-by-mystarch`
    - Work/handoffs from a session launched at `~/code/mystarch_chief-of-staff` (native terminal,
      no Intent UI involved) → `created-by-alfred`
    - Every *other* Intent workspace (not `__chief__`) → `created-by-kavanah`, unchanged from the
      existing convention (see `AGENT_IDENTITY_REFERENCE.md`'s Kavanah section)
    - **Why this matters practically:** the recurring "awaiting tool response" hangs / stream
      timeouts in Intent's UI (this doc's own Pondering section, and the Auggie-login gap) mean the
      native-terminal `code/` fallback gets used often enough that its jsonl transcripts and
      AGENT-SYNC contributions need their own clear lane — otherwise it's easy to lose track of
      which environment actually produced a given piece of continuity work.
    - **Engine options for the `code/` fallback session:** plain Anthropic-subscription Claude Code
      CLI (default), or — when the NVIDIA NIM free-tier proxy (`free-claude-code`, see
      `sandbox/FREE_MODEL_SETUP.md`) is actually up — Alfred-NIM mode, same as any other repo's
      free-model-sandbox option. Also worth trying: Intent's own macOS desktop app has a built-in
      terminal-instance feature that could host this `code/` session without leaving the app, as an
      alternative to a separate native Terminal.app window — untested as of this writing, noted here
      as an option to try rather than a settled recommendation.
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
