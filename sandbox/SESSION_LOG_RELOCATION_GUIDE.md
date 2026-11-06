# Session Log Relocation — What Happens When a Project Directory Moves
#### Compiled by Alfred (Claude Code CLI, native `code/pir` session) · 2026-09-29

> **Note, Nov 6, 2026:** `code/pir` is now `~/drasticstaticLabs/pir-lab`; a planned rename follows `SOP-IT-006` and `my-template/scripts/rename-local-repo.py`. This guide keeps the September moves with their paths as they were.

> **What this is:** A how-to for a real, recurring gotcha — moving a repo's working directory
> orphans its Claude Code session log under the old path, and `/resume` won't find it there because
> it only looks under the *current* cwd's path slug. This doc explains why, how to detect it,
> and the housekeeping convention for cleaning it up without losing anything. Written up after
> living through it firsthand across `pir` and four sibling repos on 2026-09-22/27.
>
> **Related:** [`INTENT_WORKTREE_LEGEND.md`](./INTENT_WORKTREE_LEGEND.md) — the same "path you
> launched from, not the repo name, determines where the session log lives" mechanic, documented
> there for Intent-workspace vs. manual-clone worktrees of the same repo. This doc is the general
> case (any directory move, not just Intent worktrees) and the concrete cleanup procedure. Read that
> doc's "Where session chat logs actually live" section first if you haven't — this one builds on it.

## The mechanic, in one paragraph

Claude Code CLI keys each session's `.jsonl` transcript by the **working directory the CLI was
launched from**, slugified into a directory name under `~/.claude/projects/`
(`/Users/x/code/pir` → `-Users-x-code-pir`). If a session is *already running* when its working
directory gets moved or renamed on disk, the running process doesn't know — it keeps appending to
the log under the **old** slug until it exits or crashes. A fresh session launched afterward from
the new path starts a **new**, separate log under the new slug. Same repo, same ongoing work, two
disconnected log directories — and `/resume` (which matches on exact current cwd) will only ever
see the new one, making it look like history vanished when it's actually just sitting under the old
name.

## How to detect it

For any repo you suspect moved:

1. List `~/.claude/projects/` and look for a slug matching the repo's **old** path alongside one
   matching its **current** path (e.g. `-Users-x-pir` next to `-Users-x-code-pir`).
2. Check whether the old path still exists on disk (`[ -d ~/pir ]`, or equivalent). If it doesn't,
   that project folder is orphaned — safe to archive, nothing will ever write to it again.
3. If you want to confirm it's the *same* underlying session (not just a coincidentally-similar
   name), compare session IDs: the old log's last few lines and the new log's first few lines often
   share a `sessionId` field, or the old log's tail will read as a real conversation that matches
   what a `pending-tasks.md`/handoff doc says happened around the move date.
4. **A shared crash timestamp across multiple, otherwise-unrelated old-path logs is a strong
   signal of a single crash event, not several unrelated failures.** When we did this pass, four
   different repos' orphaned logs (`pir`, `O-R-G`, `private-legal-repo`,
   `mystarch_chief-of-staff`, `the-holy-earth-foundation`) all stopped dead at the exact same
   timestamp (2026-09-27 14:50) — that's the moment Christopher's terminal application crashed and
   rebooted, taking down every session open in it at once, not five separate incidents.

## The archival convention

Don't delete anything — `mv` the orphaned project folder into a dedicated archive directory,
keeping its original slug name so it's still greppable by old path:

```sh
ARCHIVE=~/.claude/projects/_archived-pre-move-sessions
mkdir -p "$ARCHIVE"
mv ~/.claude/projects/<old-slug> "$ARCHIVE/<old-slug>"
```

Then add one line to the running legend at `~/.claude/projects/ARCHIVE-LOG.md` (old slug → new
slug, real old/new filesystem paths, date archived, why). That file is the map for "where did this
repo's pre-move history go" the next time anyone — human or agent — goes looking and finds it
missing from where they expect it.

**Never archive a slug whose real directory still exists**, and never archive a slug that's still
being actively written to (check `latest mtime` — if it's from today/this session, leave it alone).
The check in step 2 above (does the old path still exist on disk?) is the safe gate: if it does,
the "old" folder might just be a second legitimate location, not an orphan.

## Why this will matter more, not less, as things scale

`INTENT_WORKTREE_LEGEND.md` already documents today's version of this problem: an Intent-workspace
clone and a manual `code/`/`dappu/` clone of the *same* repo already produce two independent session
logs, because each is its own launch path. Christopher's noted aspiration (recorded there) is a
future with multiple agents working autonomously across multiple git worktrees at once, at real
speed — and every additional worktree is, by this same mechanic, another independent
`~/.claude/projects/<slug>/` bucket. That's not a problem to solve once; it's a shape that repeats
per-worktree, indefinitely. The practical upshot: the archive-folder + `ARCHIVE-LOG.md` legend
pattern in this doc isn't a one-off cleanup, it's the durable housekeeping habit this fleet needs
once multi-worktree, multi-agent scale is real — treat this doc as the template to reapply, not a
closed incident.

## Second mechanism, same root cause: MCP server registrations (found 2026-09-29, same day)

`~/.claude/projects/` isn't the only place Claude Code keys state by project path. MCP server
registrations added via `claude mcp add` (without `-s user`) also live per-project, inside
`~/.claude.json` under `projects["<absolute-path>"].mcpServers`. Same failure shape: two WordPress
MCP servers (`emcp-psychedelicsinrecovery-org`, `emcp-service-psychedelicsinrecovery-org`) were
registered under the old `/Users/christopherwilson/pir` project entry and simply never existed
under `/Users/christopherwilson/code/pir` — they weren't broken or disconnected, they were never
configured for the path this session actually runs from, so they showed up as unavailable with no
obvious link back to the move.

**Fix, same shape as the session-log one:** find the old project's config
(`python3 -c "import json; d=json.load(open('~/.claude.json')); print(d['projects']['<old-path>']['mcpServers'])"`
or equivalent), then re-add each server under the new path with `claude mcp add --transport http
<name> <url> -H "Authorization: ..." -s local` (local scope defaults to the current project path).
Verify with `claude mcp get <name>` — look for `Status: ✔ Connected`.

**Checklist for any future repo move:** don't just archive the old session-log folder — also check
`~/.claude.json`'s `projects["<old-path>"]` entry for an `mcpServers` block before considering the
move fully reconciled. A repo that used any project-scoped MCP server (not just WordPress — this
applies to any `claude mcp add` done without `-s user`) will silently lose access to it the same
way.

## Handoffs

See `agent-notes-pir/by-alfred/20260929-handoff-session-log-relocation-cleanup.md` and
`mystarch_chief-of-staff/agent-notes/by-alfred/20260929-handoff-session-log-relocation-cleanup.md`
for the concrete record of what got archived this round.
