# waw-mcp — World Agentic Web MCP server

The **MCP server** of [WAW](https://github.com/Revens2/waw). It gives an AI agent, over a
single stdio MCP connection:

- a **real, valid email address** as its identity (`mailbox_provision`),
- the ability to **read OTP / verification codes** sent to it (`otp_get`),
- **autonomous signup** to a website with a real browser (`signup_run`),
- and **discovery / publishing of MCP servers** by intent in the shared WAW registry
  (`registry_search` / `registry_register`).

This package is the **standalone, runs-anywhere** distribution: one bundled file, started
by `node` over stdio, configured **only by environment variables** (no secret ever lives in
the repo). The full source, the HTTP/SMTP gateway that receives email, and the Docker demo
live in the main repo: **https://github.com/Revens2/waw**.

---

## Install

```bash
npm install -g waw-mcp        # global `waw-mcp` command
# or run without installing:
npx waw-mcp
```

`better-sqlite3` (native) is installed automatically. For `signup_run` you also need a
browser once: `npx playwright install chromium`.

## Configure (environment variables — never commit secrets)

| Variable | Required | Purpose |
|---|---|---|
| `VAULT_PASSPHRASE` *(or `VAULT_MASTER_KEY`)* | **yes** | Master secret for the encrypted vault. |
| `MAIL_DOMAIN` | recommended | Domain the agent provisions addresses on (e.g. `agents.acme.com`). |
| `DB_PATH` | no | SQLite path. Default `~/.waw/gateway.db`. Point it at the WAW gateway's DB to receive real email. |
| `WEBHOOK_SIGNING_SECRET` | no | Only if pairing with the WAW gateway for inbound email. |

## Use from an MCP client (Claude Code, etc.)

Add to your MCP config (e.g. `.mcp.json` or `claude mcp add`). Secrets go in the `env`
block, which lives in your **local** client config — not in any repo:

```json
{
  "mcpServers": {
    "waw": {
      "command": "waw-mcp",
      "env": {
        "VAULT_PASSPHRASE": "your-long-random-passphrase",
        "MAIL_DOMAIN": "agents.example.com"
      }
    }
  }
}
```

```bash
# CLI equivalent
claude mcp add waw --env VAULT_PASSPHRASE=… --env MAIL_DOMAIN=agents.example.com -- waw-mcp
```

---

## Tools

| Tool | Purpose |
|---|---|
| `mailbox_provision` / `mailbox_list` | Get (or list) a real `@MAIL_DOMAIN` address. |
| `mailbox_link_owner` | Link the human operator's real email to a mailbox. |
| `otp_get` / `emails_list` | Read the latest OTP / recent emails for a mailbox. |
| `signup_run` | Resumable, idempotent autonomous signup (Playwright + vault + OTP). |
| `registry_search` / `registry_register` / `registry_list` / `registry_get` | Discover or publish MCP servers by intent. |

### First connection — link a human owner

On a fresh connection the agent calls `mailbox_list`; if it is empty, it asks the **human
operator for their real email** and provisions its primary identity with `ownerEmail` set to
it. The agent address is then **derived from the owner's**, and the ownership is recorded —
every autonomous identity traces back to a real human (accountability + recovery):

```
owner you@example.com  →  agent you.agent@MAIL_DOMAIN
```

The owner email is supplied at runtime by the human; it is **never** stored in this repo.

---

## Receiving real email

This server reads OTPs from the WAW database. To make real messages land there, pair it
with the **WAW gateway** (main repo) sharing the same `DB_PATH`: the gateway exposes a
signed inbound webhook (Mailgun / Postmark / generic) and an optional self-hosted SMTP
server. See https://github.com/Revens2/waw for the gateway, the security model, and a
Dockerized end-to-end demo.

## Security

Secrets come only from environment variables; `.env` is git-ignored and `.env.example`
carries placeholders. Vault data is AES-256-GCM encrypted at rest, OTP codes are never
returned over the wire, and logs redact sensitive fields. Full threat model in the main
repo's `SECURITY.md`.

## License

MIT
