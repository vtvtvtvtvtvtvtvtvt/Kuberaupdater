# Monthly Kubera Update — Claude Code Routine (PROPOSES writes; you approve separately)

Schedule: monthly. Keeps the manual (non-Plaid) loan balances current in Kubera. Reference data: `monthly-kubera/handoff.md` (this repo).

## Boundaries
- This routine PROPOSES. On the scheduled run it does NOT call `Kubera:update_portfolio_item`. It outputs the proposed changes and stops.
- Only the manual loans in the handoff are in scope. Every other Kubera item is Plaid-linked and updates itself — leave those alone.

## Step 1 — Determine each manual loan's balance for the current month
- **AUTO loans** (2920 Eagle Rock, Lincoln, Wells Fargo): look up the current month's balance from the amortization tables in the handoff. Respect the hard-stops — Lincoln resets 03/01/2027, Wells Fargo resets 01/01/2027. Past those dates, do NOT compute; flag that a statement is needed.
- **STATEMENT loans** (Thorofare; the two SBA EIDLs until anchored): do NOT compute. Flag that a fresh statement is needed.

## Step 2 — Output a proposal (no writes)
Produce a table: loan, Kubera custodianId, current value → proposed value, and the basis. List any flags. Do NOT write to Kubera.

## Step 3 — Approval + write (separate, human-triggered)
After Vartan reviews and approves the proposal, write the confirmed values via `Kubera:update_portfolio_item(custodianId, value)` — one call per approved loan. Report success/failure per item.
