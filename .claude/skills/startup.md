---
name: startup
description: >
  Use at the start of any Alfred session to choose the session backend (Anthropic Claude or
  free-model sandbox) and verify proxy status. TRIGGER when: "startup", "start session",
  "good morning", "let's get started", "session start", "pick a model", "which model",
  "check proxy", or any phrase indicating Alfred is beginning a new work session.
  Do NOT use for: mid-session model switches (use /model directly), Fortuna sessions
  (Fortuna is always Anthropic Claude — never NIM for live trading).
---

# Skill: /startup

Choose your session backend before starting work. Alfred runs in two modes — same identity, different engine.

## Step 0 — Check Proxy Status

```bash
curl -s http://localhost:8082/v1/models
```

**401 "Missing API key"** → Proxy is UP ✅ skip to Choose Your Backend  
**Connection refused** → Proxy is DOWN — start it:

```bash
cd ~/code-forked/free-claude-code && nohup uv run free-claude-code > /tmp/fcc.log 2>&1 &
```

Wait ~4 seconds, then re-check.

---

## Choose Your Backend

**[1] Alfred-Claude** — Anthropic Sonnet/Opus. Full quality, full context, uses quota.  
**[2] Alfred-NIM** — NVIDIA NIM GLM-4.7 (free). For exploratory questions, research, drafts.  
**[3] Alfred-DeepSeek** — DeepSeek (free, strong reasoning/coding). Via proxy.  
**[4] Alfred-OpenRouter** — OpenRouter (free, multiple models). Via proxy.

**Choosing [1]:** Proceed normally. This is the default for anything in trading-assistant or live environments.

**Choosing [2], [3], or [4]:**

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 ANTHROPIC_API_KEY=freecc claude
```

An "auth conflict" warning will appear — **harmless, ignore it**. Once inside, run `/model` → select your preferred model (`anthropic/nvidia_nim/z-ai/glm4.7` for NIM).

---

## NIM Session Notes

- **Text only** — no images (proxy rejects image blocks with error 400)
- **If error jams session** (fires on every message): restart the session — an image block is stuck in context
- **"peer closed connection" / "Provider API request failed"**: transient errors — tell the agent to continue working
- **Smaller context window**: read large files in sections; use `HANDOFF.md` not the full session log
- Full NIM reference: `specs/alfred-workflow.md`
