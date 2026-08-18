---
name: expense-claim
description: File or process an employee expense claim in OPC. Use when the user asks to "submit a receipt", "file an expense", "create an expense claim", or wants to upload a receipt for reimbursement.
---

# Expense Claim Workflow

## When to use

Trigger this skill when the user wants to:

- Submit a new expense claim with a receipt
- Upload a receipt image and have it OCR-extracted
- Settle or cancel an existing expense claim
- Look up the status of an outstanding claim

## The claim lifecycle

OPC runs a **solo workflow** — there is no separate approver, so the old
`submitted → under_review → approved` chain no longer exists. A claim has three
meaningful states:

```
draft ──▶ settled     (paid / reimbursed — this is the normal path)
      └─▶ cancelled   (withdrawn, never paid)
```

`settled` and `cancelled` are final; neither can be transitioned out of. Legacy
claims still sitting in `submitted`, `under_review` or `approved` can also be
moved to those two states.

## Steps

1. **If the user provided a receipt image**, first upload it via `/api/documents/upload` with `claim_id` deferred (set later) — this returns a storage URL.
2. Call `create_claim` with:
   - `claim_type: "employee"`
   - `claim_reason: "expense_reimbursement"`
   - `title` — short summary of the expense
   - `receipt_image_url` — URL from step 1 (if any)
   - `merchant_name`, `expense_date`, `expense_category`, `amount_claimed`, `currency`
3. The server auto-creates a "Claims" Documents folder entry linked to the receipt — confirm to the user.
4. To pay the claim out, call **either**:
   - `transition_claim` with `to_status: "settled"` — the quick path. `amount_approved` defaults to `amount_claimed`.
   - `settle_claim` with `settlement_type: "reimbursement"` and an explicit `amount` — use this when the paid amount differs from the claimed amount, or when the settlement is a credit note / replacement / write-off.
5. To withdraw a claim instead, call `transition_claim` with `to_status: "cancelled"`.

## Tips

- If the user uploads a receipt without context, ask for the merchant and amount before creating the claim.
- Claims with `reimbursement_method: "payroll"` (the default) are picked up automatically by the next `create_payroll_run` and added to the employee's allowances — do **not** also settle them by hand, or the employee gets paid twice.
- A claim becomes immutable once settled or folded into a payroll run; edits after that point are rejected with HTTP 423.
- The Documents module's "Claims" folder is the source of truth for receipts; never store images elsewhere.
