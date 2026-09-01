# Agent Role Reference

> **This is a mirror.** The canonical copy lives at
> `trading-assistant/AGENT-SYNC/created-by-kavanah/INTENT_AGENT_ROLE_REFERENCE.md` — edit there
> first, then re-sync this copy. Kept here (linked from this repo's `AGENT_IDENTITY_REFERENCE.md`)
> so it's discoverable alongside the rest of the cross-repo identity/legend docs.

Use this guide to choose the right kind of agent for planning, design, implementation, and review.

## Quick chooser

- **Coordinator**: Use when you need help deciding what should happen next, clarifying scope, sequencing work, or keeping tasks aligned.
- **UI Designer**: Use when a change needs hierarchy, storytelling, accessibility, mobile-usability, or overall UX direction before code is changed.
- **Developer**: Use when a task is already approved and ready for concrete code, markup, styling, or behavior updates.
- **Verifier**: Use when completed work needs independent review against acceptance criteria, accessibility expectations, link/interaction checks, or public-safe wording.

## Best default

If you are unsure where to start, start with **Coordinator**.

## Simple workflow

1. **Coordinator** scopes the task and keeps priorities clear.
2. **UI Designer** helps shape the UX if design direction is still needed.
3. **Developer** implements the approved slice.
4. **Verifier** reviews the result and flags any gaps.

## Role details

### Coordinator

**Purpose**

Use the Coordinator as the orchestration hub for scope, sequencing, delegation, and task maintenance.

**Use when**

- You want to decide what should happen next.
- A task needs to be scoped before implementation.
- Multiple specialists need coordination.
- Priorities, approvals, constraints, or dependencies need to stay aligned.

**Do not use first for**

- Deep implementation details on an already approved task.
- Final design critique without coordination needs.
- Final approval of completed work.

**What to provide**

- The goal or problem to solve.
- Priorities, constraints, non-goals, or approvals.
- Any specialist input that should affect the queue.

### UI Designer

**Purpose**

Use the UI Designer to shape presentation, hierarchy, accessibility direction, and practical polish before implementation.

**Use when**

- You want help defining the smallest worthwhile UI improvement.
- A page or flow needs hierarchy, storytelling, scanability, or mobile-usability work.
- Accessibility considerations should be built into the task before coding starts.
- You want a narrow Developer-ready UX brief.

**Do not use first for**

- Final approval of completed work.
- Broad project coordination or task sequencing.
- Pure implementation with no design decision involved.

**What to provide**

- The exact page, component, or surface in scope.
- The desired user-facing outcome.
- Scope, tone, or technical constraints.
- Anything that should stay unchanged.

### Developer

**Purpose**

Use the Developer to implement an approved, scoped task in the current codebase.

**Use when**

- A task is approved and ready for implementation.
- The files or surfaces in scope are known or can be tightly narrowed.
- You want concrete code, markup, style, or behavior changes.
- A small technical spike is needed to confirm feasibility within scope.

**Do not use first for**

- Broad planning or task sequencing.
- Final approval or independent review.
- Open-ended design-direction decisions.
- Large bundled requests without a clarified task.

**What to provide**

- The exact task goal and acceptance criteria.
- Scope boundaries and non-goals.
- Likely files, pages, or components in scope.
- Required checks, tests, or smoke validations.

### Verifier

**Purpose**

Use the Verifier as the independent acceptance-criteria gate for completed work.

**Use when**

- An implementation-complete task needs approval.
- A UI change needs accessibility or interaction review.
- Public-facing copy or docs need public-safe wording review.
- You want the smallest validation needed before calling work done.

**Do not use first for**

- Brainstorming new features.
- Choosing design direction from scratch.
- Implementation without an approved task.
- Vague review requests with no criteria.

**What to provide**

- The task goal and acceptance criteria.
- The files or surfaces that changed.
- What was already tested or checked.
- Any known risks that should be re-checked.

## Shared guidance

- Keep tasks narrow, concrete, and easy to verify.
- State constraints and non-goals up front.
- Use evidence when asking for approval or review.
- For sharable/public-facing artifacts, avoid private coordination details, repo-private process notes, and workspace-only instructions.

---

## 🧩 AugmentIntent Features
*Appended 2026-08-31, after revisiting Intent and documenting the upgrades to our setup.*

### 🖥️ Logins possible — AI coding CLIs

| CLI | Notes |
|---|---|
| 🧠 **Augment Auggie** | Real-time codebase understanding with Context Engine; GitHub, Linear, and Sentry workflow integration; multiple AI model provider selection & use. Used to consist of Claude/GPT models only — now also has Gemini/GLM/Grok/Kimi/Prism. |
| 🤖 **Anthropic Claude Code** | |
| 🔓 **OpenAI Codex** | |
| 🔧 **OpenCode** | |
| 🥧 **Pi** | |
| 🏭 **Factory Droid** | |

### 🏭 Specialists Factory with AugmentIntent

- 📂 `~/.augment/specialists`
- 🎛️ When starting a chat, select a model and system prompt.
- ✏️ Built-in specialists have been customized — overrides saved at `~/.augment/specialists/`.

**Available specialists:**

- 🗂️ **Chief of Staff** — App-level assistant for managing Intent itself: workspaces, settings,
  specialists, navigating and learning the app. Not a repo-coding agent — hands code work off to
  the right workspace/specialist rather than doing it directly. Uses proposal cards for reversible
  changes and confirmation cards for destructive ones, so nothing changes without review.
- 🔨 **Implementor** — Executes one assigned implementation task with a tight scope: no refactors,
  no scope creep. Best used as a delegate inside a coordinated wave of work, not as a standalone
  planner.
- 🛠️ **Developer** — Plans and implements solo: writes a spec first, waits for approval, then
  implements the whole thing itself. Good for self-contained tasks that don't need multi-agent
  coordination.
- 🔁 **Ralph** — Runs an iterative build-and-test loop: agrees a plan and test criteria with the
  user, then autonomously cycles fresh work/test agents until tests pass or it needs help. Good
  fit for tasks with a clear, automatable pass/fail signal.
- 🎨 **UI Designer** — Produces accessible, production-ready interface work that matches the
  codebase's existing design system. Holds hard requirements around accessibility (contrast,
  focus states, semantic HTML) and full interactive-state coverage (hover/active/focus/disabled/
  loading/error).
- 🧭 **Coordinator** — Orchestration hub: plans work, breaks it into tasks, and delegates to other
  specialists. Never edits code directly; keeps the plan current as the source of truth. (See
  "Role details" above for the fuller Purpose/Use-when breakdown of this role.)
- ✅ **Verifier** — Independent reviewer: checks completed work strictly against acceptance
  criteria, evidence-driven, no partial approvals without explicit sign-off. Used as a final gate,
  not for design or planning input. (See "Role details" above for the fuller breakdown.)
- ➕ ...plus the ability to **"Create Specialist"**

*Summaries scrubbed from the underlying specialist definitions at `~/.augment/specialists/` —
internal tool-call syntax and orchestration mechanics omitted; purpose and best-use guidance kept.*
