---
name: sales-overview
description: Show the sales pipeline / today's orders / outstanding invoices in OPC. Use when the user asks "how are sales", "show me my pipeline", "what's outstanding", or wants a sales briefing.
---

# Sales Overview

Compose a short executive briefing using the OPC MCP tools.

## Steps

1. `list_sales_orders` with `status: "open"` to find in-flight orders.
2. `list_deals` to see the active pipeline by stage.
3. Optionally `get_profit_and_loss` for revenue context (only if user asked about revenue).
4. Format the response as a tight bulleted summary — never paste raw JSON.

## Output template

```
**Today (YYYY-MM-DD)**

- Open orders: N (total $X)
- Top deal in pipeline: <title> ($X, stage)
- Overdue invoices: N
- Suggested next action: <one sentence>
```
