# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

waw-mcp

The **standalone distribution** of the WAW (World Agentic Web) MCP server. One bundled ESM
file started by `node` over **stdio**, configured **only by environment variables**. Gives an
AI agent: a real email identity, OTP reading, autonomous signup, browser perception
(vision + UI tree), and an MCP discovery registry.

## ⚠️ This repo ships a build artifact

`dist/waw-mcp.mjs` is **generated** — do not edit it by hand. The source of truth is the main
repo **[`waw`](https://github.com/Revens2/waw)** (`src/`). To update this package:

```bash
# in the waw repo
npm run mcp:bundle            # esbuild → dist-bundle/waw-mcp.mjs
# copy that file here, then:
npm install                   # native dep: better-sqlite3
```

`better-sqlite3` and `playwright` are kept **external** (native / heavy) and declared as
(optional) dependencies — never bundled.

## Run

```bash
npm install -g waw-mcp        # global `waw-mcp` command (runs from any cwd)
npx waw-mcp                   # once published to npm
```

## Configuration (env only — never commit secrets)

| Variable | Required | Purpose |
|---|---|---|
| `VAULT_PASSPHRASE` (or `VAULT_MASTER_KEY`) | yes | Master secret for the encrypted vault. |
| `MAIL_DOMAIN` | recommended | Domain the agent provisions addresses on. |
| `DB_PATH` | no | SQLite path. Default `~/.waw/gateway.db`. Point at the WAW gateway DB to receive real email. |
| `WEBHOOK_SIGNING_SECRET` | no | Only when pairing with the WAW gateway for inbound email. |

Secrets are passed via the MCP client's `env` block (local config), or a gitignored `.env`.
`.env.example` carries placeholders only. `files` in package.json whitelists the tarball
(`dist`, README, LICENSE, .env.example) — no `.env`/DB can leak.

## Tools (15)

`mailbox_provision` · `mailbox_list` · `mailbox_link_owner` · `otp_get` · `emails_list` ·
`signup_run` · `registry_search` · `registry_register` · `registry_list` · `registry_get` ·
`browser_open` · `browser_observe` · `browser_fill` · `browser_click` · `browser_close`.

- **First connection**: call `mailbox_list`; if empty, ask the human for their real email and
  pass it as `ownerEmail` to `mailbox_provision` (the agent address derives from it).
- **Perception**: `browser_open`/`browser_observe` return a screenshot (vision) **and** the
  interactive elements with refs/selectors (UI tree); act by `ref` or `selector`. Combine both
  to adapt when guessed selectors fail.

## Constraints

- stdio transport: stdout is JSON-RPC — never print to stdout (logs go to stderr at warn level).
- Don't hand-edit `dist/`; regenerate from `waw`. Keep this file and the README in sync with the tool list.
- MIT licensed. Full source, gateway, security model, and Docker demo live in the `waw` repo.

## CodeGraph — Codebase Intelligence

This project is indexed by CodeGraph. **Use CodeGraph MCP tools instead of Grep/Glob/Explore agents for code analysis.** The pre-built index provides instant, semantic, relationship-aware results.

### Tier 1 — Start Here (use first)

| Tool | When to Use | Instead of |
|------|------------|------------|
| `codegraph_context` | Starting any task — returns relevant code, relationships, and structure | Multiple Read + Grep calls |

### Tier 2 — Drill Down (after context)

| Tool | When to Use | Instead of |
|------|------------|------------|
| `codegraph_callers` | "Who calls this function?" | Grep for function name |
| `codegraph_callees` | "What does this function call?" | Reading function body manually |
| `codegraph_node` | Get full source code of a specific symbol | Read tool on the whole file |
| `codegraph_query` | Search for symbols by name or meaning | Glob + Grep |
| `codegraph_search` | Quick exact name lookup (<10ms) | Grep for exact function name |
| `codegraph_dependencies` | "What does this file/module depend on?" | Reading import statements |
| `codegraph_find_references` | "Where is this used?" (all relationship types) | Project-wide Grep |

### Tier 3 — Specialized (when task requires)

**Deep Search:** `codegraph_deep_query` (cross-encoder re-ranked search for highest precision)
**Structure & Analysis:** `codegraph_structure` (PageRank overview), `codegraph_impact` (blast radius), `codegraph_stats`, `codegraph_circular_imports`, `codegraph_project_tree`, `codegraph_export_map`, `codegraph_import_graph`, `codegraph_file`, `codegraph_diagram`, `codegraph_dead_code`, `codegraph_tests`, `codegraph_frameworks`, `codegraph_languages`
**Git:** `codegraph_blame`, `codegraph_file_history`, `codegraph_recent_changes`, `codegraph_commit_diff`, `codegraph_symbol_history`, `codegraph_branch_info`, `codegraph_modified_files`, `codegraph_hotspots`, `codegraph_contributors`
**Security:** `codegraph_scan_security`, `codegraph_check_owasp`, `codegraph_check_cwe`, `codegraph_explain_vulnerability`, `codegraph_suggest_fix`, `codegraph_find_injections`, `codegraph_taint_sources`, `codegraph_security_summary`, `codegraph_trace_taint`
**Data Flow:** `codegraph_find_path` (call path between functions), `codegraph_complexity`, `codegraph_data_flow`, `codegraph_dead_stores`, `codegraph_find_uninitialized`, `codegraph_reaching_defs`

### Anti-Patterns — Don't Do This

- **Don't** `grep -r "functionName"` — use `codegraph_callers("functionName")`
- **Don't** read entire files to find a function — use `codegraph_node("functionName")`
- **Don't** spawn Explore agents for code structure — use `codegraph_context("your task")`
- **Don't** manually trace imports — use `codegraph_dependencies("file.ts")`
- **Don't** launch Explore agents to trace code flow — use `codegraph_dependencies` + `codegraph_callers`
- **Don't** use `git log` via Bash — use `codegraph_file_history` or `codegraph_recent_changes`

### Project Stats
- Languages: N/A
- Symbols: 0 | Relationships: 0
