# Audit: AaroNLaU0307 (profile README)

## Scope and method
- **Commit audited:** `7f4f734`, `README.md` (28 lines, last edited 2026-08-19).
- **What was checked:** every claim in the profile was traced to the linked repos at their HEADs. The cell-by-cell evidence is in `profile-readme-crosscheck.md`; this file lists the findings that follow from it.
- **Staleness:** the profile has not been edited since 2026-08-19. multi-asset-tsmom-research changed its status vocabulary on 2026-09-13 (commit `8a63ef0`) and closed out on 2026-09-22 (`b8404e7`). So some mismatches below are drift, not original errors. They are still what a reader sees today.

## Scorecard
| Area | Rating | Justification |
|---|---|---|
| Doc-artifact consistency | RED | Several claims are factually wrong: the headline verdict (CONFIRMED vs the repo's SUPPORTED), all three methodological claims in the philosophy line, and two of the "three illusions". The labels are also wrong on 4 overlay verdicts, the spot-MFI "IS" figure and the orderflow "OOS never opened". Every numeric key stat matches a committed artifact except three that exist only in prose: −0.339 R, the 21 in 21/24, and −25R. |
| Traceability | YELLOW | No row cites a commit, file or artifact path, so a reader cannot trace a cell without repeating this audit. |
| Coverage | YELLOW | The public semiconductor-yield-screening repo is not mentioned. Nothing says when each row was last verified. |

## Findings
- [P0] (factual) The headline row's verdict "CONFIRMED" contradicts the linked repo's own status — README.md:11 (and README.md:3, "One confirmed edge") — multi-asset-tsmom-research labels the core "SUPPORTED — NOT INDEPENDENTLY CONFIRMED" (`README.md:42,257-258`, `PROJECT_STATE.md:14`). Its prospective confirmation study has N_scored = 0 (`PROJECT_STATE.md:1349`), and the cross-vendor re-check is on HOLD. This is the highest-stakes cell in the profile, and a reviewer who clicks through sees the mismatch in the repo's first table — change it to "SUPPORTED (CI excludes 0; not independently confirmed; prospective test live since 2026-09-13)" and change line 3 to match (S)
- [P0] (factual) "Pre-register before looking" is false for the one confirmed row — README.md:5 — the repo's own ledgers say "The core had no preregistration" (`research/extensions/TRIAL_LEDGER.md:104,175`, `research/extensions/SAMPLE_REUSE.md:74`). The core code and results arrive in one squashed "fresh repo" commit (`7032780`). Across the portfolio, only commodity-carry-research has server-side evidence that its pre-registration preceded its data (see `00-SUMMARY.md`, systemic issue S2) — scope the claim, e.g. "overlays and later studies pre-registered; the TSMOM core predates the protocol and is being confirmed prospectively" (S)
- [P0] (factual) "BH-FDR across every family" is not true — README.md:5 — the TSMOM core has no multiplicity treatment: a 45-cell robustness grid, 3 aggregation schemes, a no-trade band tried and discarded, and a historical trial count the repo records as UNKNOWN (`research/extensions/TRIAL_LEDGER.md:106-108`). Crash-defense and vol-breakout have no inference at all (`verify_systemic.py:180-198`, `run_breakout_phase1b.py:80-90`). In spot-MFI, the 49-config M2 grid is outside the 42-config family (`run_04_optimize.py:107`) — reword to "BH-FDR within each pre-registered family" and say where none applies (S)
- [P0] (factual) "No-look-ahead proven by truncation-invariance" is contradicted by quant-backtest-framework — README.md:5 — the take-profit target reads swings before they are confirmed (`mtf_smc/strategy/context.py:109`; the auditor's truncation probe found 35 of 320 lookups differ). A pending order is cancelled on the close of a bar that had already traded through the limit (`mtf_smc/engine/backtester.py:123-129`). Neither path has a truncation test (`tests/test_lookahead_primitives.py:32-74`) — remove "proven" until that engine is fixed and re-run, or scope the claim to the repos where it holds (S)
- [P0] (factual) The four overlay verdicts say "FALSIFIED", but the repo relabelled them `not_promoted (rejected at premise)` on 2026-09-13 — README.md:13-16 vs multi-asset-tsmom-research `README.md:241,363,373,388,414` (commit `8a63ef0`) — the profile uses a stronger word than the repo now uses. Crash-defense and breakout were also decided by point comparisons with no inference — use the repo's vocabulary (S)
- [P0] (factual) "a yield-curve 'edge' was entirely the 2022–24 inversion" / "single-episode illusion" is contradicted by the repo's CSVs — README.md:16, README.md:24 — dropping 2022–24 leaves 26–68% of Δ, with the sign preserved in 6/6 cells (`research/yield_spread/yield_spread_premise_family.csv`). The state actually tested ("flat" tercile) has 12–13 episodes, of which 2022–24 is 19–21% (`research/yield_spread/yield_spread_episodes.csv`). The 69%/97% shares exist only in prose. The "edge" was never significant (p_raw 0.18–0.67) — drop it from the "illusions caught" list, or restate it as "a null; 2022–24 explains 31–74% of an insignificant point estimate" (S)
- [P0] (factual) "OOS never opened" is false at code level — README.md:18 — orderflow's headline runner loads the OOS months, runs the detectors on them and computes per-event OOS forward returns in memory before filtering (`runners/phase3_event_study.py:117-143`; `src/orderflow/eventstudy.py:54-57`). OOS event counts are published (`reports/event_counts_by_half_year.csv`). No OOS return statistic was ever written to an artifact — change to "no OOS return statistic computed or reported" (S)
- [P0] (factual) "IS plateau 1.07" is a full-sample figure — README.md:19 — the 1.07 grid spans 2020-05 → 2025-12, and about 82% of that period is the walk-forward OOS window (`src/walkforward.py:28-31`, heatmap titled "full-sample" at `run_04_optimize.py:89`, `output/phase4_optimize.md:5`). The true per-fold IS Sharpes are 1.045–1.764 (`output/phase4_optimize.md:28-32`) — change to "full-sample peak 1.07 → WF OOS 0.29" (S)
- [P0] (factual) "null uniform across ~30 registered variants" and "long/short failure symmetric" do not match commodity-carry-research's artifacts — README.md:21 — the prereg registers 12 constructed variants (14 with the primaries), all run. No artifact contains ~30, and the variants have no CIs (`preregistration/PREREGISTRATION.md:190-205`; `reports/ROBUSTNESS_REPORT.md:13-24`). The long leg earned +1.87%/yr and the short leg −1.82%/yr, which is offsetting, not symmetric failure (`reports/ROBUSTNESS_REPORT.md:179-184`) — change to "all 12 registered variants below the 0.30 gate" and "long-leg gains offset by short-leg losses" (S)
- [P1] (factual) The Monday example is misattributed — README.md:15, README.md:24 — the pooled Monday cell was never significant (p_raw 0.118). The only cell with p < 0.05 (Bond, 0.026) also fails the pre-registered magnitude and stability gates, so BH-FDR is not what kills it (`research/seasonality/seasonality_premise_family.csv`). The only cell where FDR is the sole binding gate is RealEstate (p_raw 0.078) — restate as "1 of 18 raw p < 0.05, as expected under noise; no cell survives BH-FDR" (S)
- [P1] "= same-source alpha" for XSMOM is not established — README.md:17 — the Lo–MacKinlay own-autocovariance term (term1) has a CI containing 0 in 5/5 universes (`research/xsmom/xsmom_universes_decomposition.csv`). Only the +0.42 correlation supports "same source" — change to "+0.42 corr with TSMOM; decomposition cannot separate the terms (only static dispersion ≠ 0)" (S)
- [P1] (factual) "~18bp cost bar" mislabels the materiality bar — README.md:18 — the round-trip cost is 12 bp. 18 bp is the 1.5× materiality bar (`src/orderflow/config.py:74-80`) — change to "18bp materiality bar (1.5× ~12bp costs)" (S)
- [P1] "FALSIFIED / null" merges the prereg's "underpowered" outcome into null — README.md:18 — H3 (N = 62) and H6 (N = 286) fail the 300-event gate, and the prereg forbids conflating that with null (`preregistration/PREREGISTRATION.md:241-243`) — use "null (H1, H2); underpowered (H3, H6)" (S)
- [P1] The spot-MFI benchmark and the Variant A "fake winner" story omit material caveats — README.md:19-20, README.md:24:
  - "B&H 0.46" is perp price ex-funding, not the pre-registered B&H perp net of funding (0.313) (`src/backtest.py:79`; `research/PREREGISTRATION.md:52-54`).
  - Base-study M2 also beat B&H OOS (0.717) and never went through the bootstrap/permutation tests (`output/phase4_optimize.md:41-42`).
  - Variant A was designed after the base OOS results and reused the same OOS windows.
  - "Persistence" and PBO are post-hoc (`run_A5_validate.py:4-5`; `output/phase_B2_pbo.md`).

  Fix: tag the post-hoc items and use the pre-registered failing gates (CI [−0.19, 1.62], perm p = 0.10) as the key stat (S)
- [P1] Two quant-backtest-framework key stats have no committed artifact, and all its stats came from an engine with confirmed look-ahead — README.md:12 — "WF E[R] −0.339 R" and the "21" in "21/24" exist only in docs (`docs/MERGE_REPORT.md:36,46`), because `output/` is git-ignored. The verdict very likely survives, since the biases favour the strategy, but the numbers may move after a fix — mark as "pending re-run on the corrected engine", or commit the derived CSVs after re-running (M)
- [P1] The data-defect sentence presents three catches as settled, but two are only partly guarded in code and one number has no artifact — README.md:26:
  - The 2022-09-06 quarantine window lives in an untracked, hand-made JSON. The loader silently returns `{}` when it is missing (`src/orderflow/quarantine.py:20-22`).
  - The −25R/trade EURUSD figure appears in no committed output. The mechanism reproduces (−49.6 R on a 10-pip stop, −10.96 R on a 50-pip stop), and the regression test `tests/test_instruments_multi.py:58-77` would pass with the old constant on WTIUSD.

  Fix: commit the quarantine window with a fail-closed loader, and say "≈−25R (not preserved)" or commit the buggy-run summary (S)
- [P1] The commodity key stats are consistent with the repo's reports, but the reports themselves are mis-scaled — README.md:21-22 — every Sharpe and CI is annualised with √252 on a series that has one row per Sunday–Friday calendar day (5,031 rows = the exact number of non-Saturday days 2010-06-06 → 2026-06-30; `src/config.py:44`). That scales the figures by about 0.90. The verdict is unaffected — update after the repo's addendum (see `commodity-carry-research.md`) (M, in that repo)
- [P2] No row is traceable without an audit — README.md:9-22 — cells cite no commit hash or artifact path, so drift like the 2026-09-13 relabel goes unnoticed — add a per-row "source" link to the exact report/CSV at a pinned commit, or generate the table from a `headline.json` exported by each repo (M)
- [P2] semiconductor-yield-screening (public, 2026-08-23) is absent from the profile — README.md (whole file) — a finished data-science project is invisible to a reader of the profile — add an "Other work" line (see `00-SUMMARY.md`, Decisions needed) (S)
- [P2] "Negatives reported with the same prominence as positives" is consistent in spirit, but "found by watching output rather than by a passing test" (README.md:26) conflicts with the orderflow README's own wording ("caught by a check that existed specifically to catch it", orderflow `README.md:251-253`) — align the two phrasings (S)

## Verified OK
- All 12 repo links resolve to public repos, and each was cloned at the HEAD listed in `profile-readme-crosscheck.md`.
- All numbers in the table match an artifact, except the three flagged as prose-only (−0.339 R, the 21 of 21/24, −25R):
  - 0.75 / [0.29, 1.23], recomputed.
  - 0.94–0.95 / 0.49, 1.31×, ER Δ ≈ 0, 0/18, 0/6, +0.42, 0/5.
  - 3.45, 0/210, 24 windows.
  - ~2bp, 0/20, 2M reps.
  - 0.29, 0.46, 0/42.
  - 0.68, CI and permutation failures, PBO 0.842, 12,870.
  - −0.0031 [−0.4417, 0.4287], t = 0.30, −0.1255 [−0.5580, 0.3227].
- The 2022-09-06 date matches orderflow `reports/QA_SUMMARY.md:45,57,87,92`.
- The same-ID/revised-quantity repair has a regression test (orderflow `tests/test_backfill.py:96-126`).

## Decisions needed
See `00-SUMMARY.md` §5, items 1–4 and 10.
