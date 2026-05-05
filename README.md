# Anthropas-Argus-Alfred

> *Wise Counselor · All-Seeing Guardian · Human-Centric Intelligence*

[![License: MIT](https://img.shields.io/badge/license-MIT-lightgrey?style=flat)](https://github.com/drasticstatic/.github)

**Anthropas-Argus-Alfred** is the local AI assistant framework for Christopher Wilson's machine — a system-level agent that supports all active projects, keeps context lean, and operates as a trusted right-hand across trading, web3, personal productivity, and everything in between.

---

## The Name

**Anthropas-Argus-Alfred** is a strong, multi-layered name that connects Claude's origins to classical mythology and the ultimate "butler" archetype.

### The Three Pillars

| Name | Origin | Role |
|------|--------|------|
| **Anthropas** | Anthropic · Greek *ánthrōpos* (human) | *Human-centric intelligence* — a nod to Anthropic's design philosophy: helpful, harmless, and honest. Alfred is a partner, not just a tool. |
| **Argus** | Greek mythology · Argos Panoptes ("all-seeing") · *argos* = "bright, shining" | *Operational awareness* — monitoring workflows, watching repos, identifying gaps, maintaining security and efficiency across the local environment. Synonyms: sentinel, watchman, guardian. |
| **Alfred** | Old English *Ælfrǣd* · *ælf* (elf) + *rǣd* (counsel/advice) | *Master Butler archetype* — the legendary Alfred Pennyworth. Highly capable, discreet, always one step ahead. A proactive strategist and trusted right-hand. "Wise counselor." |

### The Goal

Use this framework to balance **deep technical execution** (Argus) with a **supportive, reliable, human-aligned personality** (Anthropas/Alfred).

Alfred isn't just a task executor — he is a proactive strategist who keeps Christopher's ecosystem running smoothly, context efficient, and ready to scale.

---

## What Alfred Does

- **Secretary / local coordinator** — handles cross-repo tasks, housekeeping, and context management so specialized agents (Fortuna, Auggie, Kavanah) stay focused on their domains
- **Free-model sandbox** — integrates `free-claude-code` to route capable open-source models (NVIDIA NIM, DeepSeek, OpenRouter) for appropriate tasks, preserving Claude quota for high-value work
- **Watchman** — monitors repos, flags security issues, tracks pending tasks across the full project ecosystem
- **Session efficiency** — lightweight startup, clean context, fast answers without spinning up the full trading-assistant context when not needed

---

## Architecture

```
anthropas-argus-alfred/
  CLAUDE.md              Alfred's persistent instructions (this repo's soul)
  README.md              This file
  sandbox/               Free-model integration notes + Alfred's operational config
    free-claude-code/    → /code/free-claude-code/ (see below)
  logs/alfred/           Alfred session logs (YYYY/MM-Mon/)
  AGENT-SYNC/            Handoff files between Alfred and other agents
    created-by-alfred/   Files Alfred writes for other agents
  .claude/skills/        Alfred's skill library
```

### Free-Model Sandbox

The free-model capability lives at `/code/free-claude-code/` — a working copy of [Alishahryar1/free-claude-code](https://github.com/Alishahryar1/free-claude-code), maintained as a `drasticstatic` fork with upstream tracked for comparison.

Configured providers: **NVIDIA NIM**, **DeepSeek**, **OpenRouter**, **Ollama** (local).

See `sandbox/FREE_MODEL_SETUP.md` for Alfred's integration guide.

---

## Agent Ecosystem

| Agent | Platform | Primary Domain |
|-------|----------|---------------|
| **Alfred** (this repo) | Claude Code CLI | System coordinator, secretary, free-model sandbox |
| **Fortuna** | Claude Code CLI | Trading workflow, session analysis, reviews, coaching docs |
| **Auggie** | Augment CLI | Code builds — Pine Script, Python, MCP servers, web3 |
| **Kavanah** | Augment Intent | Spec-driven orchestration, file deployment, cross-repo coordination |

Alfred is the generalist layer — the others are domain specialists.

---

## Related Repos

| Repo | Role |
|------|------|
| [`trading-assistant`](https://github.com/drasticstatic/trading-assistant) | Fortuna's primary workspace — futures trading |
| [`free-claude-code`](https://github.com/drasticstatic/free-claude-code) | Free-model proxy sandbox |
| [`my-template`](https://github.com/drasticstatic/my-template) | Shared repo scaffolding |

---

*Built by [drasticstatic](https://github.com/drasticstatic) · Powered by Anthropic's Claude · "Elf counsel meets all-seeing guardian."*
