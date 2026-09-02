# Graphify — Setup, Security Review & Integration Plan
#### Evaluated by Alfred (Anthropas-Argus-Alfred) · May 10, 2026

> **What this is:** Pre-install security review + complete setup guide + ecosystem integration plan for `github.com/safishamsi/graphify`.

> **Related:** the Integration Plan below references several repos by name across `dappu/`,
> `code/`, and Intent workspace paths — see [`INTENT_WORKTREE_LEGEND.md`](INTENT_WORKTREE_LEGEND.md)
> for the full key mapping of Intent workspace slugs → repo → branch → spec-note location →
> manual-clone counterpart, so you don't have to hold that mapping in memory.

---

## 🔬 Research & Evaluation

**Verdict: SAFE TO INSTALL (95% confidence)**

| Check | Result |
|-------|--------|
| Install hooks / setup scripts | ✅ None — standard setuptools, no `shell=True` subprocess calls |
| Credential harvesting | ✅ Clean — no reads from `~/.ssh`, `~/.aws`, `.env`, or system paths |
| API keys | ✅ Environment variables only — never written to disk |
| Provenance | ✅ 45.7k stars · 5k forks · 349 commits · Safi Shamsi + 15 active contributors |
| Typosquatting | ✅ PyPI package is `graphifyy` (double-y) — explicitly documented in README |
| Network calls | ✅ None during extraction — LLM calls only when explicitly triggered; SSRF-hardened URL validation |
| License | ✅ MIT |
| Recent security work | ✅ May 2026: F-series fixes, YAML injection + XSS hardening, V-003 vulnerability fix |

**Minor caveat:** 40+ tree-sitter transitive dependencies — minimal risk but inherent in the package surface area.

### What It Does

Builds a knowledge graph from the codebase using tree-sitter AST (code files) and LLM vision (docs, PDFs, images). Reported **71.5x fewer tokens per query** vs raw file reading.

**Output — `graphify-out/` directory:**

| File | Commit? | Notes |
|------|---------|-------|
| `graph.json` | ✅ Yes | Full graph data — queryable |
| `GRAPH_REPORT.md` | ✅ Yes | Key concepts, god nodes, architectural suggestions |
| `graph.html` | ✅ Yes | Interactive visualization |
| `manifest.json` | ❌ No | File mtimes — breaks after git clone |
| `cost.json` | ❌ No | Local API cost log only |
| `cache/` | Optional | Semantic extraction cache — commit for speed, omit to keep repo lean |

**Claude Code hook:** `graphify claude install` adds a pre-search hook to `.claude/settings.json` — before any grep/find, Claude reads `GRAPH_REPORT.md` to orient by architecture.

---

## 🛠️ Install & Setup

```bash
# Install globally with all LLM backend SDKs (one-time)
uv tool install graphifyy --with anthropic --with openai

# Per repo: build the graph and wire the Claude Code hook
cd ~/code/anthropas-argus-alfred
graphify extract .
graphify cluster-only .   # free — no API calls; generates GRAPH_REPORT.md from graph.json
                          # also run this after a partial extraction (rate-limited chunks)
graphify claude install   # free — adds PreToolUse hook to .claude/settings.json

# Add to .gitignore before first commit
echo "graphify-out/manifest.json" >> .gitignore
echo "graphify-out/cost.json" >> .gitignore

# Commit the useful outputs
git add graphify-out/graph.json graphify-out/GRAPH_REPORT.md graphify-out/graph.html
git commit -m "Add graphify knowledge graph"
```

### ⚠️ Free Tier Rate Limit — Large Repos

Gemini free tier: **5 requests/minute** and **250k input tokens/minute**. Graphify fires all chunks in parallel without respecting the retry delays in the 429 response. For repos with 100+ files (17+ chunks), many chunks will rate-limit.

**Observed in trading-assistant (334 files, 17 chunks — May 11, 2026):**
- Chunks 1, 3, 4 completed; chunk 2 failed (connection error); chunks 5–17 hit 429
- Despite failures, graphify still wrote output from completed chunks:
  `graph.json: 116 nodes, 141 edges, 18 communities` · tokens: 225,612 in / 5,579 out · est. cost: ~$0.13
- `GRAPH_REPORT.md` and `graph.html` were not generated (partial extraction); run `graphify cluster-only .` (free, no API calls) to generate the report from existing `graph.json`

**For comparison — alfred repo (17 docs, 1 chunk):**
  `graph.json: 16 nodes, 13 edges, 4 communities` · tokens: 64,138 in / 1,916 out · est. cost: ~$0.04

**Fix options:**
- Use a real Anthropic API key (see below) — most reliable; ~$0.10–0.30 per full extraction
- For small/new repos: free Gemini tier works fine (stay under 1 chunk ≈ under ~20 files)
- `graphify cluster-only .` is always free — generates report from existing `graph.json`

---

### ⚠️ Claude Code Session — API Key Requirement

Graphify v0.7.13 requires an LLM API key in the **shell environment** — there is no AST-only fallback. Claude Code uses OAuth internally, so `ANTHROPIC_API_KEY` is not set as an env var and subprocess tools like graphify cannot access it.

**Fix:** Before running `graphify extract .` in a Claude Code session, set the key in-session:

```
! export ANTHROPIC_API_KEY=sk-ant-...
```

The `!` prefix runs it in the terminal and makes it available to graphify. This is a one-time step per session — the key is not persisted.

**NIM proxy note:** `localhost:8082` (free-claude-code proxy) is NOT compatible with graphify's Claude client — the response format causes a `'str' object has no attribute 'content'` error. Use the real Anthropic key directly.

**No Anthropic API key? Use Gemini (free):** Claude Pro (OAuth) and the Anthropic API are separate products with separate billing. If you don't have an API key, Google AI Studio provides free Gemini API access:

1. Go to `aistudio.google.com` → create a **project** first, then "Get API key" → create a free key (no credit card required; a project is required before generating a key)
2. Run extraction with the Gemini backend:
   ```
   ! GEMINI_API_KEY=your-gemini-key graphify extract /path/to/repo/
   ```
   Graphify uses an OpenAI-compatible client for Gemini — the `--with openai` install flag above covers this.

### .graphifyignore

Each repo gets a `.graphifyignore` to keep the graph focused on business logic only.
The canonical base template lives at `~/code/my-template/.graphifyignore` — deploy it per repo and add repo-specific overrides at the bottom.

**Why it matters:**
- **Bytecode stripping:** Hardhat/Foundry output massive metadata per compiled contract. Hiding `artifacts/` + `out/` stops Claude from parsing raw cryptographic bytecode.
- **Token economy:** Pruning `node_modules/` + framework caches ensures graphify builds its map exclusively on custom logic.
- **Security:** `.env` files and credential paths never enter the graph.

Key version fixes to be aware of when upgrading:
- `v0.4.1` — fixed indexing engine crashes from skipped ignore rules
- `v0.4.26` — resolved relative path matching bugs with `./raw` expressions
- `v0.6.8` — corrected strict behavior for nested whitelist patterns (`!src/**`)

---

## 📋 Integration Plan — Install Order

| Priority | Repo | Rationale | Sensitivity |
|----------|------|-----------|-------------|
| 1 | `anthropas-argus-alfred` | Start here — lowest sensitivity, learning reference | Low |
| 2 | `trading-assistant` | Fortuna navigates strategy/indicator/MCP architecture without reading every file | Medium — add `.graphifyignore` for `logs/`, `specs/`, `*.env` |
| 3 | `divorce-custody-assistant` | Alfred finds case logic connections without re-reading everything | High — careful `.graphifyignore` for case data dirs |
| 4 | `pir-devine-news` | Maps submission → review → publish pipeline at a glance | Low |
| 5 | `gratitude-token-project` | Kavanah/Claude Code navigates Hardhat contracts ↔ React frontend wiring without reading every component | Low — private capstone repo, key-free setup steps only |
| 6 | `gratitude-token-project_astro` | Public-preview Astro site — small, low-noise, cheap to keep current | Low — public repo |
| 7 | `gratitude-token-project_docs` | Large Docusaurus docs site — biggest token win from pruning noise (images, PDFs, `.docusaurus/` cache) | Low — public-facing docs |
| 8 | `mystarch_chief-of-staff` | Mystarch's own seat (`~/intent/workspaces/__chief__`) — orient across the whole ecosystem without re-reading everything | Medium — app-level coordination repo |
| 9 | `code-forked repos` | `free-claude-code`, `hummingbot-mcp`, `tradingview-mcp-jackson` — commit graphs as community learning reference after all main repos tested | None (public forks) |

### `gratitude-token-project` setup notes (2026-08-25, extraction completed 2026-08-27/28)

Key-free steps completed (`.graphifyignore` deployed from the canonical template
plus repo-specific overrides for `coverage/`, `boilerPlates/`, `.intent/`;
`graphify claude install` run; `graphify-out/manifest.json` + `cost.json`
gitignored). Christopher has since run the keyed extraction himself —
`graphify-out/graph.json` + `GRAPH_REPORT.md` + `graph.html` are present
(677 nodes · 1461 edges · 43 communities, 98% EXTRACTED). **Fully done.**

Also worth noting for that repo: `.claude/settings.json` (where the
PreToolUse hook lives) is gitignored there by existing repo convention — only
`.claude/skills/` is tracked — so the hook is local per worktree/clone and
would need `graphify claude install` re-run in `/Users/christopherwilson/dappu/gratitude-token-project`
separately if that worktree is used for Claude Code sessions too.

### `gratitude-token-project_astro` / `_docs` + `mystarch_chief-of-staff` setup notes (2026-09-02)

Key-free steps completed for all three: `.graphifyignore` deployed from the
canonical template (`_astro` gets a small `.vscode/` override; `_docs` gets a
heavier override for `.docusaurus/`, `.intent/`, `whitepaper4print/`,
`DAPP_frontend-content-export/`, `deployTest/`, and loose root-level
images/PDFs — this repo carries a lot of raw asset dumps that add no graph
signal). `graphify claude install` run in all three (`_astro` committed +
pushed cleanly; `_docs` and `__chief__` had substantial *pre-existing*
uncommitted work in the tree unrelated to graphify — deleted PDFs, in-progress
doc edits, an in-progress role-note change — so the graphify files were left
on disk uncommitted rather than risk bundling someone else's in-flight edits
into a commit. Christopher should review and commit those two separately).
`graphify extract .` **not run** in any of the three — needs Christopher's
Gemini key in-session (`! GEMINI_API_KEY=... graphify extract .`), same
handling as a PAT.

### Why It Helps Each Repo

**`trading-assistant` (Fortuna):** Maps the full indicator → strategy → backtester → MCP → live execution chain. Fortuna can find connections between ZTH rules, Pine Script indicators, and Tradovate order flow without opening 40 files.

**`divorce-custody-assistant` (Alfred):** Shows case document structure → financial modeling → filing logic connections. Alfred can identify which case context files are linked vs. isolated, and where new analysis should be anchored.

**`pir-devine-news`:** Maps data sources → Google Drive sync → dashboard pipeline. Shows where the submission → review → publish chain connects and where it might break.

**`code-forked repos`:** Left as committed knowledge graphs — useful reference for other builders cloning these forks to understand the architecture without reading everything.

---

## 🔒 Notes

- `graphify extract .` requires an LLM API key — v0.7.13 has no AST-only fallback mode. See the Claude Code session note above for the `! export` workaround.
- `graphify hook install` adds post-commit/post-checkout hooks for auto-rebuild; remove with `graphify hook uninstall` if they slow things down.
- If `GRAPH_REPORT.md` flags hardcoded secrets — rotate them, it's doing its job.

---

*Evaluated by Alfred · anthropas-argus-alfred · May 10, 2026*
