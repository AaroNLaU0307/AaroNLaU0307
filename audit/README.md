# Audit record

An independent research-engineering audit of the repositories linked from this profile, run on
2026-09-27 and published here on purpose: the findings, what was fixed, and the re-runs that
followed are part of the record, not a private appendix to it.

| File | What it is |
|---|---|
| `00-SUMMARY.md` | Phase 1. Scorecard per repository, the prioritised findings (P0 factual errors and leakage risks, P1 reproducibility gaps, P2 practice), the cross-repository systemic issues, and the judgment calls that were put to the owner. |
| `profile-readme-crosscheck.md` | Phase 1. Every cell of the profile table checked against the artifact that is supposed to support it. |
| `<repo>.md` | Phase 1. Located findings for each repository, one file per repo. |
| `PHASE2_REPORT.md` | Phases 2 to 4, and 6. Per-finding status with the commit that carries each fix, the evidence label on every claim (reproduced, reasoned, or self-reported), the merge into each default branch, the CI result on the merged head, the merge of the two re-runs, and the merge of the owner-machine artifacts. |

**How it was run.** Phase 1 was read-only. Phase 2 changed code, tests and documents on a branch per
repository, with a regression test for every code fix that fails on the pre-fix source, and never
edited a pre-registration, sealed artifact or decision log in place; corrections are dated files
that cite what they correct. Phase 3 fast-forwarded each default branch to that head and confirmed
CI green there. Phase 4 merged the two re-runs and removed the "re-run pending" qualifiers. Phase 6
merged the artifacts committed on the owner's machine.

**Re-runs.** Two studies were changed at the engine or data-pipeline level
(quant-backtest-framework, commodity-carry-research). Both were re-run on the owner's machine,
which holds the licensed data, following each repo's `RERUN_RUNBOOK.md`, and merged on 2026-09-28.
Both verdicts held: the SMC study stays FALSIFIED (walk-forward pooled OOS E[R] −0.339 → −0.329 R),
and commodity carry stays NOT PROMOTED (H1 net Sharpe −0.003 → 0.059, CI [−0.41, 0.52]; H2 now
closes at the premise gate). The profile shows the re-run figures. The gold study's one-time
2023–2025 OOS figure predates the fix and was deliberately not recomputed.

**What is still open.** The re-runs are done, and the artifacts that existed only on the owner's
machine are committed (Phase 6): orderflow's quarantine windows, QA logs, ETH repair runner and
proof, and deterministic ingestion; spot's data-cache manifest; TSMOM's core return series,
screening report, DGS3MO series and a T-bill excess-return sensitivity (Sharpe 0.61, CI
[0.15, 1.09], beside the unchanged rf = 0 headline). What remains is documented, not pending. The
orderflow ETH 2023-05 splice cannot be reproduced from the raw archives, because the pre-fix
ingestion did not fix the order of same-millisecond trades; the rebuild is proven and the stored
parquet is the authority. `phase3_sensitivity_stage.py` re-ingests raw zips, so a future run may
shift two sensitivity configs slightly. TSMOM's `PROJECT_STATE.md` and `qros-state.yaml` stay at
the repository root because sealed records pin them there. The spot decision-log lines stay as
written, by owner decision. The commodity trial count is settled at N_trials = 16, counting the
lag-1 execution sensitivity as registered trials (delegate decision, 2026-09-27); the DSR
threshold at 14 is reported beside it in that repo's addendum for comparison.

Findings here are stated as they were found. Where a claim in a repository turned out to be
wrong, the repository now says so; this record is not softened after the fact.
