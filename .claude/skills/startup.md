---
name: alfred-startup
description: >
  Use at the start of any Alfred session to choose the session backend (Anthropic Claude or
  free-model sandbox) and verify proxy status. TRIGGER when: "startup", "start session",
  "good morning", "let's get started", "session start", "pick a model", "which model",
  "check proxy", "begin work", "new session", "morning", "start work", "initialize",
  "boot up", "which backend", or any phrase indicating Alfred is beginning a new work
  session or wants to select a model backend. Do NOT use for: mid-session model switches
  (use /model directly), Fortuna sessions (Fortuna is always Anthropic Claude — never NIM
  for live trading).
---

# Alfred Startup

Choose your session backend before starting work. Alfred runs in two modes — same identity, different engine.

## Check Proxy Status

Before choosing a backend, verify the free-model proxy:

```bash
curl -s http://localhost:8082/v1/models
```

**401 "Missing API key"** → Proxy is UP ✅  
**Connection refused** → Proxy is DOWN — start it:

```bash
cd ~/code-forked/free-claude-code && nohup uv run free-claude-code > /tmp/fcc.log 2>&1 &
```

Wait ~4 seconds, then re-check with the curl command above.

---

## Graph Orientation (if graphify installed)

If `graphify-out/GRAPH_REPORT.md` exists, read it before opening any files or running searches — god nodes and community structure orient the session faster than file-by-file exploration.

```bash
# Quick check
[ -f graphify-out/GRAPH_REPORT.md ] && echo "graph present" || echo "no graph"
```

Use `graphify query "your question"` for cross-file questions. The PreToolUse hook fires automatically before grep/find, but reading the report at session open is faster.

---

## Choose Your Backend

**[1] Alfred-Anthropic** — Anthropic Sonnet/Opus. Full quality, full context, uses quota.

**[2] Alfred-NIM** — NVIDIA NIM (free). GLM-4.7 default. For exploratory questions, research, drafts.

**[3] Alfred-DeepSeek** — DeepSeek (free, strong reasoning/coding). Via proxy.

**[4] Alfred-OpenRouter** — OpenRouter (free, multiple models). Via proxy.

### Choosing [1]: Alfred-Anthropic

Proceed normally. This is the default for anything in trading-assistant or live environments.

### Choosing [2], [3], or [4]: Free Models via Proxy

Launch with base URL override:

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 ANTHROPIC_API_KEY=freecc claude
```

**Expected behavior:**
- An "auth conflict" warning will appear — **harmless, ignore it**. Claude Code detects both subscription credentials and a custom base URL; the override takes precedence for routing.
- Session opens on Sonnet 4.6 (claude.ai auth wins) — run `/model` → select your preferred model

### Model Selection

Once inside a free-model session, use `/model` to choose:
- **`anthropic/nvidia_nim/z-ai/glm4.7`** — Fast, general-purpose (confirmed working)
- **DeepSeek variants** — Strong reasoning and coding
- **OpenRouter models** — Access to multiple providers through one endpoint

---

## Free Model Session Notes

### Image Support
**Text only** — proxy rejects image blocks with error 400. For vision work, use Alfred-Anthropic.  
If the error fires on every message (session jammed): an image block is stuck in context — **restart the session**.

### Error Recovery
- **"peer closed connection" / "Provider API request failed"** — transient errors. Tell the agent to continue working; these self-resolve on retry.

### Context Management
- Smaller context window than Claude — read large files in sections, not all at once
- Use `HANDOFF.md` for session pickup, not the full session log
- Prefer targeted reads over workspace-wide scans

---

## Fortuna Sessions (Trading)

**Always use Alfred-Anthropic for Fortuna** — never NIM or free models.  
Live trading requires full reliability, context, and quality. Free models are for exploration and research only.

---

## Reference

Full dual-mode philosophy, NIM error protocol, proxy launch details: `specs/alfred-workflow.md`
