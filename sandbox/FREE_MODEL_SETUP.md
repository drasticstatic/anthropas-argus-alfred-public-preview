# Free-Model Sandbox — Setup Guide

Alfred integrates [free-claude-code](https://github.com/drasticstatic/free-claude-code) as a local proxy that routes Claude Code CLI requests to free/open-source AI providers. This lets you keep working when Claude session tokens are exhausted.

**Sandbox location:** `~/code-forked/free-claude-code/`
**Config file:** `~/.config/free-claude-code/.env` (never committed)

---

## Supported Providers

| Provider | Notes |
|----------|-------|
| **NVIDIA NIM** | Free tier — high-quality inference (Llama, Nemotron, GLM, Mistral) |
| **DeepSeek** | Strong coding + reasoning, very low cost |
| **OpenRouter** | Multi-model aggregator — mix of free and paid |
| **Ollama** | Fully local — needs models pulled separately |
| **LM Studio** | Local GUI-based inference |
| **llama.cpp** | Raw local inference |

---

## Initial Setup (One-Time)

Prerequisites:
- `uv` — install from [astral.sh/uv](https://astral.sh/uv) if not present
- Python 3.14 — via Homebrew (`brew install python@3.14`) or [python.org](https://www.python.org/downloads/)
- Clone the repo: `git clone https://github.com/Alishahryar1/free-claude-code ~/code-forked/free-claude-code`
- Run `uv sync` inside the clone to create the virtual environment at `~/code-forked/free-claude-code/.venv`
- Run `uv run free-claude-code init` to scaffold the config at `~/.config/free-claude-code/.env`

**The only remaining step: add your API key for your chosen provider (see below).**

### Get a Free NVIDIA NIM API Key

1. Go to `https://build.nvidia.com` → sign in (NVIDIA account or Google/GitHub OAuth)
2. Navigate to **Settings → API Keys** (direct: `https://build.nvidia.com/settings/api-keys`)
3. Click **Generate Key** — copy the `nvapi-...` key
4. Open `~/.config/free-claude-code/.env` and set:
   ```
   NVIDIA_NIM_API_KEY="nvapi-your-key-here"
   ```

That's it. The proxy is ready to start.

---

## Starting the Proxy

```bash
cd ~/code-forked/free-claude-code
uv run free-claude-code
# Proxy starts on http://localhost:8082
```

## Launching Claude Through the Proxy

You need **two** env vars — `ANTHROPIC_BASE_URL` points Claude at the proxy, and `ANTHROPIC_API_KEY=freecc` sends the passphrase the proxy expects (matching `ANTHROPIC_AUTH_TOKEN="freecc"` in the proxy config):

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 ANTHROPIC_API_KEY=freecc claude
```

**Why both are needed:** Claude Code sends `ANTHROPIC_API_KEY` as its auth header. The proxy validates it against `ANTHROPIC_AUTH_TOKEN` in the config (`freecc`). Your real Anthropic key is never sent — the proxy uses your `NVIDIA_NIM_API_KEY` (or other provider key) to make the actual upstream call.

### Model Picker Issue (Workaround)

**Known issue:** Claude Code CLI's initial model picker doesn't display non-Anthropic models from the proxy, even though `/v1/models` returns them correctly.

**Workaround:** Specify the model directly when launching:

```bash
ANTHROPIC_BASE_URL=http://localhost:8082 ANTHROPIC_API_KEY=freecc claude --model anthropic/nvidia_nim/z-ai/glm4.7
```

**Important:** The `/model` command only shows the full list of models in sessions that were started WITHOUT `--model`. If you use `--model` to bypass the picker, `/model` will still only show Anthropic models.

**To see the full model list:** Start a session without `--model`, then run `/model` once you're in the session. The models will appear correctly.

**Common NVIDIA NIM models** (parent org prefix included — the proxy path always encodes it, so the org tells you at a glance who trained the model):
- `anthropic/nvidia_nim/z-ai/glm4.7` — Z.ai's GLM, general purpose, fast
- `anthropic/nvidia_nim/z-ai/glm5` — Z.ai's GLM — **Recommended** — best free-tier model for Claude Code tool-use
- `anthropic/nvidia_nim/z-ai/glm-5.1` — Z.ai's newest GLM; switch via `/model` (don't put in .env — causes boot crash)
- `anthropic/nvidia_nim/z-ai/glm-5.2` — Z.ai's latest GLM as of August 2026; strongest performer tested so far — see Battle Notes below
- `anthropic/nvidia_nim/meta/llama-3.3-70b-instruct` — Meta's Llama, reasonable quality, can hallucinate tool calls
- `anthropic/nvidia_nim/nvidia/llama-3.1-nemotron-70b-instruct` — NVIDIA's Nemotron (Llama-based), reasoning/coding
- `anthropic/nvidia_nim/nvidia/nemotron-3-ultra-550b-a55b` — NVIDIA's larger Nemotron; untested as of August 2026 (see Battle Notes)
- `anthropic/nvidia_nim/moonshotai/kimi-k2.5` — Moonshot AI's Kimi model
- `anthropic/nvidia_nim/moonshotai/kimi-k2.6` — Moonshot AI's newer Kimi — built the majority of the early `iamoneself` repo, performed well
- `anthropic/nvidia_nim/minimaxai/minimax-m2.5` — MiniMax AI's model
- `anthropic/nvidia_nim/minimaxai/minimax-m3` — MiniMax AI's newer model — see Battle Notes below
- `anthropic/nvidia_nim/databricks/dbrx-instruct` — Databricks' DBRX MoE; strong language + coding + RAG

Browse all models at [build.nvidia.com](https://build.nvidia.com/explore/discover).

---

## 🏆 Model Battle Notes (Tested June 2026)

It is a massive win that GLM was found deep down in the NVIDIA NIM pool. Here's what we've learned from live testing:

**GLM-5.1 startup quirk:** Putting `z-ai/glm-5.1` directly into the proxy's `.env` file on startup causes a hard crash. The proxy's local boot-up script uses explicit string matching that doesn't natively recognize GLM's unique name format — yet it allows the active runtime router to resolve it dynamically once the engine is already awake and listening. **Workaround:** Set a safe default (e.g. `glm4.7`) in `.env`, then switch to GLM-5.1 via `/model` after the session starts.

**Model comparison for Claude Code tool-use:**

| Model | Verdict | Why |
|-------|---------|-----|
| **z-ai/GLM (4.7 / 5.1 / 5.2)** | ✅ **Hidden gem** — recommended | Handles structured JSON logic exceptionally well. Easily picks up broken contexts and executes proper engineering tasks just like native Claude Sonnet or Opus. Best free-tier model for tool-use workflows, and the strongest performer across every round of testing so far. |
| **google/Gemma** | ❌ Problematic | Lacks specialized multi-turn tool-handling parameters required by Claude Code. Hallucinates raw file operations. |
| **meta/Llama 3.3 70B** | ⚠️ Mixed | Capable for structured tasks but does not match Claude for nuanced reasoning, complex tool use, or multi-step workflows. Can hallucinate file operations under pressure. |
| **deepseek-ai/DeepSeek / Claude Gateways** | ❌ Chokes on port 8082 | Frequently fail because they attempt complex native streaming protocols that the standard proxy translator struggles to parse. |
| **databricks/DBRX Instruct** | ⚠️ Untested (June 2026) | Mixture-of-Experts architecture. Strong on language understanding, coding, and RAG per Databricks benchmarks. Available on free NIM tier. May have lower free-tier capacity than GLM — caused cascading "Provider API request failed" errors when set as default MODEL. **Recommend:** Test via `/model` switch only, don't set as `.env` default. |

**Bottom line for NIM sessions:** GLM is the go-to. It recovers context well, handles tool calls properly, and stays on task. When the Anthropic subscription returns, use Sonnet/Opus for verification passes on NIM-generated work.

---

### 🏆 Model Battle Notes — Update (Tested August 2026)

**z-ai/GLM-5.2:** Remains the strongest NIM performer tested to date, consistent with the June 2026 verdict on earlier GLM versions below. Handles structured tool-use and multi-step engineering work with the least hand-holding of any free-tier model tried so far.

**moonshotai/Kimi-K2.6:** Built the majority of the early `iamoneself` repo in an earlier session and did great — a genuinely strong performer, not a fallback pick. Attempted again this session but only during a confirmed provider-side NIM outage (see below), so no fresh read either way this time; the earlier `iamoneself` result stands as the real data point.

**minimaxai/MiniMax-M3:** Usable, and got real work done — scaffolded a full new case-study card + modal in a live repo (structured Tailwind markup, tooltips, evidence galleries) during an outage when nothing else was getting through. Held conversational context well through a long, chaotic recovery. But it was the model that got through, not the best-performing one tested this session: its HTML output had several unclosed-tag errors that required a follow-up Sonnet pass to find and fix before the work was safe to commit. Christopher's framing captures the trade-off: "GLM-5.2 wins on absolute capability, complex reasoning, and heavy coding benchmarks, whereas MiniMax M3 wins significantly on speed, lower latency, and pricing efficiency." **Verdict: a real, deliberate speed/cost choice for scaffolding — not a quality equal to GLM-5.2.** Plan on a GLM-5.2 or Anthropic review pass after any MiniMax-M3 output that will ship publicly. See [[nim-model-pairing-strategy]] memory for the full hand-off pattern.

**nvidia/Nemotron-3-Ultra-550B-A55B:** Attempted only during the confirmed provider-side NIM outage (see below) — every request failed regardless of model, including this one. **Inconclusive, not a verdict on Nemotron.** Worth a clean retry once NIM is stable — flagged as a priority follow-up.

**GLM-5.2 discontinuation — unverified flag:** Christopher saw an indication GLM-5.2 may be discontinued around **August 24, 2026**. This is Christopher's own unconfirmed read, not independently verified by Alfred. **Re-check the `build.nvidia.com` model catalog before this date** and update this file with the actual outcome.

**One to try if the chance comes up: Sakana AI's Fugu.** Not yet available/tested on this NIM proxy setup as of August 2026 — noted here as a "would be curious" for a future battle-notes round, not a current recommendation.

**Every model has earned its keep, wins or misses included.** GLM's consistency, Kimi's early iamoneself build, MiniMax getting through a dead proxy, even Nemotron's inconclusive outage attempts — each one taught something about the free-tier landscape that the next session benefits from.

**⚠️ When every model fails at once, it's probably not you:** This session burned significant time cycling through `/model` and `/effort` combinations before realizing the outage was external. Two NVIDIA developer forum threads confirmed cascading 429/API-failure issues on the same day:
- https://forums.developer.nvidia.com/t/critical-performance-issues-with-nim-request-for-transparency/380606
- https://forums.developer.nvidia.com/t/api-error-429-help/376113

**Lesson:** if requests fail across multiple *different* models in the same session, check NVIDIA's developer forum before assuming local proxy/config is at fault. Cycling through unrelated models and effort levels burns time without fixing an upstream outage.

### 📌 `.env` Model Values Are Only a Cold-Start Default

The `.env` variables (`MODEL_OPUS`, `MODEL_SONNET`, `MODEL_HAIKU`, `MODEL`) only set the model a **brand-new session** boots with. Once a session is running, `/model` overrides them for that session, and Claude Code's own last-used-model memory means later sessions default to whichever model was last selected — not necessarily what's in `.env`. Editing `.env` alone will not change an already-running session's model, and won't even affect the next session if `/model` was used more recently than the `.env` edit. To force a specific model, use `/model` inside the session rather than relying on `.env` alone.

---

## ⚠️ Rate Limiting — Critical Config Note

**Do NOT set `PROVIDER_RATE_LIMIT` too low.** Claude Code's tool-use pattern fires multiple rapid API calls per turn (tool calls, reads, edits, searches). The proxy's defaults are:

```dotenv
PROVIDER_RATE_LIMIT=40      # requests per window
PROVIDER_RATE_WINDOW=60     # seconds
```

Setting `PROVIDER_RATE_LIMIT=1` and `PROVIDER_RATE_WINDOW=3` (1 request per 3 seconds) causes **constant "Provider rate limit reached" and "Provider API request failed" errors** because Claude Code easily exceeds that ceiling. The 40/60 defaults are safe for free-tier NIM usage.

**Symptoms of misconfigured rate limits:**
- Repeated "Provider rate limit reached" errors
- "Provider API request failed" on every other message
- Session appears to stall or loop on errors

**Fix:** Restore `PROVIDER_RATE_LIMIT=40` and `PROVIDER_RATE_WINDOW=60` in `~/.config/free-claude-code/.env`, then restart the proxy.

---

## Model Configuration

The default model is set in `~/.config/free-claude-code/.env`. NVIDIA NIM free tier recommended options:

```dotenv
# Good general-purpose default (lighter, fast)
MODEL="nvidia_nim/z-ai/glm4.7"

# Best quality on free NIM tier (Llama 3.3 70B)
MODEL="nvidia_nim/meta/llama-3.3-70b-instruct"

# Reasoning / coding tasks (Nemotron)
MODEL="nvidia_nim/nvidia/llama-3.1-nemotron-70b-instruct"
```

To route Claude's model tiers separately:
```dotenv
MODEL_OPUS="nvidia_nim/meta/llama-3.3-70b-instruct"
MODEL_SONNET="nvidia_nim/meta/llama-3.1-70b-instruct"
MODEL_HAIKU="nvidia_nim/z-ai/glm4.7"
```

---

## When to Use the Sandbox

The proxy works in **any repo** — just launch Claude Code with `ANTHROPIC_BASE_URL=http://localhost:8082 claude` from that directory. Fortuna, Alfred, any repo.

Common use cases:
- Claude session tokens running low — keep working with no interruption
- Getting a second perspective from a different model family (Llama, Mistral, Nemotron)
- Repetitive or bulk tasks (housekeeping, templating, research drafts)
- Testing prompts before committing Claude quota
- Comparing model outputs side by side

The proxy is a full backend swap for the session — the client (Claude Code) behaves identically.

---

## Security Notes

- Config binds to `localhost:8082` by default (not externally accessible)
- `~/.config/free-claude-code/.env` is never committed — keep `chmod 600`
- `ANTHROPIC_AUTH_TOKEN` in config is a local proxy secret, not your real Anthropic key
- Review upstream changes before merging: `git diff main upstream/main` in the repo

---

## Updating the Proxy

```bash
cd ~/code-forked/free-claude-code
git fetch upstream
git log upstream/main --oneline -10  # review changes
# If safe to merge:
git merge upstream/main
uv sync  # re-install deps if pyproject.toml changed
```

---

## 🔬 Research & Evaluation

**Verdict: SAFE TO USE (evaluated May 2026 by Alfred)**

| Check | Result |
|-------|--------|
| Network exposure | ✅ Binds to `localhost:8082` only — not externally accessible |
| Credential handling | ✅ Real Anthropic key never sent to proxy; passphrase (`freecc`) is a local proxy secret only |
| Config storage | ✅ `~/.config/free-claude-code/.env` — local only, never committed |
| Upstream provenance | ✅ Fork of `Alishahryar1/free-claude-code` — actively maintained, reviewed before merging |
| API key exposure | ✅ NVIDIA NIM key in `.env` stays local; no writes to disk beyond config |
| Subprocess calls | ✅ No `shell=True` patterns in core proxy code |
| Proxy behavior | ✅ Full backend swap — Claude Code client behavior is identical; no prompt interception |

**What it does:** Routes Claude Code CLI requests to free/open-source LLM providers (primarily NVIDIA NIM free tier) via a local proxy at `localhost:8082`. The Claude Code client is unaware of the swap — all prompts, tool use, and responses pass through identically. Real Anthropic key is never used; the proxy substitutes your NIM or other provider key upstream.

**Key limitation:** Model quality varies significantly from Claude. NIM free-tier models (Llama 3.3 70B, GLM 4.7) are capable for structured tasks but do not match Claude Opus/Sonnet for nuanced reasoning, complex tool use, or multi-step workflows. Plan accordingly.

---

## 🎯 Fortuna / Trading-Assistant Use Cases

**Rule:** NIM is for non-live, non-account work only. Live trading decisions, prop firm account actions, and anything that affects real money are always Anthropic.

### ✅ Where NIM Does a Genuinely Good Job (Fortuna) — Anthropic Still Takes the Final Pass

NIM models (GLM especially) handle these well on their own — this isn't "NIM as a lesser substitute," it's real, usable output. Anthropic still comes in for the final quality/judgment pass before anything ships, because that pass is worth having on this kind of work regardless of how well the NIM draft turned out.

| Task | Why NIM Handles It Well |
|------|---------------|
| Draft markdown reviews from raw notes | Structured templating — model quality less critical |
| Research and summarize strategy documentation | Reading + summarizing, not reasoning |
| Bulk housekeeping (rename files, update headers, pattern find/replace) | Mechanical tasks |
| First-pass weekly review drafts | Anthropic does final wrap and quality pass |
| Historical trade data formatting | CSV → table → markdown — no judgment calls |
| Skills and spec documentation drafts | Template-filling work |
| PENDING-TASKS.md cleanup and organization | Administrative |

### ❌ Not NIM-Eligible (Always Anthropic)

| Task | Why Anthropic Only |
|------|-------------------|
| Live session decisions (entry, SL, TP, position sizing) | Real money — no exceptions |
| Trade reviews (final, publication-ready) | Coaching-grade analysis requires full reasoning |
| Prop firm account status and progression | Account-critical accuracy |
| Pattern Tracker updates | Behavioral continuity requires judgment |
| Any action that triggers a git push to a live coaching channel | Irreversible external action |

### Handoff Pattern (NIM → Anthropic)

When using NIM for drafts:
1. NIM session writes draft to a temp file or stdout
2. Close NIM session — do NOT switch models mid-session (re-prices context, drains quota)
3. Open fresh Anthropic session → read draft → quality-wrap into final format

Log NIM sessions as `session_YYYYMMDD_nvidia.md` in `logs/fortuna/YYYY/MM-Mon/`.

---

*Evaluated by Alfred · May 2026 · See `sandbox/GRAPHIFY_SETUP.md` for graphify equivalent*
