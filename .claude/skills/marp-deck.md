---
name: marp-deck
description: >
  Use to generate a Marp slide deck from any document, briefing, or research file. TRIGGER
  when: "create a deck for", "make slides from", "marp this", "turn [doc] into slides",
  "shareable deck", "Marp version of", "slide summary", "presentation from". Do NOT use
  for: creating the underlying document (build that first), or when the user wants a
  formatted markdown export rather than slides.
---

# Skill: /marp-deck — Alfred

Convert any document into a Marp slide deck and generate the HTML output. Alfred's decks are
general-purpose — use this for briefings, overviews, agent documentation, and ecosystem
explainers. Each repo has its own visual theme as the ecosystem grows.

## Quick Command

```bash
# Generate HTML from any .marp.md file
~/.nvm/versions/node/v22.12.0/bin/marp [path/to/file].marp.md -o [path/to/file].marp.html
```

---

## Repo Theme Reference

Each repo in the ecosystem has its own Marp identity:

| Repo | Theme | Palette |
|------|-------|---------|
| **Alfred** (this repo) | Clean, minimal, neutral — coordinator voice | `#0f172a` navy · `#38bdf8` sky · `#f8fafc` white |
| **trading-assistant** | Dark professional — canonical case study | Already built; linked externally |
| **pir-devine-news** | Dark navy/green — PIR brand | `#000814` · `#00d082` · `#7c3aed` |
| **divorce-custody** | Professional legal — court-appropriate | `#f8f9fa` white · `#1a3a5c` navy · `#2c5f8a` steel |

---

## Alfred Color Theme

Clean, minimal, coordinator-appropriate — trustworthy, not flashy:

```markdown
---
marp: true
theme: default
paginate: true
backgroundColor: '#0f172a'
color: '#f1f5f9'
style: |
  h1 { color: #38bdf8; border-bottom: 2px solid #38bdf8; padding-bottom: 10px; }
  h2 { color: #7dd3fc; margin-bottom: 0.4em; }
  h3 { color: '#94a3b8'; }
  strong { color: #38bdf8; }
  em { color: #7dd3fc; font-style: normal; }
  code { background: #1e293b; color: #38bdf8; padding: 2px 6px; border-radius: 3px; }
  pre { background: #0f1a2a; border: 1px solid #1e3a5f; border-radius: 6px; padding: 1em; }
  table { border-collapse: collapse; width: 100%; font-size: 0.85em; }
  th { background: #1e293b; color: #38bdf8; padding: 6px 12px; }
  td { border: 1px solid #1e3a5f; padding: 5px 12px; background: '#0f172a'; color: #f1f5f9; }
  blockquote { border-left: 4px solid #7dd3fc; padding-left: 1em; color: #94a3b8; font-style: italic; }
  section { font-family: 'Inter', system-ui, sans-serif; }
---
```

---

## Document Types Alfred Produces

| Source | Deck Type | Key Slides |
|--------|-----------|-----------|
| Agent ecosystem overview | Architecture deck | Cover, agent roles, workflow, how to contribute |
| Skill documentation | Reference deck | Cover, skill list, trigger examples, version control rules |
| Research summary | Briefing deck | Cover, question, findings, recommendation |
| Session recap | Handoff deck | Cover, what changed, next steps, open items |

---

## Step 1 — Create the .marp.md File

```markdown
---
marp: true
theme: default
paginate: true
backgroundColor: '#0f172a'
color: '#f1f5f9'
style: |
  [paste Alfred Color Theme style block above]
---

<!-- Cover slide -->
# [Title]
**Alfred — System Coordinator**

*[Date] · [Brief subtitle]*

---

<!-- Context or overview slide -->
## Overview

[3-4 bullet points — high level]

---

<!-- Main content -->
## [Section]

[Content]

---

<!-- Closing -->
## Next Steps

- [Action 1]
- [Action 2]

*Questions? — [contact or repo link]*
```

---

## Naming Convention

```
[topic]-[YYYYMMDD].marp.md
[topic]-[YYYYMMDD].marp.html
```

**Location:**
- `setup/` — public-facing ecosystem docs (synced via gitexporter)
- `specs/` — private briefings (excluded from public preview)

---

## After Completing

1. Verify HTML renders in browser
2. Commit both `.marp.md` and `.marp.html` if going to `setup/`
3. For `specs/` decks — local only or private sync

---

## Reference

- Marp binary: `~/.nvm/versions/node/v22.12.0/bin/marp`
- Quick syntax: `.claude/skills/marp-quick-reference.md`
- Alfred palette: navy `#0f172a` · sky `#38bdf8` · slate `#94a3b8`
- PIR palette: `pir-devine-news/.claude/skills/marp-deck.md`
- Legal palette: `divorce-custody-assistant/.claude/skills/marp-deck.md`
- Canonical public example: `trading-assistant/setup/create-skill.marp.html`
