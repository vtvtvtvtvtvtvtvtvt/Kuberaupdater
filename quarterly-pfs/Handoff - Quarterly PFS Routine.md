# Handoff — Quarterly PFS Routine (data reference)

Reference data for the **quarterly PFS routine only**. This routine READS from Kubera and populates the PFS Excel. It never writes to Kubera and never computes loan balances.

## Files
- Format + baseline: `PFS Format Reference.xlsx` (defines the required delivery format; carries forward all non-Kubera fields).

## PFS current state (last approved)
- Net Worth: $65,987,050.08 · balanced (check cell = 0).

## Kubera -> PFS cell mapping
**Schedule C — Real Estate loans (column J):** read each loan's current Kubera balance into its cell.
| PFS cell | Loan | Kubera item |
| --- | --- | --- |
| J8 | 2920 Eagle Rock (VT 2021 seller note) | Ignacio Ramirez - VT 2021 LLC LOAN |
| J9 | Verdugo — VT 3011 | Columbia VT 3011 LLC LOAN |
| J10 | Verdugo — VT 3045 | Columbia VT 3045 LLC LOAN |
| J11 | Verdugo — SBA EIDL (VT 3045) | SBA VT 3045 LLC EIDL |
| J12 | Verdugo — SBA EIDL (VT 3011) | SBA VT 3011 LLC EIDL |
| J14 | 1201-1217 Cypress (VT 1207) | Columbia VT 1207 LLC LOAN |
| J15 | 3149 Verdugo (VT 3149) | Columbia VT 3149 LLC LOAN |
| J16 | 1974-1992 Lincoln (VT 1992) | Chase VT 1992 LLC LOAN |
| J19 | 712-826 Glendale (VT 826) | Thorofare Loan |
| J24 | 375 Canyon Vista (residence) | Wells Fargo 375 CV mortgage (Kubera value already includes deferred interest) |

**Schedule A — Cash:** each row is labeled with its institution + last-four; match Kubera bank/brokerage cash accounts to rows by those labels and enter the balance.

**Schedule B — Securities:** SUTXX (Schwab money fund) → C3 (share count); combined crypto (Coinbase + MetaMask + Trust Wallet) → D4; Fidelity → D5.

## Carry forward — DO NOT change (not sourced from Kubera)
Income (salary/bonuses), business valuations (Sch D), property market values (Sch C column I), receivables (Sch E), personal property (Sch F), and any other non-Kubera field. These stay exactly as the baseline has them.

## Timing note
Kubera balances are as of the run date (~1st of the new quarter), which may reflect one payment beyond the exact quarter-end on amortizing loans. That's acceptable for this snapshot; if a loan must tie exactly to quarter-end, flag it for Vartan.

## Output
Save without underscores: `Vartan Tchekmedyian PFS MM-DD-YYYY.xlsx`. After approval, replace `PFS Format Reference.xlsx` with the approved file.
