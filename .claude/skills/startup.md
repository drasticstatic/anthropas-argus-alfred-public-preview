# Startup — Model Choice

**Purpose:** Choose your session backend (Anthropic Claude or free-model sandbox) before starting work.

---

## Step 0 — Model Choice

Check proxy status and choose your session backend:

```bash
curl -s http://localhost:8082/v1/models
```

**If you see "Missing API key" (401):** Proxy is UP ✅  
**If you see "Connection refused":** Proxy is DOWN — start it:

```bash
cd ~/code-forked/free-claude-code && nohup uv run free-claude-code > /tmp/fcc.log 2>&1 &
```

---

## Choose Your Backend

**[1] Anthropic Claude** — Full context, highest quality, uses quota  
**[2] NVIDIA NIM** — Free, fast, GLM-4.7 (default)  
**[3] DeepSeek** — Free, strong reasoning  
**[4] OpenRouter** — Free, multiple models available

---

## If You Chose [2], [3], or [4]

Launch with the proxy:

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 ANTHROPIC_API_KEY=freecc claude
```

**Note:** You'll see an "auth conflict" warning — this is harmless. Ignore it.

Once the session opens, run `/model` and select:
- `anthropic/nvidia_nim/z-ai/glm4.7` (NVIDIA NIM default)
- Or your preferred model from the picker

---

## If You Chose [1] or Proxy Already Running

Proceed normally — no restart needed.

---

## Proxy Health Check

```bash
curl -s http://localhost:8082/v1/models
# 401 = proxy UP (healthy)
# Connection refused = proxy DOWN
```

Log file: `/tmp/fcc.log`
