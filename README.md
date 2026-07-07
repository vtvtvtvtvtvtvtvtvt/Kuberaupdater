# Financial Routines — Claude Code

Two independent Claude Code cloud routines that keep Vartan Tchekmedyian's financial records current:
- **monthly-kubera/** — keeps the manual (non-Plaid) loan balances current IN KUBERA.
- **quarterly-pfs/** — reads Kubera and produces the PFS Excel workbook.

They never overlap: the monthly one owns Kubera writes and loan math; the quarterly one only reads Kubera and fills the PFS.

## Where things live
- **This repo** (cloned fresh at the start of every run — guaranteed present, version-controlled): the routine prompts and the handoff reference files. Text only.
- **Google Drive** (via connector): the PFS workbook — `PFS Format Reference.xlsx` (format + baseline) and the dated deliverables. Folder: `Revolving Financial Documents → Claude Code Routines → Quarterly PFS`.
- **Connectors**: Kubera (balance reads + writes) and Google Drive (workbook read/write).

## Approval model — READ THIS
Cloud routines run to completion autonomously; they CANNOT pause mid-run to ask for approval. So each routine is built to PROPOSE, not finalize:
- **monthly-kubera** computes the loan balances and OUTPUTS the proposed writes (old → new). It does NOT call Kubera on the scheduled run. You review, then trigger the write yourself (a separate Run-now / approval step).
- **quarterly-pfs** reads Kubera, builds a DRAFT PFS, saves it to Drive as a dated deliverable, and does NOT overwrite `PFS Format Reference.xlsx`. You review, then promote it to the reference.

This keeps a human check before anything writes to Kubera or becomes the new source of truth.

## What does NOT auto-update
- The handoff `.md` files are STATIC reference (custodian IDs, cell maps, loan terms). They are not rewritten each run. Update one only when a fact actually changes (e.g., after anchoring the SBA EIDLs), and commit it deliberately.
- The Drive reference workbook is promoted by you after review — not auto-replaced by a run.

## Branch behavior
By default routines push only to `claude/`-prefixed branches for your review. Merge to main once you approve.

## Open item
The two SBA EIDLs are still carried at stale Kubera values. Anchor them from the May 2025 SBA statements (see monthly-kubera/handoff.md), then update monthly-kubera/handoff.md to move them into the AUTO group.
