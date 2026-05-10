---
name: session-sync
description: >
  Run the full mid-session or end-of-work sync routine: stage and commit all outstanding work, push to remote, update HANDOFF.md + AGENT_SYNC.md with cross-agent handoff notes, and create or append the session log. Use when: "session sync", "sync everything", "commit and sync", "push and log", "update agent sync", "end of work", "sync before I go", "commit all changes", "log session", or wanting to save progress without a full session close. Do NOT use for: single-file commits, or when there is nothing to commit.
---

# Skill: /session-sync — Anthropas-Argus-Alfred

Execute the complete sync routine: stage outstanding work, commit, push to remote, update HANDOFF.md + AGENT_SYNC.md, and create or append the session log.

---

## Step 1 — Check Status and Stage Work

If code files were modified this session, update the knowledge graph first (AST-only, no API cost):

```bash
graphify update .
```

Then stage:

```bash
git status
git add AGENTS.md CLAUDE.md README.md PENDING-TASKS.md HANDOFF.md AGENT-SYNC/ sandbox/ logs/ .claude/skills/ specs/ graphify-out/
```

Stage only what was actually changed — no need to add everything if it's clean.

---

## Step 2 — Commit

```bash
git commit -m "$(cat <<'EOF'
Brief description of what was done

- What changed
- Why it changed

Co-Authored-By: Alfred · Claude · claude-sonnet-4-6 <noreply@anthropic.com>
EOF
)"
```

Adjust `Co-Authored-By` to the actual agent and model:
- Alfred-Anthropic (Sonnet): `Alfred · Claude · claude-sonnet-4-6 <noreply@anthropic.com>`
- Alfred-Anthropic (Opus): `Alfred · Claude · claude-opus-4-7 <noreply@anthropic.com>`
- Alfred-NIM: `Alfred · Claude · NVIDIA NIM Z-AI GLM-4.7 <noreply@anthropic.com>`

---

## Step 3 — Push

```bash
git push origin main
```

If push fails (remote ahead):
```bash
git pull --rebase origin main && git push origin main
```

---

## Step 4 — Update HANDOFF.md + AGENT_SYNC.md

**HANDOFF.md** (repo root) — primary pickup file for NIM sessions; keep it current and concise:
- What was done this session
- What's open / next steps
- Any model-switch context if applicable

**Append to `AGENT-SYNC/AGENT_SYNC.md`:**

```markdown
## 📡 [Month Day, Year] Session Summary ([Alfred-Anthropic / Alfred-NIM])
**Session type:** [infrastructure / housekeeping / research / cross-repo]
- [What was completed]
- [Key decision or outcome]
- Commit: [short hash] — [message]
```

Write a `AGENT-SYNC/created-by-alfred/prompts/YYYY/MM-Mon/[RECIPIENT]_PROMPT_YYYYMMDD.md` if another agent needs a handoff brief.

---

## Step 5 — Backup Session JSONL (if small enough)

```bash
PROJ="$HOME/.claude/projects/-Users-christopherwilson-code-anthropas-argus-alfred"
LATEST=$(ls -t "$PROJ"/*.jsonl 2>/dev/null | head -1)
SIZE=$(stat -f%z "$LATEST" 2>/dev/null)
[ -n "$LATEST" ] && [ "$SIZE" -lt 20971520 ] && \
  cp "$LATEST" AGENT-SYNC/app-data-claude/ && \
  echo "✓ Backed up $(basename $LATEST)" || \
  echo "⚠ JSONL too large (>20MB) or not found — stays at source only"
```

---

## Step 6 — Session Log

Path: `logs/alfred/YYYY/MM-Mon/session_YYYYMMDD_{anthropic|nvidia}.md`

Alfred session logs **are committed** to the private alfred repo.

```markdown
## YYYY-MM-DD — [session type: housekeeping / research / infrastructure / cross-repo]
- [What was accomplished]
- [Key decision or outcome]
- Commit: [short hash] — [message]
```

---

## Step 7 — Final Push

```bash
git add AGENT-SYNC/ HANDOFF.md logs/
git commit -m "Update agent sync and session log [date]

Co-Authored-By: Alfred · Claude · claude-sonnet-4-6 <noreply@anthropic.com>"
git push origin main
```

---

## NIM Session Notes

Alfred-NIM reads `HANDOFF.md` first (compact, ~40 lines) as the primary pickup doc. If the full session context is needed, Alfred-NIM can also read the session log. The `/sessions` data-dump pattern (exporting the Claude session transcript to NIM) is a valid shortcut — NIM will process it even as a large doc.

**Model-switching note:** The `/model` switch itself consumes minimal tokens, but causes the existing context to re-price under the new model, accelerating how quickly remaining quota drains. Preferred pattern: write HANDOFF.md + AGENT_SYNC.md → open a fresh NIM session reading just the handoff doc. Mid-session switching is an option for quick data-dumps but burns quota faster.

---

## What NOT to do

- Never commit `.env` files, credentials, or wallet/key files
- Don't skip HANDOFF.md — it's how Alfred-NIM picks up without re-reading the full session
- Don't skip AGENT_SYNC.md — it's how all agents stay aligned between sessions
