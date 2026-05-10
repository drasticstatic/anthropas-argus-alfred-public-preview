# Graphify — Setup, Security Review & Integration Plan
#### Evaluated by Alfred (Anthropas-Argus-Alfred) · May 10, 2026

> **What this is:** Pre-install security review + complete setup guide + ecosystem integration plan for `github.com/safishamsi/graphify`.
> Fortuna's trading/financial research appendix is forthcoming in `FREE_MODEL_SETUP.md`.

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
# Install globally (one-time)
uv tool install graphifyy

# Per repo: build the graph and wire the Claude Code hook
cd ~/code/anthropas-argus-alfred
graphify .
graphify claude install

# Add to .gitignore before first commit
echo "graphify-out/manifest.json" >> .gitignore
echo "graphify-out/cost.json" >> .gitignore

# Commit the useful outputs
git add graphify-out/graph.json graphify-out/GRAPH_REPORT.md graphify-out/graph.html
git commit -m "Add graphify knowledge graph"
```

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
| 5 | `code-forked repos` | `free-claude-code`, `hummingbot-mcp`, `tradingview-mcp-jackson` — commit graphs as community learning reference after all main repos tested | None (public forks) |

### Why It Helps Each Repo

**`trading-assistant` (Fortuna):** Maps the full indicator → strategy → backtester → MCP → live execution chain. Fortuna can find connections between ZTH rules, Pine Script indicators, and Tradovate order flow without opening 40 files.

**`divorce-custody-assistant` (Alfred):** Shows case document structure → financial modeling → filing logic connections. Alfred can identify which case context files are linked vs. isolated, and where new analysis should be anchored.

**`pir-devine-news`:** Maps data sources → Google Drive sync → dashboard pipeline. Shows where the submission → review → publish chain connects and where it might break.

**`code-forked repos`:** Left as committed knowledge graphs — useful reference for other builders cloning these forks to understand the architecture without reading everything.

---

## 🔒 Notes

- `graphify .` on repos with PDF/image docs will trigger LLM API calls (uses `ANTHROPIC_API_KEY` from env). AST-only extraction is free — pure local tree-sitter.
- `graphify hook install` adds post-commit/post-checkout hooks for auto-rebuild; remove with `graphify hook uninstall` if they slow things down.
- If `GRAPH_REPORT.md` flags hardcoded secrets — rotate them, it's doing its job.

---

*Evaluated by Alfred · anthropas-argus-alfred · May 10, 2026*
