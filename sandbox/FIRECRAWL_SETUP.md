# Firecrawl — Setup, Security Review & Integration Plan
#### Evaluated by Alfred (Anthropas-Argus-Alfred) · May 12, 2026

> **What this is:** Pre-install security review + complete setup guide + ecosystem integration plan for the Firecrawl stack:
> - `github.com/firecrawl/firecrawl` — core scraping engine + SDKs
> - `github.com/firecrawl/firecrawl-mcp-server` — MCP server (the piece we install)
> - `github.com/firecrawl/cli` — optional CLI tool

---

## 🔬 Research & Evaluation

**Verdict: SAFE TO INSTALL (high confidence)**

| Check | Result |
|-------|--------|
| Install hooks (pre/post) | ✅ None — `prepare` script runs TypeScript build only (standard for published packages) |
| Credential harvesting | ✅ Clean — reads only `FIRECRAWL_API_KEY` and `FIRECRAWL_API_URL` from env; `dotenv.config()` reads local `.env` (standard practice) |
| Unexpected env access | ✅ No reads of `~/.ssh`, `~/.aws`, `~/.config`, or system credential paths |
| Outbound HTTP calls | ✅ All calls route through `FirecrawlApp` client to Firecrawl endpoints only — no other domains |
| Code execution risks | ✅ No `eval()`, `Function()`, or dynamic code execution found in MCP server source |
| File system writes | ✅ None outside normal operation; `readFile` is imported but unused (minor, not a concern) |
| Provenance (MCP server) | ✅ 6.3k stars · 718 forks · 270 commits · v3.15.0 · MIT license · maintained by Firecrawl org |
| Provenance (core repo) | ✅ 119k stars · 7.4k forks · AGPL-3.0 (SDKs are MIT) · last release v2.9.0 April 2026 |
| Typosquatting | ✅ Package name is `firecrawl-mcp` — matches GitHub repo exactly, no lookalikes |
| Telemetry | ⚠️ CLI collects anonymous usage data by default — disable with `FIRECRAWL_NO_TELEMETRY=1` |
| CLI init behavior | ⚠️ `firecrawl-cli init` auto-writes MCP config to Claude Code, Cursor, Windsurf — skip this; configure manually below |

**One flag worth noting:** The CLI's `init -y --browser` command auto-configures MCP settings across all detected editors. That's convenient but opaque — we configure the MCP manually instead so we know exactly what gets written where.

### What It Does

Firecrawl converts any URL (or entire website) into clean, LLM-ready markdown. Unlike a raw `fetch`, it handles JavaScript-rendered content via headless browser, strips nav/footer/cookie-banner noise, respects `robots.txt`, and can crawl an entire site map in one call.

**MCP tools exposed to Claude Code:**

| Tool | What it does |
|------|-------------|
| `firecrawl_scrape` | Single URL → clean markdown or JSON |
| `firecrawl_batch_scrape` | List of URLs → batch extraction |
| `firecrawl_crawl` | Full site crawl (respects depth limits, sitemap) |
| `firecrawl_check_crawl_status` | Poll async crawl job |
| `firecrawl_map` | Discover all indexed URLs on a site |
| `firecrawl_search` | Web search + optional content extraction |
| `firecrawl_extract` | Structured data extraction with JSON schema |
| `firecrawl_agent` | Autonomous research agent for complex queries |

### Pricing Reality for Our Usage

| Tier | Cost | Credits | dpnelson crawl cost |
|------|------|---------|---------------------|
| **Free** | $0/mo | 1,000/mo | ~20 credits (whole site) |
| Hobby | $16/mo | 5,000/mo | — |

A full dpnelson.com crawl (all ~20 pages) costs 20 credits. The free tier (1,000/month) comfortably covers all our projects. We won't need a paid plan unless doing high-volume batch scraping.

---

## 🛠️ Install & Setup

### Step 1 — Get API Key (free, no credit card)

1. Go to `firecrawl.dev` → Sign Up → Dashboard → API Keys
2. Create a key — copy it
3. Add it to your shell environment (persisted across sessions):
   ```bash
   # Add to ~/.zshrc (run once)
   echo 'export FIRECRAWL_API_KEY="fc-your-key-here"' >> ~/.zshrc
   source ~/.zshrc
   ```

### Step 2 — Configure MCP Server Globally in Claude Code

> **⚠️ Correct file: `~/.claude.json`, NOT `~/.claude/settings.json`**
>
> `settings.json` controls permissions, theme, and effort level — Claude Code does **not** load `mcpServers` from it. User-level MCPs must go in `~/.claude.json` under the top-level `"mcpServers"` key. Putting them in `settings.json` silently does nothing — the server won't appear in `/mcp` and won't be available as tools. (Discovered May 12, 2026 — settings.json had been wrong since initial setup.)

Add the MCP server to `~/.claude.json` under the top-level `"mcpServers"` key (alongside any existing entries like `auggie`):

```json
{
  "mcpServers": {
    "firecrawl": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "firecrawl-mcp"],
      "env": {
        "FIRECRAWL_API_KEY": "fc-your-key-here"
      }
    }
  }
}
```

> **Note:** `~/.claude.json` is a large JSON file managed by Claude Code — edit carefully and only touch the `mcpServers` block. The `"type": "stdio"` field is required here (unlike `settings.json` which doesn't use it).

No global npm install needed — `npx -y firecrawl-mcp` fetches and runs it on demand, always at the latest version.

### Step 3 — Verify in a Claude Code Session

After adding the config, **fully restart Claude Code** (close and reopen — a new conversation is not enough; the MCP server spawns at CLI launch). The `firecrawl_scrape` and related tools will appear in `/mcp` as connected. Test with:

```
Use firecrawl_scrape to fetch https://dpnelson.com and return the clean markdown.
```

### Optional — CLI (for running crawls outside Claude sessions)

```bash
npm install -g firecrawl-cli

# Disable telemetry
export FIRECRAWL_NO_TELEMETRY=1

# Authenticate (uses your API key)
firecrawl login

# Example: crawl dpnelson.com → markdown files
firecrawl crawl https://dpnelson.com --output ./crawl-output/
```

Install this only if you want to run crawls directly from your terminal. The MCP server (Step 2) covers everything Alfred needs from inside Claude Code.

---

## 📋 Ecosystem Integration Plan

### Immediate — dpnelson (this session)

With the MCP wired, the first job is a full dpnelson.com crawl:

```
# One call replaces ~15 sequential WebFetch calls
firecrawl_crawl: https://dpnelson.com
  → all blog posts (full text, not excerpts)
  → all service pages
  → about, coaching-vs-therapy, models-frameworks, resources
  → real Calendly URL embedded in WordPress source
  → any content missed in manual scrape
```

**Credit cost:** ~20–25 credits (the whole site). Well within free tier.

### Ongoing — All Projects

| Project | Use case | Est. credits/month |
|---------|----------|--------------------|
| `dpnelson` | Sync blog content, check for new posts | ~5–10 |
| `pir-devine-news` | Scrape source articles for the news pipeline | ~20–50 |
| `trading-assistant` | Fetch financial docs, strategy articles | ~10–30 |
| `divorce-custody-assistant` | Research legal references | ~5–10 |
| **Total** | | ~40–100/mo — free tier headroom |

### Self-Hosted Option (future)

If usage grows past the free tier, Firecrawl is fully self-hostable via Docker. The MCP server supports `FIRECRAWL_API_URL` to point at a local instance — no code changes, just swap the env var.

---

## 📊 Effectiveness Metrics

Measured on first full dpnelson.com crawl (May 12, 2026) vs. prior WebFetch baseline.

### Coverage

| Method | Pages fetched | Blog posts (full text) | Sub-pages | Missed content |
|--------|--------------|----------------------|-----------|----------------|
| WebFetch (manual, 4 calls) | 4 / ~20 | 0 / 12 | 0 / 8 | Client journey, coaching vs therapy, models, resources, contact, all blog bodies, real Calendly URL |
| Firecrawl crawl (1 call) | — | — | — | _fill after first run_ |

### Output Quality

| Dimension | WebFetch | Firecrawl |
|-----------|----------|-----------|
| Nav/footer noise stripped | ⚠️ Inconsistent | ✅ Purpose-built |
| JS-rendered content | ⚠️ Sometimes missed | ✅ Headless browser |
| Exact copy fidelity | ⚠️ AI may paraphrase | ✅ Raw markdown |
| Structured extraction (JSON schema) | ❌ | ✅ |
| Multi-page in one call | ❌ | ✅ |

### Token Efficiency

_Fill after first crawl — compare token count of WebFetch responses (AI-summarized) vs. Firecrawl markdown output for same URLs._

| URL | WebFetch tokens (approx) | Firecrawl tokens (approx) | Delta |
|-----|-------------------------|--------------------------|-------|
| dpnelson.com (homepage) | — | — | — |
| /about-douglas/ | — | — | — |
| /fallow/ (blog post) | n/a — not fetched | — | — |

### Credit Cost

| Operation | Credits used | Free tier remaining |
|-----------|-------------|-------------------|
| Full site crawl (first run) | — | — |
| Monthly maintenance crawl | — | — |

_Update this table after each significant crawl._

---

## 🔒 Security Notes

- **API key scope:** The Firecrawl API key only grants access to Firecrawl's scraping service — it cannot access your Drive, email, or any other account. Low blast radius if exposed.
- **Where to store:** In `FIRECRAWL_API_KEY` env var in `~/.zshrc` (or in the MCP server's `env` block in `~/.claude.json`). Never in `.env` files committed to any repo.
- **MCP server runtime:** Runs as a subprocess spawned by Claude Code — isolated to its own process, communicates only with Firecrawl's API.
- **Telemetry:** CLI sends anonymous usage data; disable with `FIRECRAWL_NO_TELEMETRY=1`. The MCP server package itself has no documented telemetry.
- **readFile import (unused):** The MCP server source imports `readFile` but doesn't call it — no active file access. Worth re-checking on major version upgrades.

---

*Evaluated by Alfred · anthropas-argus-alfred · May 12, 2026*
