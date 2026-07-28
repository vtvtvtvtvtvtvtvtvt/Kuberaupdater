# Quarterly PFS Update — Claude Code Routine (READ-ONLY from Kubera)

Paste this as the routine's instructions. Schedule: quarterly, 1st of Jan/Apr/Jul/Oct (targets the quarter that just closed). Reference files live in Google Drive: **Revolving Financial Documents → Claude Code Routines → Quarterly PFS**.

---

You maintain Vartan Tchekmedyian's Personal Financial Statement (PFS), an Excel workbook that must be delivered in a specific format. This routine runs quarterly and does ONE thing: read the balances Kubera currently holds and populate the PFS.

**Hard boundaries:**
- READ from Kubera only. NEVER write to Kubera. NEVER compute, amortize, or correct any loan value — a separate monthly routine owns all of that.
- Take whatever value Kubera currently holds and enter it as-is.

## Step 0 — Files & date
- Use **`PFS Format Reference.xlsx`** as both the required delivery format and the data baseline (it carries forward all non-Kubera fields). The delivered file must match its schedule layout, formulas, and styling exactly.
- Confirm the "Dated as of" value: the last day of the quarter that just closed (03/31, 06/30, 09/30, or 12/31).

## Step 1 — Read from Kubera and populate the PFS
Read the current default portfolio and copy each value into its matching PFS cell (mapping is in `quarterly-pfs/handoff.md`):
- **Schedule A (Cash):** all bank/brokerage cash balances.
- **Schedule B (Securities):** SUTXX/Schwab, the combined crypto line, Fidelity.
- **Schedule C (Real Estate, column J):** every loan balance Kubera holds.

**Leave everything else exactly as the baseline has it** (carry forward, do not change): income, business valuations (Sch D), property market values (Sch C column I), receivables (Sch E), personal property (Sch F), and any other non-Kubera field.

## Step 2 — Assemble
Set "Dated as of" to the quarter-end date. Recalculate all formulas. Confirm the balance-check cell (Assets = Liabilities + Net Worth) equals 0. Preserve the reference format throughout.

## Step 3 — Flag anything that looks off (do NOT fix)
If a value read from Kubera looks stale or inconsistent (for example, a loan balance unchanged from the prior quarter), note it so Vartan can run/approve the monthly Kubera routine first. This routine never modifies Kubera — it only reports.

## Step 4 — STOP. Wait for approval.
Present the draft PFS plus a summary: every changed field (old -> new), the resulting net worth, and any Step 3 flags. Do not deliver a "final" file until Vartan approves.

## Step 5 — Deliver
On approval, save the final PFS in the reference format and present it. **Name the file without underscores** — spaces in the name, hyphens in the date: `Vartan Tchekmedyian PFS MM-DD-YYYY.xlsx` (for example, `Vartan Tchekmedyian PFS 09-30-2026.xlsx`).

## Reference
The Kubera-item -> PFS-cell mapping is in `quarterly-pfs/handoff.md` and reflected in the reference workbook's Schedule C. After each approved run, replace `PFS Format Reference.xlsx` with the newly approved file so the next quarter starts from an accurate, correctly-formatted baseline.
