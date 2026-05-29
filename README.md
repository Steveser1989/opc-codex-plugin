# OPC Codex Plugin

Connect [Codex](https://www.codex-marketplace.com) to your **OPC Business Operations** workspace at [www.myopc.io](https://www.myopc.io).

OPC Suite gives any AI agent secure, audit-logged access to a complete business stack:

- 📇 CRM, deals, customers, suppliers
- 💰 Sales orders, invoices, quotations, P&L
- 📦 Inventory, products, stock movements
- 🧾 Accounting, journals, taxes
- 🧑‍💼 HRM, payroll, expense claims
- 📋 Projects, tasks, milestones

## Install

After this plugin is approved on [codex-marketplace.com](https://www.codex-marketplace.com), it appears in the Codex CLI plugin picker:

```bash
codex plugin install opc
```

Then provide your Personal API Token from `https://www.myopc.io/dashboard/settings/developer`.

## Manual install (right now)

```bash
npm install -g @tsa-ai/opc-mcp-server
```

Add to your `~/.codex/config.toml`:

```toml
[mcp_servers.opc]
command = "npx"
args = ["-y", "@tsa-ai/opc-mcp-server"]
env = { OPC_API_TOKEN = "opc_pat_…", OPC_AGENT_KIND = "codex" }
```

## What's in this repo

```
.codex-plugin/
├── plugin.json          ← Codex plugin manifest
├── .mcp.json            ← MCP server config (points to https://www.myopc.io/api/mcp)
├── assets/              ← logo + icon
└── skills/
    ├── expense-claim/   ← Skill: file an expense claim from a receipt
    └── sales-overview/  ← Skill: pull a sales pipeline summary
```

The actual MCP server source lives at [`Steveser1989/Main-MY-OPC-System/packages/opc-mcp`](https://github.com/Steveser1989) and is published to npm as [`@tsa-ai/opc-mcp-server`](https://www.npmjs.com/package/@tsa-ai/opc-mcp-server).

## Links

- 🌐 Website — https://www.myopc.io
- 📖 Docs — https://www.myopc.io/dashboard/settings/developer
- 📦 npm — https://www.npmjs.com/package/@tsa-ai/opc-mcp-server
- 🗂️ MCP Registry — `io.github.Steveser1989/opc-mcp-server`

## License

MIT
