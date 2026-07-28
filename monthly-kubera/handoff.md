# Handoff — Monthly Kubera Update (data reference)

Reference data for the **monthly Kubera routine only**. This routine keeps the manual (non-Plaid) loan balances current in Kubera. It PROPOSES writes on the scheduled run and writes only after Vartan approves (see `monthly-kubera/routine-prompt.md`).

> ⚠️ **This file is partially scaffolded.** The custodian IDs and current Kubera
> balances below were pulled live from Kubera on **2026-07-28** and are correct.
> The **amortization inputs** for the AUTO loans (original principal, rate, term,
> monthly payment, first-payment date, payment day) are **placeholders marked
> `‹NEEDS DATA›`** — the routine cannot compute an AUTO balance until these are
> filled in from each loan's note/statement. Until then, treat every loan as a
> STATEMENT loan and flag it.

## Scope — manual loans only
Only the loans in the table below are manual (non-Plaid) and in scope for this routine. **Every other Kubera loan is Plaid-linked and self-updates — never touch it.** For reference, the out-of-scope Plaid loans are: Columbia VT 3011 / VT 3045 / VT 1207 / VT 3149 / VT 3400 LLC loans.

## Manual loans — custodian IDs and current values (live as of 2026-07-28)

| Loan | Kubera item name | custodianId | Current Kubera value | Class |
| --- | --- | --- | --: | --- |
| 2920 Eagle Rock (VT 2021 seller note) | Ignacio Ramirez - VT 2021 LLC LOAN | `4cab8cc6-e02e-49e6-ac3b-ce5fd8ddd005` | 495,981 | AUTO |
| 1974-1992 Lincoln (VT 1992) | Chase Bank - VT 1992 LLC LOAN | `f483cad9-8f40-40e5-8c49-acc3c3e1a5f7` | 1,470,133 | AUTO |
| 375 Canyon Vista residence (Wells Fargo) | Wells Fargo - MORTGAGE - 375 CV - 5676 | `1c62ed2d-fff5-47af-b525-02095931a170` | 889,397 | AUTO |
| 712-826 Glendale (VT 826) | Thorofare Loan - 714-826 Glendale Ave | `bd487e0a-f0b6-44dd-a568-586b68ba1c04` | 18,504,734 | STATEMENT |
| Verdugo SBA EIDL (VT 3011) | SBA - VT 3011 LLC EIDL LOAN | `6b3d1a8e-b7f9-4832-8bca-55f33fb4cf5c` | 145,000 | STATEMENT (until anchored) |
| Verdugo SBA EIDL (VT 3045) | SBA - VT 3045 LLC EIDL LOAN | `6877eb35-2e0b-4308-83c3-d161a3acba10` | 672,000 | STATEMENT (until anchored) |

## AUTO loans — amortization inputs (FILL IN before relying on computed balances)

The routine derives the current-month balance by amortizing from these inputs. Fill each `‹NEEDS DATA›` from the loan note / most recent statement, then the routine can compute AUTO balances instead of flagging them.

### 2920 Eagle Rock — Ignacio Ramirez / VT 2021 LLC seller note
- Original principal: `‹NEEDS DATA›`
- Annual interest rate: `‹NEEDS DATA›`
- Amortization term (months): `‹NEEDS DATA›`
- Monthly payment (P&I): `‹NEEDS DATA›`
- First payment date: `‹NEEDS DATA›`
- Payment day of month: `‹NEEDS DATA›`
- Balloon / maturity date, if any: `‹NEEDS DATA›`
- Anchor: balance `495,981` as of 2026-07-28 (Kubera). Confirm against a statement.

### 1974-1992 Lincoln — Chase / VT 1992 LLC loan
- Original principal: `‹NEEDS DATA›`
- Annual interest rate: `‹NEEDS DATA›`
- Amortization term (months): `‹NEEDS DATA›`
- Monthly payment (P&I): `‹NEEDS DATA›`
- First payment date: `‹NEEDS DATA›`
- Payment day of month: `‹NEEDS DATA›`
- **Hard stop:** rate/terms reset **03/01/2027** — do NOT compute past this date; flag that a new statement is needed.
- Anchor: balance `1,470,133` as of 2026-07-28 (Kubera). Confirm against a statement.

### 375 Canyon Vista — Wells Fargo mortgage
- Original principal: `‹NEEDS DATA›`
- Annual interest rate: `2.5%` fixed (per Kubera note). Confirm.
- Amortization term (months): `‹NEEDS DATA›`
- Monthly payment (P&I): `‹NEEDS DATA›`
- First payment date: `‹NEEDS DATA›`
- Payment day of month: `‹NEEDS DATA›`
- **Hard stop / discrepancy to resolve:** the routine prompt says the rate resets **01/01/2027**; the Kubera note says **"2.5% Fixed until 02/01/2027."** Confirm the correct reset date, then align both. Do NOT compute past the reset; flag for a statement.
- Note: the Kubera value already includes deferred interest — keep that convention.
- Anchor: balance `889,397` as of 2026-07-28 (Kubera). Confirm against a statement.

## STATEMENT loans — no computation, flag for a fresh statement
These are never amortized by the routine. Each run, flag that a current statement is needed and enter the confirmed balance only on approval.

- **Thorofare (714-826 Glendale):** current `18,504,734`. Interest-only / lender-reported — always statement-driven.
- **SBA EIDL VT 3011:** current `145,000`. Carried at a stale value — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.
- **SBA EIDL VT 3045:** current `672,000`. Same — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.

## Open item
The two SBA EIDLs are still carried at stale Kubera values. Anchor them from the May 2025 SBA statements, then update this file to move them into the AUTO group (with full amortization inputs) if they amortize, or keep them STATEMENT if they remain lender-reported.

## Write mechanics
On approval only: `Kubera:update_portfolio_item(custodianId, value)` — one call per approved loan, using the custodianId from the table above. Report success/failure per item.
