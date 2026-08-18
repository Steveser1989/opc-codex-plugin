---
name: sales-overview
description: Show the sales pipeline / today's orders / outstanding invoices in OPC. Use when the user asks "how are sales", "show me my pipeline", "what's outstanding", or wants a sales briefing.
---

# Sales Overview

Compose a short executive briefing using the OPC MCP tools.

## Steps

1. `list_sales_orders` to find in-flight orders. There is no "open" status —
   the valid values are `draft`, `confirmed`, `processing`, `shipped`,
   `delivered` and `cancelled`. Call it without a filter and treat
   `confirmed` / `processing` / `shipped` as in-flight, or filter one status
   at a time.
2. `list_deals` for the active pipeline. Pass `status: "open"` to exclude
   won and lost deals; each deal carries a `stage_id` and its stage name.
3. `get_aging_report` with `type: "receivable"` for money owed to the
   business, bucketed by how overdue it is.
4. Optionally `get_profit_and_loss` for revenue context (only if the user
   asked about revenue).
5. Format the response as a tight bulleted summary — never paste raw JSON.

## Output template

```
**Today (YYYY-MM-DD)**

- In-flight orders: N (total $X)
- Top deal in pipeline: <title> ($X, stage)
- Receivables overdue 30+ days: $X
- Suggested next action: <one sentence>
```

## Tips

- Amounts come back in each record's own `currency` — don't sum across
  different currencies without saying so.
- `get_aging_report` buckets by age: `current`, `d30`, `d60`, `d90`,
  `over90`. "Overdue" means anything past `current`.
