# Cosmos Agent Role Reference — Advisor + the code-review fleet
#### Compiled by Cosmos Advisor (`drasticstatic` environment) · 2026-09-13

> **What this is:** a public-facing map of the Cosmos side of the fleet — who Cosmos Advisor is,
> how it pairs with Mystarch, and what each of the seven code-review experts actually does. Companion
> to [`INTENT_AGENT_ROLE_REFERENCE.md`](./INTENT_AGENT_ROLE_REFERENCE.md), which covers the Augment
> Intent specialists (Coordinator / UI Designer / Developer / Verifier / Chief of Staff).
>
> **Related:** [`AGENT_IDENTITY_REFERENCE.md`](./AGENT_IDENTITY_REFERENCE.md) (the cross-surface
> identity map — Kavanah, Alfred, Fortuna, Mystarch, Aunt Harriot, Littlebird, and Cosmos Advisor).

---

## The short version

Augment Intent agents (Kavanah, Alfred, Fortuna, Mystarch) run **where Christopher is** — a terminal
or an Intent workspace on his machine. Cosmos agents run **where the code is** — cloud sessions that
wake on a GitHub event, do a scoped job, and report back.

That difference drives everything else. An Intent agent is a collaborator you talk to. A Cosmos
expert is closer to a colleague who is already reviewing your pull request by the time you open it.

---

## Cosmos Advisor — the seat that builds the other seats

**Surface:** Cosmos cloud session · **Model:** Claude Opus 5

Advisor does not review code. It designs, deploys, and maintains the agents that do, and it owns the
orchestration layer they share: commit-attribution conventions, handoff lanes, session logs,
public/private boundaries, and the sync filters that keep private material private.

Two environments, kept deliberately distinct:

| Environment | Scope | Purpose |
|---|---|---|
| `drasticstatic` | all 32 repositories, team default | Working environment. Cross-repo retrieval. |
| `drasticstatica (cosmos init chat)` | narrower | The original setup chat, kept for provenance. |

Handoff lanes mirror that split: `AGENT-SYNC/created-by-cosmos_Advisor-drasticstatic/` and
`…-drasticstatica/`. The near-identical names are intentional — they record *which* environment
produced a claim, which matters when a handoff says "verified across the fleet," because only the
working environment can see the fleet.

### Advisor and Mystarch — the ChiefAdvisor pairing

Mystarch is Christopher's app-level Chief of Staff on Augment Intent. Advisor is the equivalent seat
on Cosmos. They are the same *function* on opposite sides of the machine boundary, and they are
designed to work as one:

| | Mystarch | Cosmos Advisor |
|---|---|---|
| Runs | locally, Augment Intent / Claude Code CLI | cloud sessions |
| Sees | local filesystem, Intent workspaces, uncommitted work — and across repositories when Christopher grants it | pushed state of all 32 repositories, standing |
| Needs | Christopher present, machine on | nothing; wakes on a GitHub event |
| Strength | local context, live collaboration, the human in the room | unattended event-driven work, fleet-wide change in a single pass |
| Blind spot | needs someone there — nothing happens while Christopher is away | no local filesystem; nothing unpushed or uncommitted |

**Cross-repo reach is not an Advisor exclusive.** Mystarch — and Alfred, Fortuna, and Kavanah — can
see across repositories whenever Christopher grants that access. Treating cross-repo view as the
dividing line would be wrong, and an earlier revision of this file made exactly that mistake.

The durable difference is **attendance**. Mystarch is attended: it does excellent work *with*
Christopher, and none while he is asleep or the laptop is shut. Advisor is unattended: it wakes on a
pull request at 03:00, does a scoped job, and leaves a record. Scope is a permission either seat can
be given; attendance is structural.

So neither is authoritative alone, and `mystarch_chief-of-staff/PENDING-TASKS.md` is the shared
living document between them — the one place either seat can read to learn what the other has in
flight.

`mystarch_chief-of-staff_acp-spoof` is the experimental local portal: a controlled proof-of-concept
that exposed a real finding about Augment Intent's local bridge accepting fabricated workspace
headers. It is documentation and a repro, not daily-use infrastructure — but it is the direction a
real Cosmos-to-local channel would take if one is ever built.

---

## The code-review fleet

Seven experts covering a pull request from open to merge. They are **not** seven opinions on the same
diff — each occupies a distinct slot, and the interesting property is that the fleet gets better over
time rather than merely running repeatedly.

### Reasoning-heavy seats (Claude Opus 5)

**PR Author** — takes a task and produces a pull request. Owns the change end to end: writes it,
responds to CI, addresses review feedback, and resolves safe merge conflicts. User-launched.

**Deep Code Reviewer** — wakes when a PR opens and posts inline bug-finding comments directly on
GitHub. Non-interactive and deliberately narrow: finding defects, not discussing design.

**Pair Reviewer** — the interactive counterpart. Walks a human through a change focusing on intent,
history, and judgment — the questions that need a conversation rather than a comment thread.

**PR Risk Analyzer** — assesses how risky a change actually is, auto-approves genuinely low-risk
changes, and launches Pair Reviewer when a change needs human attention. Its exclusion list is a
hard floor: financial paths, deploy and GitHub Pages surfaces, agent rule files, and the ACP spoof
repository can never be auto-approved, and it must state its reasoning when it declines.

### Mechanical seats (tuned smaller models)

**PR Fixer** — adopts a pull request that already exists and drives it to ready-for-review: review
feedback, CI failures, merge conflicts. Does not create PRs or assign reviewers. The counterpart to
Author for work that started by hand.

**PR Dashboard Manager** — observer-only. Maintains one canonical status view per PR. It never
repairs code, approves, coordinates other experts, or monitors CI — the constraint is the point, so
the dashboard stays a place to look rather than another actor.

**Code Review Memory Manager** — hidden companion, invisible in day-to-day use. When a PR closes or
merges, it distills what the review actually surfaced into per-repository notes the other reviewers
read on their next run. This is the piece that makes the fleet improve: a false positive corrected
once stops recurring, and a real pattern caught once gets recognized again.

---

## How a change moves through the fleet

```
task ──► PR Author ─────────┐
                            ├──► PR opens ──► Deep Code Reviewer (inline comments)
existing PR ──► PR Fixer ───┘                 PR Risk Analyzer (risk call)
                                                   │
                                        low risk ──┴── needs judgment
                                            │              │
                                       auto-approve   Pair Reviewer (human, interactive)
                                            │              │
                                            └──────┬───────┘
                                                   ▼
                                          merge / close
                                                   │
                                    Code Review Memory Manager
                                    (distills the lesson for next time)

PR Dashboard Manager observes throughout, writes the canonical status view, changes nothing.
```

---

## Scope and guardrails

Triggers are currently scoped to a single repository (`drasticstatic/resume`) rather than the whole
fleet. That is deliberate: an expert that misbehaves on one repository is a bad afternoon, whereas
one that misbehaves across 32 is a bad week. Scope widens once behavior is observed, not before.

Fleet-wide constraints that no expert may bypass:

- Financial, tax, custody, and legal paths are never auto-approved
- Deploy and GitHub Pages surfaces are never auto-approved
- Agent rule files (`CLAUDE.md`, `AGENTS.md`, `AGENT-SYNC/`) are never auto-approved
- Every commit carries attribution and a session URL, enforced by a `commit-msg` hook
- `AGENT-SYNC/` and `logs/` never reach a public mirror

---

## Reading this alongside the Intent reference

| You want… | Read |
|---|---|
| Which Intent specialist fits a task | [`INTENT_AGENT_ROLE_REFERENCE.md`](./INTENT_AGENT_ROLE_REFERENCE.md) |
| Which persona you are talking to, and on what surface | [`AGENT_IDENTITY_REFERENCE.md`](./AGENT_IDENTITY_REFERENCE.md) |
| What the Cosmos experts do and how they chain | this file |

---

*Source of truth for the Cosmos side. If a repository's own `CLAUDE.md` disagrees with this file,
this file is the one to trust and the repository should be updated to match.*
