---
name: expense-claim
description: File or process an employee expense claim in OPC. Use when the user asks to "submit a receipt", "file an expense", "create an expense claim", or wants to upload a receipt for reimbursement.
---

# Expense Claim Workflow

## When to use

Trigger this skill when the user wants to:

- Submit a new expense claim with a receipt
- Upload a receipt image and have it OCR-extracted
- Approve / settle an existing expense claim
- Look up the status of a pending claim

## Steps

1. **If the user provided a receipt image**, first upload it via `/api/documents/upload` with `claim_id` deferred (set later) — this returns a storage URL.
2. Call `create_claim` with:
   - `claim_type: "employee"`
   - `claim_reason: "expense_reimbursement"`
   - `title` — short summary of the expense
   - `receipt_image_url` — URL from step 1 (if any)
   - `merchant_name`, `expense_date`, `expense_category`, `amount_claimed`
3. The server auto-creates a "Claims" Documents folder entry linked to the receipt — confirm to the user.
4. To progress the claim: call `transition_claim` with `to_status: "submitted"` (then `under_review`, `approved`, `settled`).
5. To finalise: call `settle_claim` with `settlement_type: "reimbursement"` and the amount.

## Tips

- If the user uploads a receipt without context, ask for the merchant and amount before creating the claim.
- The Documents module's "Claims" folder is the source of truth for receipts; never store images elsewhere.
