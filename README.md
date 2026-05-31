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

## ⚡ Quick start (3 steps)

### 1. Install from Codex Plugin Marketplace

```bash
codex plugin install opc
```

This adds an `opc` MCP server to `~/.codex/config.toml` that runs the local stdio bridge `@tsa-ai/opc-mcp-server` (auto-fetched via `npx`).

### 2. Generate your Personal API Token

Visit **[myopc.io/dashboard/settings/developer/ai-connections](https://www.myopc.io/dashboard/settings/developer/ai-connections)**:

1. Click the **Codex CLI** preset
2. Pick the scopes you want this token to have (e.g. `contacts.read`, `sales.write`, `accounting.read`)
3. Click **Generate token** — copy the resulting `opc_pat_xxxxxxxxxxxxxxxx`

### 3. Export the token before starting Codex

```bash
export OPC_API_TOKEN="opc_pat_xxxxxxxxxxxxxxxx"
codex
```

That's it. Try a starter prompt:

> "List my pending expense claims and summarise total amount."

> "What's my Profit & Loss for last month?"

> "Find all overdue invoices and draft polite reminder emails."

---

## 🔐 How auth works

The plugin's `.mcp.json` references `${OPC_API_TOKEN}` — Codex resolves this from your shell environment at startup and forwards it as a Bearer token to `https://www.myopc.io/api/mcp` on every tool call.

To make the token persistent across sessions, add the export to your shell profile:

```bash
# ~/.zshrc or ~/.bashrc
export OPC_API_TOKEN="opc_pat_xxxxxxxxxxxxxxxx"
```

Tokens are scoped — Codex only sees tools your scopes allow. You can rotate, revoke, or re-scope tokens at any time from the same wizard. Every tool call is audited (agent name, latency, status) and viewable in the **Usage** tab.

---

## Manual install (without Marketplace)

If you'd rather skip the Codex Marketplace and wire it up by hand, paste this into `~/.codex/config.toml`:

```toml
[mcp_servers.opc]
command = "npx"
args = ["-y", "@tsa-ai/opc-mcp-server"]
env = { OPC_BASE_URL = "https://www.myopc.io", OPC_API_TOKEN = "opc_pat_xxxxxxxxxxxxxxxx", OPC_AGENT_KIND = "codex" }
```

The bridge package is on npm: [`@tsa-ai/opc-mcp-server`](https://www.npmjs.com/package/@tsa-ai/opc-mcp-server). Pre-installing globally is optional (`npm install -g @tsa-ai/opc-mcp-server`) — `npx -y` will fetch the latest on first run.

---

## What's in this repo

```
.
├── .codex-plugin/
│   └── plugin.json      ← Codex plugin manifest (spec-required location)
├── .mcp.json            ← MCP server config — stdio bridge to https://www.myopc.io/api/mcp
├── assets/              ← logo + icon (PNG 256×256 + SVG sources)
└── skills/
    ├── expense-claim/   ← Skill: file an expense claim from a receipt
    └── sales-overview/  ← Skill: pull a sales pipeline summary
```

The actual MCP server source lives at [`Steveser1989/Main-MY-OPC-System/packages/opc-mcp`](https://github.com/Steveser1989) and is published to npm as [`@tsa-ai/opc-mcp-server`](https://www.npmjs.com/package/@tsa-ai/opc-mcp-server). All tools are served from `https://www.myopc.io/api/mcp` so upgrades reach every connected agent without users having to update this package.

---

## Links

- 🌐 Website — https://www.myopc.io
- 📖 Connect wizard — https://www.myopc.io/dashboard/settings/developer/ai-connections
- 📦 npm — https://www.npmjs.com/package/@tsa-ai/opc-mcp-server
- 🗂️ MCP Registry — `io.github.Steveser1989/opc-mcp-server`
- 🛍️ Codex Marketplace — https://www.codex-marketplace.com/plugins/opc

## License

MIT
