# Free-Model Sandbox — Setup Guide

Alfred integrates [free-claude-code](https://github.com/drasticstatic/free-claude-code) as a local proxy that routes requests to free/open-source AI providers using Claude's API format.

**Sandbox location:** `/code/free-claude-code/`

---

## Supported Providers

| Provider | Notes |
|----------|-------|
| **NVIDIA NIM** | Free tier — high-quality inference for Llama, Mistral, etc. |
| **DeepSeek** | Strong coding + reasoning model, very low cost |
| **OpenRouter** | Multi-model aggregator — mix of free and paid |
| **Ollama** | Fully local — needs models downloaded separately |
| **LM Studio** | Local GUI-based inference |
| **llama.cpp** | Raw local inference |

---

## Initial Setup

```bash
cd /code/free-claude-code

# Install uv if not present
curl -LsSf https://astral.sh/uv/install.sh | sh

# Initialize the config (creates ~/.config/free-claude-code/.env)
fcc-init

# Or manually: copy and edit the example
cp .env.example .env
# Edit .env with your provider API keys (NVIDIA_NIM_API_KEY, OPENROUTER_API_KEY, etc.)
chmod 600 .env
chmod 600 ~/.config/free-claude-code/.env  # if using global config
```

---

## Running the Proxy

```bash
cd /code/free-claude-code
uv run free-claude-code
# Proxy starts on http://0.0.0.0:8082
```

To point Claude Code CLI at the proxy:
```bash
ANTHROPIC_BASE_URL=http://localhost:8082 claude
```

---

## Security Notes (from audit)

- Proxy binds to `0.0.0.0:8082` by default — restrict to `127.0.0.1` if on shared/untrusted network
- `ENABLE_WEB_SERVER_TOOLS` defaults to `False` — leave off unless needed
- Keep `.env` permissions tight: `chmod 600`
- Review upstream changes before `git merge upstream/main`

---

## Comparing with Upstream

```bash
cd /code/free-claude-code
git fetch upstream
git log upstream/main --oneline -10  # see what's new
git diff main upstream/main           # compare before deciding to merge
```
