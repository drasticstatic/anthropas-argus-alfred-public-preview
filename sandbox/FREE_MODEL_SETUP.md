# Free-Model Sandbox — Setup Guide

Alfred integrates [free-claude-code](https://github.com/drasticstatic/free-claude-code) as a local proxy that routes Claude Code CLI requests to free/open-source AI providers. This lets you keep working when Claude session tokens are exhausted.

**Sandbox location:** `/Users/christopherwilson/code-forked/free-claude-code/`
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

Prerequisites are already satisfied:
- `uv` ✅ installed at `~/.local/bin/uv`
- Python 3.14 ✅ installed via Homebrew
- Virtual environment ✅ created at `/code-forked/free-claude-code/.venv`
- Config scaffold ✅ created at `~/.config/free-claude-code/.env`

**The only remaining step: add your NVIDIA NIM API key.**

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
