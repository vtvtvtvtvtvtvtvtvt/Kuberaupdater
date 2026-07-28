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
| VT 3400 LLC Note 1 (note receivable — **ASSET**) | VT 3400 LLC Note 1 | `4e76a440-849d-47e4-9fd5-77f09e598fcb` | 495,000 | AUTO (accrue 8%/yr, value ↑ — ASSET) |
| 712-826 Glendale (VT 826) | Thorofare Loan - 714-826 Glendale Ave | `bd487e0a-f0b6-44dd-a568-586b68ba1c04` | 18,504,734 | STATEMENT |
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

> **What Kubera holds (CONVENTION — decided 2026-07-28):** Kubera's value = **interest-bearing
> principal + second principal + deferred interest**. As of the 07/01/2026 payment that is
> 875,657.02 + 13,740.54 + 15,024.28 = **904,421.84** (written to Kubera on 2026-07-28).
> All three components are carried in the Kubera figure so it reflects the full payoff amount.
> This aligns with `quarterly-pfs/handoff.md` (J24). The two fixed deferred balances
> (second principal $13,740.54 + deferred interest $15,024.28 = **$28,764.82**) stay constant;
> only the interest-bearing principal amortizes.

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

**This is an ASSET, not a debt — accrual INCREASES the Kubera value.** Vartan holds a $495,000 note against VT 3400 LLC. No payments are due for the first 12 months; interest accrues monthly at 8%/yr and is added to the balance. Kubera has carried it flat at $495,000 since origination and has never been updated for accrual, so the first approved run is a **catch-up** to the current month.

- Principal: **$495,000** · Origination / anchor: **2025-12-16** (Kubera entry date; value clean at 495,000 through 2026-07-28 per item history)
- Rate: **8.0%/yr**, accrued monthly · Monthly rate = 8% ÷ 12 = **0.66667%**
- **No payments for the first 12 months** → interest accrues and is added to the balance each month.
- **Hard stop: 2026-12-16** (month 12). The no-payment window ends here — do NOT extend past it; confirm the post-window terms (payments begin? balloon? continue accruing?) before proposing any later month.

> ⚠️ **ASSUMPTIONS pending Vartan's confirmation** — the numbers below depend on these; correct if wrong:
> 1. **Compounding basis:** monthly **compounding** (unpaid interest capitalizes and itself accrues) is the default column. If the note is **simple** interest (8%/yr on the original $495,000 only, +$3,300/mo flat), use the simple-interest column instead.
> 2. **Accrual start:** interest runs from **2025-12-16**, accruing on the **16th** of each month.

**Method (default — monthly compounding):** new balance = prior balance × (1 + 0.08 ÷ 12). Anchor = $495,000 on 2025-12-16; accrue on the 16th.

**Computed value table (enter the row for the current accrual month — the latest 16th on/before the run date):**

| Accrual month (16th) | Compounding (default) | Simple-interest alt (+3,300/mo) |
| --- | --: | --: |
| 2025-12-16 (origination) | 495,000.00 | 495,000.00 |
| 2026-01-16 | 498,300.00 | 498,300.00 |
| 2026-02-16 | 501,622.00 | 501,600.00 |
| 2026-03-16 | 504,966.15 | 504,900.00 |
| 2026-04-16 | 508,332.59 | 508,200.00 |
| 2026-05-16 | 511,721.47 | 511,500.00 |
| 2026-06-16 | 515,132.95 | 514,800.00 |
| 2026-07-16 | 518,567.17 *(current month)* | 518,100.00 |
| 2026-08-16 | 522,024.28 | 521,400.00 |
| 2026-09-16 | 525,504.44 | 524,700.00 |
| 2026-10-16 | 529,007.80 | 528,000.00 |
| 2026-11-16 | 532,534.52 | 531,300.00 |
| **2026-12-16 →** | **536,084.75 — HARD STOP** | **534,600.00 — HARD STOP** |

As of the run date (2026-07-28) the current accrual month is **2026-07-16**, so the proposed value is **$518,567.17** (compounding) / **$518,100.00** (simple). Kubera currently shows $495,000, so this is a catch-up of **~$23,567**.

---

## STATEMENT loans — no computation, flag for a fresh statement
These are never amortized by the routine. Each run, flag that a current statement is needed and enter the confirmed balance only on approval.

- **Thorofare (714-826 Glendale):** current `18,504,734`. Lender-reported — always statement-driven.
- **SBA EIDL VT 3011:** current `145,000`. Carried at a stale value — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.
- **SBA EIDL VT 3045:** current `672,000`. Same — anchor from the May 2025 SBA statement, then move to AUTO if it amortizes.

## Open items
1. The two SBA EIDLs are still carried at stale Kubera values. Anchor them from the May 2025 SBA statements, then update this file to move them into an AUTO group (with amortization inputs) if they amortize, or keep them STATEMENT if lender-reported.
2. ~~Resolve the Wells Fargo deferred-interest question.~~ **Resolved 2026-07-28:** Kubera value is grossed up to include deferred interest (interest-bearing principal + $13,740.54 second principal + $15,024.28 deferred interest = $904,421.84). Written to Kubera and aligned in `quarterly-pfs/handoff.md` J24.
3. Both AUTO reset dates (Lincoln 03/01/2027, Wells Fargo ~01/01–02/01/2027) fall within ~8 months. After each reset, replace that loan's computed table with a fresh statement anchor.
4. **VT 3400 LLC Note 1 (new AUTO item 4):** confirm the accrual basis (monthly **compounding** vs **simple**) and the start date with Vartan — the table defaults to monthly compounding from 2025-12-16. Kubera has never been updated for accrual, so the first approved run is a **~$23.6k catch-up** (to $518,567.17 compounding / $518,100 simple as of 07/2026). Hard stop at the 12-month mark (2026-12-16) — confirm post-window payment terms before extending.

## Write mechanics
On approval only: `Kubera:update_portfolio_item(custodianId, value)` — one call per approved loan, using the custodianId from the scope table above. Report success/failure per item.
