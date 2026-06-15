# CLAUDE.md — waw-mcp

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
