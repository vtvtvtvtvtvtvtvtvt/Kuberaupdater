# Monthly Kubera Update — Claude Code Routine (PROPOSES writes; you approve separately)

Schedule: monthly. Keeps the manual (non-Plaid) loan/note balances current in Kubera. Reference data: `monthly-kubera/handoff.md` (this repo).

## Boundaries
- This routine PROPOSES. On the scheduled run it does NOT call `Kubera:update_portfolio_item`. It outputs the proposed changes and stops.
- Only the manual items in the handoff are in scope. Every other Kubera item is Plaid-linked and updates itself — leave those alone. In particular, do NOT touch the Plaid *Columbia — VT 3400 LLC LOAN — 4192* debt; the in-scope VT 3400 item is the separate *VT 3400 LLC Note 1* asset.

## Step 1 — Determine each manual item's value for the current month
- **AUTO items** (2920 Eagle Rock, Lincoln, Wells Fargo — debts that amortize DOWN; **VT 3400 LLC Note 1** — an accruing note *asset* that grows UP at 8%/yr): look up the current month's value from the tables in the handoff. Respect the hard-stops — Lincoln resets 03/01/2027, Wells Fargo resets ~01/01/2027, and VT 3400 Note 1's 12-month no-payment window ends 2026-12-16. Past those dates, do NOT compute; flag that a statement / confirmed terms are needed.
- **STATEMENT loans** (Thorofare; the two SBA EIDLs until anchored): do NOT compute. Flag that a fresh statement is needed.

## Step 2 — Output a proposal (no writes)
Produce a table: loan, Kubera custodianId, current value → proposed value, and the basis. List any flags. Do NOT write to Kubera.

## Step 3 — Approval + write (separate, human-triggered)
After Vartan reviews and approves the proposal, write the confirmed values via `Kubera:update_portfolio_item(custodianId, value)` — one call per approved loan. Report success/failure per item.
