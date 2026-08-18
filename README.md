# OPC Codex Plugin

Connect [Codex](https://www.codex-marketplace.com) to your **OPC Business Operations** workspace at [www.myopc.io](https://www.myopc.io).

OPC Suite gives any AI agent secure, audit-logged access to a complete business stack:

- 📇 CRM, deals, customers, suppliers
- 💰 Sales orders, invoices, quotations, P&L
- 📦 Inventory, products, stock movements
- 🧾 Accounting, journals, taxes
- 🧑‍💼 HRM, payroll, expense claims
- 📋 Projects, tasks, milestones

---

## ⚡ Quick start (2 commands, no token setup)

### 1. Install from the Codex Plugin Marketplace

```bash
codex plugin install opc
```

### 2. One-click OAuth — Codex opens your browser, you click Approve

```bash
codex mcp login opc
```

Codex will:

1. Spin up a local OAuth callback server
2. Open `https://www.myopc.io/oauth/authorize?...` in your browser
3. (If you're not signed into OPC yet, you'll be bounced to the login page first)
4. Show OPC's **consent screen** — pick the scopes you want Codex to have (e.g. `contacts.read`, `sales.write`, `accounting.read`)
5. After you click **Approve**, Codex receives the token and stores it securely in `~/.codex/auth/`

**That's it.** No tokens to copy. No environment variables. No `~/.codex/config.toml` edits.

```bash
codex
> List my pending expense claims and summarise total amount.
```

> 💡 **Codex Desktop users**: this OAuth flow is triggered **automatically** the first time the agent tries to call an OPC tool. You can skip step 2 and just say *"List my expense claims"* — Codex will pop the consent screen by itself.

---

## 🔐 How auth works

Under the hood, OPC implements full **OAuth 2.1 + PKCE + Dynamic Client Registration**:

- The plugin's `.mcp.json` declares OPC as a streamable HTTP MCP server at `https://www.myopc.io/api/mcp`
- On first call, the server returns `401` + `WWW-Authenticate: Bearer resource_metadata=...`
- Codex follows the metadata to discover OPC's authorization endpoints (RFC 8414 / RFC 9728)
- Codex auto-registers itself via Dynamic Client Registration (RFC 7591)
- Browser-based PKCE flow → user consent → bearer token issued
- Token is scoped to whatever you approved, refreshable, and revocable from **Settings → Developer** at any time

Every tool call is audit-logged (agent name, scopes used, latency, status).

---

## 🔄 Keeping the tool catalog fresh

OPC's tools are served from the server, so upgrades reach you without
reinstalling the plugin — but **Codex caches the tool list for the life of a
connection**. After a server-side change (a new tool, a corrected argument
schema) an already-connected session keeps using the catalog it fetched at
startup. The symptom is confusing: the tool call fails with an error from the
current backend, while the schema Codex shows you is the old one.

Restart Codex, or re-run `codex mcp login opc`, and the new catalog is picked
up.

### Checking which catalog you have

```bash
curl -s https://www.myopc.io/api/mcp/health \
  -H "Authorization: Bearer opc_pat_xxxxxxxxxxxxxxxx" | jq .catalog
```

```json
{ "fingerprint": "5070e087", "delete_tool_count": 24 }
```

The fingerprint is derived from every tool's name, required fields and enums,
so any schema change moves it. If the server reports a fingerprint but your
session behaves as though the old schema is in force, it is the client cache —
reconnect. `tool_count` in the same response tells you how many tools your
token's scopes actually grant.

### Cleaning up

Every record the plugin can create, it can also delete — `delete_contact`,
`delete_project`, `delete_payroll_run`, `delete_pay_component` and so on, 24 in
all. They are marked destructive, so Codex will ask before calling one. Some
are deliberately restricted server-side: a claim or an e-Invoice can only be
deleted while it is still a draft.

## 🛠️ Advanced fallback — Personal API Token (for non-OAuth Codex builds)

If you're on an older Codex version without MCP OAuth support, or you'd prefer to manage the token yourself, you can fall back to a Personal API Token (PAT):

### 1. Generate a token

Visit **[myopc.io/dashboard/settings/developer/ai-connections](https://www.myopc.io/dashboard/settings/developer/ai-connections)**, pick the **Codex CLI** preset, choose your scopes, and copy the resulting `opc_pat_xxxxxxxxxxxxxxxx`.

### 2. Use the local stdio bridge

Replace the OAuth `[mcp_servers.opc]` entry in `~/.codex/config.toml` with the stdio bridge form:

```toml
[mcp_servers.opc]
command = "npx"
args = ["-y", "@tsa-ai/opc-mcp-server"]
env = { OPC_BASE_URL = "https://www.myopc.io", OPC_API_TOKEN = "opc_pat_xxxxxxxxxxxxxxxx", OPC_AGENT_KIND = "codex" }
```

The bridge package is on npm: [`@tsa-ai/opc-mcp-server`](https://www.npmjs.com/package/@tsa-ai/opc-mcp-server). It's a thin pipe to the same hosted endpoint, so server-side tool catalog upgrades reach you without re-installing.

This path works on every Codex version (CLI and Desktop, old and new) but skips the polished consent UI.

---

## What's in this repo

```
.
├── .codex-plugin/
│   └── plugin.json      ← Codex plugin manifest (spec-required location)
├── .mcp.json            ← MCP server config — streamable HTTP w/ OAuth discovery
├── assets/              ← logo + icon (PNG 256×256 + SVG sources)
└── skills/
    ├── expense-claim/   ← Skill: file an expense claim from a receipt
    └── sales-overview/  ← Skill: pull a sales pipeline summary
```

The actual MCP server source lives at [`Steveser1989/Main-MY-OPC-System/packages/opc-mcp`](https://github.com/Steveser1989) and the OAuth + tool endpoints are served from `https://www.myopc.io/api/mcp`. All upgrades happen server-side — every connected agent picks them up immediately.

---

## Links

- 🌐 Website — https://www.myopc.io
- 📖 Connect wizard — https://www.myopc.io/dashboard/settings/developer/ai-connections
- 🔐 OAuth metadata — https://www.myopc.io/.well-known/oauth-authorization-server
- 📦 npm — https://www.npmjs.com/package/@tsa-ai/opc-mcp-server
- 🗂️ MCP Registry — `io.github.Steveser1989/opc-mcp-server`
- 🛍️ Codex Marketplace — https://www.codex-marketplace.com/plugins/opc

## License

MIT
