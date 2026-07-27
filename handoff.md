# Handoff — Monthly Kubera Routine (data reference)

Reference data for the **monthly Kubera routine only**. This routine keeps the manual (non-Plaid) loan balances current IN KUBERA: compute/gather each value, propose it, wait for Vartan's approval, then write via `Kubera:update_portfolio_item(custodianId, value)`. It does not touch the PFS.

## Manual loans (everything else in Kubera is Plaid-linked and self-updates)
| Loan | Kubera custodianId | Last value | Handling |
| --- | --- | ---: | --- |
| 2920 Eagle Rock (VT 2021 seller note) | 4cab8cc6-e02e-49e6-ac3b-ce5fd8ddd005 | 495,981 | AUTO — 4.75% fixed, amortizes to May 2036 |
| Lincoln / Chase VT 1992 | f483cad9-8f40-40e5-8c49-acc3c3e1a5f7 | 1,470,133 | AUTO — 3.71% fixed until 03/01/2027 reset |
| Wells Fargo 375 Canyon Vista | 1c62ed2d-fff5-47af-b525-02095931a170 | 904,421.84 | AUTO — 2.5% fixed until 01/01/2027 reset |
| SBA VT 3045 EIDL | 6877eb35-2e0b-4308-83c3-d161a3acba10 | 671,780 (STALE) | Needs anchor — see open item |
| SBA VT 3011 EIDL | 6b3d1a8e-b7f9-4832-8bca-55f33fb4cf5c | 145,000 (STALE) | Needs anchor — see open item |
| Thorofare / 712-826 Glendale | bd487e0a-f0b6-44dd-a568-586b68ba1c04 | 18,504,734.84 | STATEMENT each month (bridge loan, not computable) |

## AUTO-compute lookup tables (write the current month's value to Kubera)

**2920 Eagle Rock** [4cab8cc6…] — 4.75% fixed, month-end balances:
2026 Jul 492,710 | Aug 489,426 | Sep 486,129 | Oct 482,819 | Nov 479,495 | Dec 476,159
2027 Jan 472,809 | Feb 469,446 | Mar 466,070 | Apr 462,681 | May 459,278 | Jun 455,861
(Amortizes to May 2036; extend with the same rate/payment past this range if needed.)

**Lincoln / Chase VT 1992** [f483cad9…] — 3.71% fixed, month-end balances:
2026 Jul 1,467,305 | Aug 1,464,619 | Sep 1,461,925 | Oct 1,459,071 | Nov 1,456,359 | Dec 1,453,488
2027 Jan 1,450,757 | Feb 1,448,019
**HARD STOP: rate resets 03/01/2027 — do not compute past Feb 2027; flag that a fresh statement is needed.**

**Wells Fargo 375 CV** [1c62ed2d…] — 2.5% fixed, month-end balances (interest-bearing principal + $28,764.82 of second principal & deferred interest, which are flat until payoff):
2026 Jul 904,421.84 | Aug 902,136.87 | Sep 899,847.14 | Oct 897,552.63 | Nov 895,253.34 | Dec 892,949.26
**HARD STOP: ARM resets 01/01/2027 — do not compute past Dec 2026; flag that a fresh statement is needed.**

## STATEMENT loans (do NOT compute — flag for a statement)
- **Thorofare / Glendale** [bd487e0a…] — bridge loan; balance moves non-linearly. Needs a fresh Situs/Thorofare statement each month.
- **SBA VT 3045 & VT 3011 EIDL** — flagged until anchored (see below).

## SBA EIDL detail
Both are COVID-EIDL: 3.75% fixed, 30-year, with a 30-month payment deferment (NOT interest-only), then full P&I. Deferments have ended, so they amortize now and the stale flat balances need re-anchoring.
- VT 3045: original $672,000, payment $3,460/mo, note 11/19/2021, deferment ended ~06/2024, maturity 11/19/2051.
- VT 3011: original $145,000, payment ~$747/mo, note ~3/22/2022, deferment ended ~10/2024, maturity 3/22/2052.

## OPEN ITEM — anchor the two SBA EIDLs
Vartan has May 2025 SBA statements. Read the exact current balance from each, set that as the anchor, then amortize forward at 3.75% with the payments above. Once anchored, add month-end lookup tables here and move both EIDLs into the AUTO group. Until then, leave them flagged for a statement.

## Approve-before-write
Always PROPOSE the computed values and wait for Vartan's confirmation before calling `update_portfolio_item`. VT 3400 Note 1 receivable ($495,000) remains in place; VT 3400 Note 2 was paid off and set to 0.
