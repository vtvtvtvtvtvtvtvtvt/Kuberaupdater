# Handoff — Monthly Kubera Update (data reference)

Reference data for the **monthly Kubera routine only**. This routine keeps the manual (non-Plaid) loan balances current in Kubera. It PROPOSES writes on the scheduled run and writes only after Vartan approves (see `monthly-kubera/routine-prompt.md`).

> Custodian IDs and current balances were pulled live from Kubera on **2026-07-28**.
> AUTO-loan amortization terms were confirmed from source documents on the same date
> (VT 2021 amortization schedule; Chase executed loan docs + 02/2026 statement;
> Wells Fargo 06/2026 statement). Each computed table below was reconciled against the
> live Kubera balance and matches within a few dollars.

## Scope — manual items only
Only the items in the table below are manual (non-Plaid) and in scope for this routine. **Every other Kubera loan is Plaid-linked and self-updates — never touch it.** For reference, the out-of-scope Plaid loans are: Columbia VT 3011 / VT 3045 / VT 1207 / VT 3149 / VT 3400 LLC loans.

> ⚠️ **Don't confuse the two VT 3400 items.** The out-of-scope **Plaid debt** is *Columbia — VT 3400 LLC LOAN — 4192* (id `24ca05b5-df68-4026-b09a-c767d7b55f10`, currently $0) — leave it alone. The in-scope item added below is *VT 3400 LLC Note 1* (id `4e76a440-…`), a note **receivable / ASSET** Vartan holds that accrues interest monthly.

## Manual items — custodian IDs and current values (live as of 2026-07-28)

| Item | Kubera item name | custodianId | Current Kubera value | Class |
| --- | --- | --- | --: | --- |
| 2920 Eagle Rock (VT 2021 seller note) | Ignacio Ramirez - VT 2021 LLC LOAN | `4cab8cc6-e02e-49e6-ac3b-ce5fd8ddd005` | 495,981 | AUTO (fixed lookup) |
| 1974-1992 Lincoln (VT 1992) | Chase Bank - VT 1992 LLC LOAN | `f483cad9-8f40-40e5-8c49-acc3c3e1a5f7` | 1,470,133 | AUTO (amortize, resets 03/01/2027) |
| 375 Canyon Vista residence (Wells Fargo) | Wells Fargo - MORTGAGE - 375 CV - 5676 | `1c62ed2d-fff5-47af-b525-02095931a170` | 904,422 | AUTO (amortize, resets ~01/01/2027) |
| VT 3400 LLC Note 1 (note receivable — **ASSET**) | VT 3400 LLC Note 1 | `4e76a440-849d-47e4-9fd5-77f09e598fcb` | 502,037 | AUTO (accrue 8.5%/yr compounding, value ↑ — ASSET) |
| 712-826 Glendale (VT 826) | Thorofare Loan - 714-826 Glendale Ave | `bd487e0a-f0b6-44dd-a568-586b68ba1c04` | 19,090,533 *(per 08/22/2026 statement, due 09/01/2026 — see STATEMENT loans section)* | STATEMENT |
| Verdugo SBA EIDL (VT 3011) | SBA - VT 3011 LLC EIDL LOAN | `6b3d1a8e-b7f9-4832-8bca-55f33fb4cf5c` | 145,000 | STATEMENT (until anchored) |
| Verdugo SBA EIDL (VT 3045) | SBA - VT 3045 LLC EIDL LOAN | `6877eb35-2e0b-4308-83c3-d161a3acba10` | 672,000 | STATEMENT (until anchored) |

---

## AUTO loan 1 — 2920 Eagle Rock (Ignacio Ramirez / VT 2021 LLC seller note)

Fixed-rate, fully-amortizing seller note. **No rate reset — this is a pure month → balance lookup; never needs a statement.**

- Original principal: ~$810,000 · Rate: ~4.75% fixed · Term: 240 months (20 yr)
- Monthly payment (P&I): **$5,234** · First payment: ~Jun 2016 · Payoff: **May 2036**
- The routine reads the ending balance for the current month from the schedule below.

**Ending-balance schedule (from the source amortization table; enter the row for the current month):**

| Month | Balance | Month | Balance |
| --- | --: | --- | --: |
| Jun 2026 | 495,981 *(= current Kubera)* | Jan 2027 | 472,809 |
| Jul 2026 | 492,710 | Feb 2027 | 469,446 |
| Aug 2026 | 489,426 | Mar 2027 | 466,070 |
| Sep 2026 | 486,129 | Apr 2027 | 462,681 |
| Oct 2026 | 482,819 | May 2027 | 459,278 |
| Nov 2026 | 479,495 | Jun 2027 | 455,861 |
| Dec 2026 | 476,159 | Jul 2027 | 452,431 |

For months beyond this table, extend from the source amortization schedule (same fixed $5,234 payment, ~4.75%, payoff May 2036).

---

## AUTO loan 2 — 1974-1992 Lincoln (Chase / VT 1992 LLC · "Lincoln Row, LLC")

Commercial term loan, 30-year amortization with a **rate reset on 03/01/2027**.

- Original principal: **$1,600,000** · Closed ~02/28/2022 · Loan #200542845
- Rate: **3.71%** fixed until 03/01/2027, accrued **actual/360** · Amort term: 360 months
- Monthly payment (P&I): **$7,373.58** (escrow $0)
- **Hard stop:** do NOT compute past **03/01/2027** — the rate resets; flag for a statement.
- Reconciliation: anchoring on the 02/2026 statement (ending balance $1,481,363.30) and amortizing forward reproduces the current Kubera balance (Jun 2026 ≈ $1,470,139 vs Kubera $1,470,133). ✓

**Method:** interest = balance × 3.71% ÷ 12; principal = $7,373.58 − interest; new balance = balance − principal.

**Computed ending-balance table (enter the row for the current month):**

| Month | Balance | Month | Balance |
| --- | --: | --- | --: |
| Jun 2026 | 1,470,137 *(≈ current Kubera)* | Nov 2026 | 1,455,907 |
| Jul 2026 | 1,467,308 | Dec 2026 | 1,453,034 |
| Aug 2026 | 1,464,471 | Jan 2027 | 1,450,153 |
| Sep 2026 | 1,461,625 | Feb 2027 | 1,447,263 |
| Oct 2026 | 1,458,770 | **Mar 2027 →** | **RESET — statement needed** |

---

## AUTO loan 3 — 375 Canyon Vista (Wells Fargo mortgage · residence)

**Modified ARM with deferred balances — read the composition note carefully.** Loan #0565255676, maturity 01/2050.

- Interest-bearing (unpaid) principal: **$875,657.02** as of the 07/01/2026 payment
- Deferred "second principal": **$13,740.54** (fixed; due at payoff/sale/maturity)
- Deferred interest: **$15,024.28** (fixed; due at payoff/sale/maturity)
- Rate: **2.500%** fixed until **~01/01/2027** (statement: "Until 01/01/27"; online: "Next ARM adjustment 02/01/27")
- Monthly payment (P&I): **$4,109.26**

> **What Kubera holds (CONVENTION — decided 2026-07-28, reconfirmed 2026-08-28):** Kubera's value = **interest-bearing
> principal + second principal + deferred interest**. As of the 07/01/2026 payment that is
> 875,657.02 + 13,740.54 + 15,024.28 = **904,421.84**.
> All three components are carried in the Kubera figure so it reflects the full payoff amount.
> This aligns with `quarterly-pfs/handoff.md` (J24). The two fixed deferred balances
> (second principal $13,740.54 + deferred interest $15,024.28 = **$28,764.82**) stay constant;
> only the interest-bearing principal amortizes.
>
> **Cadence — update only on the 27th (with the rest of this routine):** on 2026-07-11 and
> 2026-08-05 this item was updated off-cycle (outside this routine, on the 11th/5th of the
> month rather than the 27th) to interest-bearing principal + second principal **only**,
> dropping the $15,024.28 deferred interest — e.g. 08/05/2026 → $887,112.59 instead of the
> $902,136.87 the gross-up convention calls for. Vartan confirmed 2026-08-28: keep the gross-up
> (deferred interest included) and update this item **only** as part of this routine's 27th run —
> do not let an off-cycle update stand uncorrected; the next 27th run should true it back up to
> the gross-up figure for the current month.

**Method (only the interest-bearing principal amortizes):** interest = interest-bearing principal × 2.500% ÷ 12; principal = $4,109.26 − interest; the second-principal and deferred-interest balances stay fixed. Proposed Kubera value = new interest-bearing principal + 28,764.82 (= 13,740.54 second principal + 15,024.28 deferred interest).

**Computed Kubera-value table (enter the row for the current month):**

| Month (payment applied) | Interest-bearing principal | Proposed Kubera value |
| --- | --: | --: |
| 07/01/2026 | 875,657.02 | 904,422 *(= current Kubera)* |
| 08/01/2026 | 873,372.05 | 902,137 |
| 09/01/2026 | 871,082.32 | 899,847 |
| 10/01/2026 | 868,787.81 | 897,553 |
| 11/01/2026 | 866,488.52 | 895,253 |
| 12/01/2026 | 864,184.44 | 892,949 |
| 01/01/2027 | 861,875.56 | 890,640 |
| **02/01/2027 →** | **RESET** | **statement needed** |

---

## AUTO item 4 — VT 3400 LLC Note 1 (accruing note receivable · ASSET)

**This is an ASSET, not a debt — accrual INCREASES the Kubera value.** VT 3400 LLC (lender = Vartan) holds a $495,000 note against **2762ftbd LLC** (borrower), secured by 2762 W Ave 35, LA (APN 5457-022-022). Per the **Modification Agreement executed 05/27/2026**, interest accrues at 8.5%/yr with **no payments for the first 12 months**; the balance compounds monthly until payments begin.

- Principal: **$495,000** · Accrual anchor: **2026-06-01** (clean first-of-month start → **no stub**)
- Rate: **8.5%/yr**, compounded monthly · Monthly factor = 1 + 0.085 ÷ 12 = **1.00708333** (≈ $3,506/mo on the opening balance)
- **No payments** for 12 months (06/01/2026 → 06/01/2027).
- **Payments begin 06/01/2027:** $4,391.00/mo P&I on the 1st, until maturity **05/30/2051**. (The $4,391 payment reconciles to the compounded 06/01/2027 balance of $538,753.49 amortized over 288 months — confirming monthly compounding.)
- **Hard stop: the 2027-05-27 run is the last accrual run** (it posts the 06/01/2027 balance $538,753.49). From the 2027-06-27 run on, the note is in **repayment** — switch to an amortization schedule ($4,391/mo); do NOT keep accruing.

> **Run convention:** the routine runs on the **27th** and posts the balance **as of the 1st of the following month** (crediting the upcoming month). Method: new balance = prior first-of-month balance × 1.00708333.

**Computed value table (on each run date, enter the "value to post"):**

| Run date (~27th) | Posts balance as of | Value to post |
| --- | --- | --: |
| 2026-07-27 *(current)* | 2026-08-01 | **502,037.34** |
| 2026-08-27 | 2026-09-01 | 505,593.44 |
| 2026-09-27 | 2026-10-01 | 509,174.72 |
| 2026-10-27 | 2026-11-01 | 512,781.37 |
| 2026-11-27 | 2026-12-01 | 516,413.57 |
| 2026-12-27 | 2027-01-01 | 520,071.50 |
| 2027-01-27 | 2027-02-01 | 523,755.34 |
| 2027-02-27 | 2027-03-01 | 527,465.27 |
| 2027-03-27 | 2027-04-01 | 531,201.48 |
| 2027-04-27 | 2027-05-01 | 534,964.16 |
| 2027-05-27 | 2027-06-01 | 538,753.49 |
| **2027-06-27 →** | repayment begun | **HARD STOP — switch to $4,391/mo amortization** |

*(Reference first-of-month rests: 06/01/2026 anchor = 495,000.00; 07/01/2026 = 498,506.25.)*

As of the current run (2026-07-28) the value to post is the **2026-08-01** rest = **$502,037.34** (principal + 2 monthly accruals). Kubera was carried flat at $495,000, so this update also catches up the missed accrual.

### AUTO item 4 — repayment phase (from 06/01/2027) — READY TO SWITCH IN

Once payments begin, the note **amortizes DOWN** at **$4,391.00/mo** (8.5%/yr, interest in arrears). Financed balance = **$538,753.50** as of 06/01/2027 (the last accrual value). Same run convention — run the 27th, post the following 1st. When the 2027-05-27 accrual run has posted, replace the accrual table above with this one.

**Method:** each month — interest = balance × 0.085 ÷ 12; principal = 4,391.00 − interest; new balance = balance − principal. Post the balance as of the following 1st.

**First repayment year (run date → value to post):**

| Run (~27th) | Posts as of | Value to post |
| --- | --- | --: |
| 2027-06-27 | 2027-07-01 | 538,178.67 |
| 2027-07-27 | 2027-08-01 | 537,599.77 |
| 2027-08-27 | 2027-09-01 | 537,016.77 |
| 2027-09-27 | 2027-10-01 | 536,429.64 |
| 2027-10-27 | 2027-11-01 | 535,838.35 |
| 2027-11-27 | 2027-12-01 | 535,242.87 |
| 2027-12-27 | 2028-01-01 | 534,643.17 |
| 2028-01-27 | 2028-02-01 | 534,039.23 |
| 2028-02-27 | 2028-03-01 | 533,431.01 |
| 2028-03-27 | 2028-04-01 | 532,818.48 |
| 2028-04-27 | 2028-05-01 | 532,201.61 |
| 2028-05-27 | 2028-06-01 | 531,580.37 |

**Annual checkpoints (balance as of 06/01)** — to sanity-check the running balance without carrying the full 288-row schedule:

| As of 06/01 | Balance | | As of 06/01 | Balance |
| --- | --: | --- | --- | --: |
| 2028 | 531,580.37 | | 2040 | 375,841.15 |
| 2029 | 523,773.20 | | 2041 | 354,268.05 |
| 2030 | 515,275.94 | | 2042 | 330,788.08 |
| 2031 | 506,027.60 | | 2043 | 305,232.69 |
| 2032 | 495,961.80 | | 2044 | 277,418.44 |
| 2033 | 485,006.27 | | 2045 | 247,145.67 |
| 2034 | 473,082.37 | | 2046 | 214,197.06 |
| 2035 | 460,104.51 | | 2047 | 178,336.08 |
| 2036 | 445,979.52 | | 2048 | 139,305.33 |
| 2037 | 430,606.01 | | 2049 | 96,824.61 |
| 2038 | 413,873.62 | | 2050 | 50,588.99 |
| 2039 | 395,662.25 | | 2051 | 266.55 |

**Payoff:** the fixed $4,391 payment retires the note by **mid-2051** — balance is $266.55 as of 06/01/2051, cleared by a final ~$268 payment (note's stated maturity is 05/30/2051; the small residual is a rounding stub from $4,391.00 vs the exact $4,391.28 level payment). At payoff the Kubera value reaches $0 — **archive the item.** For any month not listed, compute it from the method above.

---

## STATEMENT loans — no computation, flag for a fresh statement
These are never amortized by the routine. Each run, flag that a current statement is needed and enter the confirmed balance only on approval.

- **Thorofare (714-826 Glendale):** current `19,090,533` (Current Principal Balance per Situs AMC statement, loan #330184116, statement date 08/22/2026, payment due 09/01/2026; Deferred Interest Balance on that statement is $0.00, so no add-on). Prior value was `18,504,734`. Lender-reported — always statement-driven.
- **SBA EIDL VT 3011:** current `145,000`. Carried at a stale value — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.
- **SBA EIDL VT 3045:** current `672,000`. Same — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.

## Open items
1. The two SBA EIDLs are still carried at stale Kubera values. Anchor them from the May 2025 SBA statements, then update this file to move them into an AUTO group (with amortization inputs) if they amortize, or keep them STATEMENT if lender-reported.
2. ~~Resolve the Wells Fargo deferred-interest question.~~ **Resolved 2026-07-28:** Kubera value is grossed up to include deferred interest (interest-bearing principal + $13,740.54 second principal + $15,024.28 deferred interest = $904,421.84). Written to Kubera and aligned in `quarterly-pfs/handoff.md` J24.
3. Both AUTO reset dates (Lincoln 03/01/2027, Wells Fargo ~01/01–02/01/2027) fall within ~8 months. After each reset, replace that loan's computed table with a fresh statement anchor.
4. **VT 3400 LLC Note 1 (AUTO item 4):** terms confirmed from the 05/27/2026 Modification Agreement — $495,000, 8.5%/yr compounding monthly from 2026-06-01, no payments until 06/01/2027, then $4,391.00/mo P&I to maturity 05/30/2051. Runs on the 27th, posting the following 1st. **When the 2027-05-27 run posts the 06/01/2027 balance ($538,753.49), the accrual phase is over** — before the 2027-06-27 run, build the $4,391/mo amortization schedule and switch AUTO item 4 to a repayment table.

## Write mechanics
On approval only: `Kubera:update_portfolio_item(custodianId, value)` — one call per approved loan, using the custodianId from the scope table above. Report success/failure per item.
