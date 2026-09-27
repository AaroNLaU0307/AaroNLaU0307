# Audit record

An independent research-engineering audit of the repositories linked from this profile, run on
2026-09-27 and published here on purpose: the findings, what was fixed, and what still waits on a
re-run are part of the record, not a private appendix to it.

| File | What it is |
|---|---|
| `00-SUMMARY.md` | Phase 1. Scorecard per repository, the prioritised findings (P0 factual errors and leakage risks, P1 reproducibility gaps, P2 practice), the cross-repository systemic issues, and the judgment calls that were put to the owner. |
| `profile-readme-crosscheck.md` | Phase 1. Every cell of the profile table checked against the artifact that is supposed to support it. |
| `<repo>.md` | Phase 1. Located findings for each repository, one file per repo. |
| `PHASE2_REPORT.md` | Phases 2 and 3. Per-finding status with the commit that carries each fix, the evidence label on every claim (reproduced, reasoned, or self-reported), the merge into each default branch, and the CI result on the merged head. |

**How it was run.** Phase 1 was read-only. Phase 2 changed code, tests and documents on a branch per
repository, with a regression test for every code fix that fails on the pre-fix source, and never
edited a pre-registration, sealed artifact or decision log in place; corrections are dated files
that cite what they correct. Phase 3 fast-forwarded each default branch to that head and confirmed
CI green there.

**What is still open.** Two studies were changed at the engine or data-pipeline level
(quant-backtest-framework, commodity-carry-research). Their code is fixed and tested, but their
published numbers predate the fix, because the re-run needs licensed data that only exists on the
owner's machine. Each carries a `RERUN_RUNBOOK.md`; until it is executed the profile marks those
figures with ‡. The handoff section of `PHASE2_REPORT.md` lists everything else that waits on the
owner.

Findings here are stated as they were found. Where a claim in a repository turned out to be
wrong, the repository now says so; this record is not softened after the fact.
