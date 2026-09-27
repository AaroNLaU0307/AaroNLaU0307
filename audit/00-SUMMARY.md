# Portfolio audit: summary

**Scope:**
- Profile README: `AaroNLaU0307` @ `7f4f734`.
- multi-asset-tsmom-research @ `b8404e7`
- commodity-carry-research @ `f0847d7`
- semiconductor-yield-screening @ `d64e644`
- spot-mfi-btc-perp-research @ `a90add7`
- orderflow-research-engine @ `e10c588`
- quant-backtest-framework @ `ad6df24`

The audit date is 2026-09-27. It is Phase 1 and read-only. Nothing in any audited repo was modified; the only thing written is this `audit/` directory.

**Files:**
- `00-SUMMARY.md`: this file.
- `profile-readme-crosscheck.md`: every cell of the profile table checked against its artifact.
- One findings file per repo:
  - `AaroNLaU0307.md`
  - `multi-asset-tsmom-research.md`
  - `commodity-carry-research.md`
  - `semiconductor-yield-screening.md`
  - `spot-mfi-btc-perp-research.md`
  - `orderflow-research-engine.md`
  - `quant-backtest-framework.md`

**Findings format:** `[P0|P1|P2] conclusion — path:line — why it is a gap — fix (effort)`.

**Priorities:**
- **P0:** a factual error, a mismatch between doc and artifact, a leakage or look-ahead risk, or a claim with no supporting code.
- **P1:** a reproducibility gap.
- **P2:** a missed best practice.

**Effort:**
- **S:** under half a day.
- **M:** 1–3 days.
- **L:** more than 3 days.

## 1. Method and limits

- **Clones and timelines.** Each repo was cloned with full history, and commit timelines were used to check ordering claims: `git log --follow` on every pre-registration file, compared with the first commit of each results file. Where GitHub Actions run timestamps exist, they were used as server-side evidence.
- **Lightweight checks per repo.**
  - A fresh virtual environment built from `requirements.txt`.
  - `pytest` (every suite passes; the counts are in each file).
  - `ruff`.
  - A scan for secrets, absolute paths and large blobs.
  - Recomputation of headline numbers from committed CSVs, where committed data allowed it.
  - The semiconductor pipeline was re-run end to end, because it is light and its data is committed.
- **No heavy backtests, no data downloads.** None of the runners of the five quant repos were executed. Their inputs are licensed (Databento, HistData), git-ignored (yfinance, FRED, Binance archives) or no longer retrievable (Kraken's rolling 720-candle API). Every number that depends on those inputs is marked "not verified" in the per-repo file.
- **Claims verified directly** from source in addition to the per-repo passes:
  1. The TSMOM Sharpe is 0.7496 over 218 months (from `research/xsmom/xsmom_monthly_returns.csv`).
  2. The TSMOM repo's own label is "SUPPORTED — NOT INDEPENDENTLY CONFIRMED", and it records that there was "no preregistration" for the core.
  3. The pre-registration commits `3378fad` / `0d3c4ae` do not exist.
  4. The `cd-independent-verification` branch is absent from the remote.
  5. The quant-backtest-framework swing gate at `context.py:109` and the invalidation-before-fill order at `backtester.py:123-129`.
  6. The commodity count of 5,031 rows equals exactly the number of non-Saturday days.
  7. The commodity execution window in `expand_monthly_to_daily`.
  8. The orderflow README calls DEVIATIONS.md "empty".
  9. Spot-MFI's `evaluate_grid` is full-sample.
  10. The semiconductor README says 0.57 where the artifact says 0.5306.
- **Citation check.** Every `path:line` citation was checked mechanically: each file exists and each line falls within its length. Every cited range was then re-read by content. One class of error (continuous line numbering across files) was found and corrected before publication.

## 2. Scorecard

Each rating uses the same rule:
- **RED:** at least one P0 in that dimension, or the dimension's central claim cannot be supported from the repo.
- **YELLOW:** P1 gaps only.
- **GREEN:** P2 items only.

| Repo | Validation / leakage | Statistical testing | Pre-registration & ordering | Reproducibility | Engineering & tests | Doc ↔ artifact consistency | Hygiene |
|---|---|---|---|---|---|---|---|
| **Profile README** | — | — | — | — | — | **RED**: the verdict "CONFIRMED" contradicts the repo, all three philosophy claims fail somewhere, and two of the "three illusions" are overstated | — |
| **multi-asset-tsmom-research** | **GREEN**: `shift(1)` at every layer and the vol window ends at the decision close; truncation tests run in CI; overlay states are lagged | **YELLOW**: the core CI reproduces exactly and survives a block bootstrap, but the core has no multiplicity treatment, the yield jackknife rule changed after the first run, and crash-defense and breakout use no inference | **RED**: the repo itself says the core had no pre-registration; the overlay preregs land in the same commit as their results; the yield report cites two non-existent commits | **RED**: no data end date; the price cache and core output are git-ignored; dependencies use `>=` with a broken pandas floor; the headline survives only in an XSMOM CSV | **YELLOW**: real unit tests and green CI, but the extension suites are not in CI, the decision-gate functions are untested, and Windows paths are hard-coded | **RED**: CONFIRMED vs SUPPORTED; "entirely", "one episode", "term1 reliably present" and "demean collapses" all contradict the repo's CSVs; the overlays are FALSIFIED in some places and `not_promoted` in others | **YELLOW**: 5.6 MB of Yahoo prices committed against its own `.gitignore`; about 2.8 MB of governance markdown |
| **commodity-carry-research** | **RED**: the pre-registered one-day execution lag is not implemented (`src/primary.py:109-134`); the "open interest" field is probably cleared volume (`stat_type 6`), not verified against raw data | **RED**: the bootstrap and BH machinery are correct, but every Sharpe, CI and DSR is annualised with √252 on about 313 rows a year (factor ≈0.90) | **YELLOW**: the freeze is server-timestamped before the data pull (the only such case in the portfolio), but the amendments, deviations and results went public together | **YELLOW**: dependencies pinned and data checksummed, but every runner hard-codes `C:\Users\...`, there is no entry point, and results are stored only as markdown | **YELLOW**: 139 substantive tests including defect regressions; the real-data assembly is untested | **RED**: "~30 registered variants" (12 exist), "long/short symmetric" (the legs offset), "every deviation logged before" | **GREEN** |
| **semiconductor-yield-screening** | **YELLOW**: all fitting is on train only (verified); the headline permutation null and CIs assume independence across time | **YELLOW**: BH is correct and pinned to statsmodels; the "decisive" p = 0.0005 becomes about 0.03 under a null that preserves time order | **YELLOW**: commit order is consistent, but all 14 commits fall on one day (three within one second), and holdout label aggregates were visible at Gate 1 | **YELLOW**: a fresh install regenerates every `.md` report byte-identically, but dependencies are unpinned (float drift, a different plotly bundle) | **YELLOW**: 51 tests; spc.py and the cost/threshold code have no unit tests; the determinism test never compares against the committed artifacts | **RED**: the sensor_103 range (0.57 vs 0.53), "every charted sensor" (9 of 10), and the DOE targets sensors the replication discredits | **GREEN** |
| **spot-mfi-btc-perp-research** | **RED**: the hypothesis direction and grid come from a full-sample IC that includes the OOS window; the plateau and DSR gates are scored on full-sample data | **YELLOW**: PSR, DSR, BH, the stationary bootstrap and CSCV are correct and seeded; trial counts omit the 49-config M2 grid; the permutation test runs on gross returns | **RED**: a single squashed root commit; the prereg was edited after the results; registered robustness variants were never run | **RED**: Kraken's rolling 720-candle API makes the analysed data unrecoverable; no snapshot or hash exists | **GREEN**: 59 tests covering lag, truncation, purge and PSR/DSR; CI on 3 OS × 2 Python versions | **RED**: "IS" is really full-sample, 2/7 gates should be 3/7, "78 configs" should be 127 | **GREEN** |
| **orderflow-research-engine** | **YELLOW**: no look-ahead found, but the OOS bars are loaded and processed in memory with no structural guard | **YELLOW**: 0/20 recomputed (min q 0.109, a near miss); a 10k-rep run that flipped H1 is disclosed only in a commit message; the bootstrap label is wrong | **YELLOW**: the prereg revision predates all code and data (manifest times), but nothing is externally timestamped and the repo was created after the results | **YELLOW**: dependencies pinned and a sha256 manifest, but the quarantine JSON, the ETH repair step and the QA logs are uncommitted, so a fresh run would silently differ | **YELLOW**: 104 tests and green CI; the L2 collector discards the snapshot book | **RED**: DEVIATIONS "is empty" (it has 2 entries), "never BH-significant at either rep count", "identical event set per horizon", "OOS never opened" | **GREEN** |
| **quant-backtest-framework** | **RED**: two confirmed look-ahead paths: the TP target uses unconfirmed swings (`context.py:109`), and a bar-close cancel is applied before the fill check (`backtester.py:123-129`) | **YELLOW**: the 210-cell BH family matches the prose and N_eff = 3.451 was recomputed, but the WF sign test assumes the 24 windows are independent | **YELLOW**: the multi-instrument spec was committed before any result and never edited; timestamps are local | **RED**: no result artifacts committed; dependencies use `>=`; no entry point; a hard-coded personal path | **YELLOW**: 141 tests, but there is no truncation test on the leaking path and the end-to-end look-ahead test is skipped in CI | **RED**: "141 passing, CI-verified" (132 run in CI); three different stop-out figures; the L3 conclusion contradicts `REPORT.md`; the high-slippage claim has no run | **YELLOW**: a stale `CLAUDE.md`, dead YAML config, stale package name |

## 3. Prioritised fixes

This section covers the whole portfolio, ranked by stakes, not grouped by repo. The full P0 list comes first. The P1 and P2 tables below are the highest-value items only; each per-repo file has the complete list.

### P0: factual errors, leakage, number or label mismatches

| # | Repo | Location | Fix | Effort |
|---|---|---|---|---|
| 1 | Profile | `README.md:3,11` | Change the headline verdict "CONFIRMED" / "One confirmed edge" to the repo's own label: SUPPORTED, CI excludes 0, not independently confirmed | S |
| 2 | quant-backtest-framework | `mtf_smc/strategy/context.py:109` | Take-profit target look-ahead: gate each swing on the close of bar `s.index + k`, not the swing bar; add a truncation test; re-run the 5 grids and the L1 walk-forward. This touches both "eye-catching" cells and 28 of the 42 configs | M |
| 3 | quant-backtest-framework | `mtf_smc/engine/backtester.py:123-129` | Resolve the fill before the close-based invalidation (the current order drops likely losers); also stop out on the fill bar (`:129-138`, P1); re-run | S + re-run |
| 4 | Profile | `README.md:5` | Scope the philosophy line. "Pre-register before looking" fails for the core, "BH-FDR across every family" fails for the core, crash-defense and breakout, and "proven no-look-ahead" fails for quant-backtest-framework | S |
| 5 | commodity-carry-research | `src/config.py:44`; daily index (`reports/PREMISE_REPORT.md:33`, 5,031 rows) | Drop the Sunday/holiday rows (or key on `ts_ref`) and re-run with a dated addendum. Every Sharpe, CI and DSR is scaled by about 0.90, the "60-day" vol window is about 50 trading days, and the "21-day" block is about 18 | M |
| 6 | commodity-carry-research | `src/primary.py:109-134`; `tests/test_primary.py:177-197` | Prereg §3 promises a full trading day between signal and entry, but the code earns the move from settle(t), the same settlement the signal uses. Either shift one more row, or amend the prereg text and add the lagged version as a sensitivity | S |
| 7 | commodity-carry-research | `src/pipeline.py:93,100`; `docs/DATA_QA_REPORT.md:227-230` | "OI" is `stat_type 6`, which the pinned `databento_dbn` enum defines as CLEARED_VOLUME (OI is 9). Check against CME-published OI for CLK0 2020-04-13..20; if confirmed, log a §11 correction and re-run | M |
| 8 | spot-mfi | `README.md:14-15,35`; `output/REPORT.md:6,47,66`; profile `README.md:19` | Relabel the "in-sample" 1.07 and DSR 0.959/0.961 as full-sample: about 82% of those days are OOS | S |
| 9 | spot-mfi | `run_01_eda.py:35-44`; `research/PREREGISTRATION.md:13-28` | The IC that fixed the direction and the grid was computed on the full sample, OOS included. Disclose this in the limitations section (the bias favours the hypothesis, so the verdict stands) | S |
| 10 | spot-mfi | `output/REPORT.md:61-71` vs `research/PREREGISTRATION.md:57-66` | Rebuild the gate table on the registered gate list: 3 of 7 pass, not 2 of 7 (registered gate 6 was dropped) | S |
| 11 | spot-mfi | `output/phase_B2_pbo.md:67`; `docs/TEST_RATIONALE.md:36` | The configs tested total 127 (42 + 49 + 36), not 78 | S |
| 12 | orderflow | `runners/phase3_event_study.py:117-143`; `README.md:13,162`; profile `README.md:18` | Replace "OOS never opened" with "no OOS return statistic computed or reported" | S |
| 13 | orderflow | `README.md:180-181` | The README says DEVIATIONS.md "is empty"; it has 2 entries, one of which raised a locked value (10k → 2M reps) | S |
| 14 | orderflow | `preregistration/DEVIATIONS.md:24-30` vs commit `e4a5d09` | Disclose that a 10k-rep run with an unseeded `hash()` crossed BH for H1; the log currently says the opposite | S |
| 15 | orderflow | `README.md:223-228`; `src/orderflow/quarantine.py:63-79` | "Identical event set per horizon" is false (H1 N = 4609 vs 4608); correct the prose, or quarantine per event | S |
| 16 | tsmom | `README.md:423-430,732`; `research/README.md:19`; `STUDY_SUMMARY.md:235-239`; `DESIGN_DECISIONS.md:66`; profile `README.md:16,24` | The yield-curve result is not "carried entirely" by 2022–24 (26–68% remains), and the tested state is not "one episode" (12–13 episodes). Restate using the measured share | S |
| 17 | tsmom | `research/yield_spread/PHASE1_PREMISE.md:7-8` | The cited pre-registration commits `3378fad` / `0d3c4ae` do not exist. Link the source repo or remove the hashes | S/M |
| 18 | tsmom | `run_xsmom_universes.py:373,396,512` → `research/xsmom/XSMOM_UNIVERSES_REPORT.md:31,39-43` | "term1 reliably present" and "demean collapses" contradict the CIs in the same report (term1 CI contains 0 in 5/5; the demeaned Sharpe is ≥ baseline in 4/5). Generate these labels from the CIs | S |
| 19 | tsmom | `README.md:241,363,373,388,414` vs profile `README.md:13-16`, `research/README.md:16-19`, `STUDY_SUMMARY.md:222-232` | Standardise the overlay verdicts on `not_promoted` | S |
| 20 | tsmom | `README.md:290-292`; `DESIGN_DECISIONS.md:107-109` | "Dropping CPER/WEAT/CORN cost the headline" has no artifact behind it, and the committed series suggests the opposite (2011-10 onward Sharpe 0.72 vs 0.75). Compute the counterfactual or delete the claim | S |
| 21 | tsmom | `DESIGN_DECISIONS.md:28` | Correct "q = 0.10 in every pre-registration": XSMOM uses α = 0.05 | S |
| 22 | commodity | `README.md:8,158`; `docs/MECHANISM_NOTES.md:29-37`; profile `README.md:21` | "~30 registered variants" should be 12; "long/short symmetric" should be "the long leg's gains offset the short leg's losses" | S |
| 23 | commodity | `README.md:160` vs `DEVIATIONS.md:29-37,71-81` | Retract "every deviation logged before the computation ran": one was logged after a run and one in its results commit, and three were never logged | S |
| 24 | quant-backtest-framework | `README.md:13,31` | "141 passing tests, CI-verified": CI runs 132; 9 need licensed data, including the end-to-end look-ahead test | S |
| 25 | quant-backtest-framework | `docs/REPORT_MULTI_ASSET.md:101`; `DESIGN_DECISIONS.md:57`; `scripts/verify_instruments.py:41-46` | The stop-out verification is given as three different figures, and the only code behind it is one synthetic trade per instrument. Compute it from real trades or relabel it | S |
| 26 | quant-backtest-framework | `README.md:41,81`; `docs/MERGE_REPORT.md:15,90-91,120` vs `docs/REPORT.md:79-88` | The L3 random-entry result says "indistinguishable from random", but its source says the strategy beats both nulls (XAU only). Publish the percentiles and reword | S |
| 27 | quant-backtest-framework | `docs/REPORT.md:126`; `README.md:88` | The high-slippage robustness run does not exist, and "truncation tests on every detector" overstates the coverage. Run them or remove the claims | S–M |
| 28 | semiconductor | `README.md:62` | Change the sensor_103 stratum range from +0.22 to +0.57 to +0.22 to +0.53 (`reports/replication.md:54-57`) | S |
| 29 | semiconductor | `reports/spc.md:123` (hard-coded at `scripts/run_pipeline.py:666-667`); `DECISIONS.md:629-630` | "Holdout OOC higher for every charted sensor" is contradicted by the same table (sensor_103 is lower). It should read 9 of 10 | S |
| 30 | semiconductor | `reports/doe_proposal.md:13-17`; `README.md:291-295` | The DOE targets sensor_059/477/205, which the replication discredits. Retarget it to sensor_103/510 or explain the choice | S |

### P1: reproducibility and evidential gaps (highest value)

| # | Repo | Location | Fix | Effort |
|---|---|---|---|---|
| 31 | tsmom | `run_backtest.py:104-106`; `.gitignore:22`; `src/fetch_data.py:42` | Commit the core `monthly_returns.csv` and report with a sha256; add a `CORE_END_DATE` truncation and a panel-hash assert | S |
| 32 | tsmom | `requirements.txt:8-13`; `config.py:154` | Add a lockfile; `pandas>=2.2` (2.1 rejects "ME"); declare the Python version; reconcile the README's contradictory test status (`README.md:83-86` vs `:342-345`) | S |
| 33 | tsmom | `README.md:276`; `config.py:196`; `STUDY_SUMMARY.md:138,153` | Show the headline at both 2 and 5 bps (5 bps gives 0.70 [0.24, 1.18]); report an excess-return Sharpe using DGS3MO | S–M |
| 34 | tsmom | `research/extensions/TRIAL_LEDGER.md:106-108`; `robustness.py:93-141` | State "not deflated; historical N unknown" beside the CI; optionally add a DSR sensitivity at N ≥ 45 | S |
| 35 | tsmom | `universe.py:85-87` vs `DESIGN_DECISIONS.md:104-106` | XLV/GDX/SLV were dropped below the stated `|r| ≥ 0.80` rule. Codify the trim and commit the screening report | S |
| 36 | tsmom | `research/TSMOM_PROGRAMME_HANDOFF_2026-09.md:111-113` | The branch `cd-independent-verification` does not exist on the remote. Push it or remove the claim | S |
| 37 | tsmom | `run_yield_premise.py:94-96` vs `research/yield_spread/PREREGISTRATION.md:164-166` | The jackknife rule was changed after the first run; under the registered rule 3 of 6 cells pass. Disclose this wherever the jackknife is cited | S |
| 38 | tsmom | `verify_systemic.py:180-198`; `run_breakout_phase1b.py:80-90` | Crash-defense and breakout verdicts have no inference, and their gates were set in the results commit. Add block-bootstrap CIs or soften to "premise not supported" | S–M |
| 39 | commodity | `scripts/*.py` (e.g. `scripts/phase1c_primary_backtest.py:23,30`); `src/config.py:18` | Remove the hard-coded `C:\Users\Aaron\...` paths; add a `make all`; commit the small result CSVs | M |
| 40 | commodity | `src/primary.py:194-206`; `reports/PRIMARY_REPORT.md:22-23,78-79` | Costs feed the vol estimator (H2's 2× cost beats 1×), and daily leverage rescaling is uncosted. Fix, or report cost at fixed leverage | S–M |
| 41 | commodity | `src/stats.py:92-133`; `scripts/phase1c_primary_backtest.py:187-188` | The DSR ignores skew/kurtosis and uses the SR's own standard error as the cross-trial dispersion. Pass the sample moments and state that the implied threshold is SR ≈ 0.76 | S |
| 42 | spot-mfi | `src/data_spot.py:12-14`; `.gitignore` | Kraken's 720-candle window makes a re-pull differ. Publish the raw parquet or a hash manifest with the pull timestamp | S |
| 43 | spot-mfi | `output/phase4_optimize.md:41-42`; `README.md:35` | Base M2 also beat B&H OOS (0.717). Disclose it; Variant A reused the base OOS windows (disclose that too) | S |
| 44 | spot-mfi | `research/PREREGISTRATION.md:1-4,43-46`; `docs/DECISION_LOG.md:120-122,224-226` | The prereg was edited after the results (T grid), registered robustness checks were not run, and the history was squashed. Restore the original text with a dated erratum and list the unrun checks | S |
| 45 | spot-mfi | `config.py:183-186` (never read); `output/REPORT.md:61-71` (hand-typed) | Generate the gate table from the artifacts with code | M |
| 46 | orderflow | `src/orderflow/quarantine.py:20-22`; `.gitignore:4-5` | Commit `quarantine_windows.json` (it is the 2022-09-06 catch) and make the loader fail closed | S |
| 47 | orderflow | `runners/phase2_backfill_gaps.py:82-108` | Commit the runner that applied the ETH 2023-05 same-ID repair; commit the QA JSONL logs | S |
| 48 | orderflow | `collector/depth_recorder.py:132-133` | Persist the REST snapshot book, or the recorded diffs can never rebuild the L2 book (H4) | S |
| 49 | quant-backtest-framework | `.gitignore:28-29`; `requirements.txt:3-9` | Commit derived result CSVs (grid, replication, walk-forward windows); pin dependencies; add a driver script with a git/config hash per row | M |
| 50 | quant-backtest-framework | `scripts/run_legacy_walkforward_report.py:70-72,104-112` | Run the sign test on the 12 calendar periods, not 24 windows treated as independent; headline the block CI | S |
| 51 | semiconductor | `yield_screening/screening.py:332-336`; `README.md:13-17` | Add a time-preserving permutation null as a labelled sensitivity (about p = 0.03); drop "decisive" | M |
| 52 | semiconductor | `requirements.txt:1-16`; `scripts/run_data_qa.py:633` | Pin dependencies and declare Python 3.11; use a stable sort | S |

### P2: best practice (highest value)

| # | Repo | Location | Fix | Effort |
|---|---|---|---|---|
| 53 | all quant repos | see S5 below | Consolidate or hash-pin the statistics helpers (7 BH implementations with 2 different defaults) | M |
| 54 | tsmom | `.github/workflows/tests.yml:23` | Run the `*_tests.py` extension suites in CI | S |
| 55 | tsmom | `data/xsmom_universes_prices.csv` vs `.gitignore:14-16` | Resolve the committed Yahoo data against the repo's own licensing rule | S |
| 56 | tsmom / quant-backtest-framework / spot-mfi | `src/validation.py:82-108`; `mtf_smc/robustness/walkforward.py:41` | Rename fixed sub-period tables from "walk-forward" to "sub-period stability" | S |
| 57 | quant-backtest-framework / spot-mfi | `CLAUDE.md` (stale: "not a git repo yet", "win32") | Remove the agent-scaffolding files from the public repos; trim the environment disclosures in spot `docs/DECISION_LOG.md:10-13` | S |
| 58 | orderflow / quant-backtest-framework / commodity | `README.md` test counts | Bring the stated test counts in line with what CI runs (97/104, 132/141, 139 vs "136") | S |
| 59 | tsmom, commodity, orderflow, semiconductor | `.python-version` / `requires-python` | Declare the Python version in-repo; it currently appears only in CI YAML | S |

## 4. Cross-repo systemic issues

**S1. There is no single source of truth between the profile and the repos, so the labels drift.**
- The profile says CONFIRMED where the repo says SUPPORTED, FALSIFIED where the repo says `not_promoted`, and "IS" where the figure is full-sample.
- The profile was last edited on 2026-08-19; the TSMOM repo changed its vocabulary on 2026-09-13.
- Several P0s are the same fact stated differently in different places. The TSMOM repo itself says "confirmed" in 11 places (`README.md:725,736`, `STUDY_SUMMARY.md:116,185,210,218`, `run_backtest.py:132,159`, …) while its governing status says SUPPORTED.
- **Fix:** each repo exports a small `results/headline.json` (verdict, key stats, artifact path, commit). A script renders the profile table from those files, or CI checks the table against them.

**S2. Pre-registration timing is self-attested almost everywhere.**
- **commodity-carry-research:** the only repo where server-side evidence (GitHub Actions run 2026-07-09T18:56Z) shows the freeze preceded the data pull (23:03Z).
- **orderflow-research-engine:** ordering is internally consistent, backed by data-manifest ingestion times, but the GitHub repo was created after every results commit.
- **quant-backtest-framework:** the spec was committed before any multi-instrument result, but only local timestamps exist.
- **multi-asset-tsmom-research:** the core had no pre-registration; the overlay preregs arrive in the same commit as their results, as "file copies"; the yield report cites commits that do not exist.
- **spot-mfi-btc-perp-research:** one deliberately squashed root commit.
- **semiconductor-yield-screening:** all 14 commits on one day, three within one second.
- **Fix:** before downloading any data, push a signed tag (or register on OSF/AsPredicted) for each future study. For past studies, add one honest sentence per README on what the history can and cannot show.

**S3. Many headline numbers have no committed machine-readable artifact.**
- `output/` is git-ignored in multi-asset-tsmom-research (the core) and in quant-backtest-framework (all results).
- commodity-carry-research and spot-mfi-btc-perp-research keep results as markdown only.
- orderflow-research-engine and semiconductor-yield-screening commit CSVs.
- **Fix:** commit derived statistics (never vendor data) with a sha256 manifest, and point every README number at a file.

**S4. Conclusions are hand-typed inside report generators and go stale silently.**
- `run_xsmom_universes.py:373,396,512` (TSMOM)
- `runners/phase3_event_study.py:410` and `runners/phase5_final_report.py:104` (orderflow)
- `scripts/run_pipeline.py:666-667` (semiconductor)
- the hand-built gate tables in spot-mfi
- the hard-coded constants in commodity's reproduction gate

At least five of the P0s above originate here. **Fix:** compute every sentence that asserts a result from the result, or move it into hand-written notes that do not claim to be generated.

**S5. The statistics helpers are copy-pasted and have drifted.**
- **BH:** implemented 7 times. Four use q = 0.10 (TSMOM `src/seasonality.py:253`, commodity `src/stats.py:147`, orderflow `src/orderflow/stats.py:239`, semiconductor `yield_screening/screening.py:139`). Three use α = 0.05 (TSMOM `src/xsmom_stats.py:40`, spot `src/stats.py:77`, quant-backtest-framework `mtf_smc/robustness/stats.py:106`).
- **Sharpe annualisation:** √12 (TSMOM, monthly), √252 on a 6-day calendar (commodity), √365 (spot), √252 on forward-filled realised equity (quant-backtest-framework, not mark-to-market).
- **Sharpe CI methods:**
  - iid bootstrap: TSMOM core, quant-backtest-framework, semiconductor.
  - stationary bootstrap: commodity, spot, the TSMOM extensions.
  - day-cluster bootstrap: orderflow, which is mislabelled as "stationary".
- **Bootstrap p-values:** several are not null-centred (TSMOM `src/xsmom_stats.py:130`, commodity inline, quant-backtest-framework `stats.py:34`).
- **Vendored copies:** spot vendors its PSR/DSR/BH from TSMOM's `xsmom_stats.py`.
- **Fix:** a small shared, versioned package, or vendored copies carrying a provenance header and a hash test. Sealed studies can pin an old version. Pick one CI method per series type and justify it.

**S6. Look-ahead tests cover primitives, not composite decision paths.**
- quant-backtest-framework has truncation tests on ATR, EMA, swings, FVG and structure, but not on the take-profit target, the TFView lookups or the order-resolution loop. That is where the two leaks are.
- TSMOM's tests use one cut point on synthetic common-inception data.
- orderflow's `tests/test_truncation_invariance.py` truncates the inputs of all four detectors at several cut points and compares the full event sets. It is the model to copy.
- **Fix:** an end-to-end prefix-invariance test per engine over several cut points, running in CI on a synthetic fixture.

**S7. Reproducibility basics are inconsistent.**
- **Unpinned dependencies (`>=`):** TSMOM, quant-backtest-framework, semiconductor.
- **Python version declared only in CI YAML:** tsmom, commodity, orderflow, semiconductor (quant-backtest-framework has `requires-python = ">=3.11"`; the spot README states 3.12+).
- **Hard-coded Windows paths:** TSMOM extensions, every commodity runner, one quant-backtest-framework script.
- **No single entry point:** every repo. semiconductor comes closest.
- **Data snapshot gaps:** spot (Kraken), TSMOM (no end date), quant-backtest-framework (no HistData checksums), orderflow (untracked quarantine config).

**S8. Stated test counts do not match what CI runs.**

| Repo | Stated | Actually runs |
|---|---|---|
| quant-backtest-framework | "141, CI-verified" | 132 |
| orderflow-research-engine | "104 passed" | 97 on a fresh clone |
| commodity-carry-research | "136" | 139 |
| multi-asset-tsmom-research | contradictory statements, both current | 98/3 or 101/0 depending on pandas |

**S9. Post-hoc diagnostics are presented as tests in the profile.**
- spot-MFI "per-fold persistence" and PBO.
- commodity "long/short" decomposition.
- The TSMOM yield "episode shares", which no code computes.
- The repos usually label these post-hoc; the profile drops the label.

**S10. "Walk-forward" is used for fixed sub-period tables with no refit.**
- TSMOM core `src/validation.py:82-108`, reused for XSMOM.
- quant-backtest-framework `mtf_smc/robustness/walkforward.py:41`.
- Only spot-MFI's walk-forward and quant-backtest-framework's L1 actually refit.

**S11. Agent scaffolding and local-environment detail are in the public repos.**
- A stale `CLAUDE.md` in quant-backtest-framework ("Not a git repo yet", "win32") and in spot-mfi.
- Agent-session prose in TSMOM `qros-state.yaml` and `PROJECT_STATE.md` (about 200 KB).
- Environment-variable and private-sibling-repo names in spot `docs/DECISION_LOG.md:10-13`.
- None is a secret. A clean public face would drop them.

**Hygiene is otherwise clean:**
- No credentials found in any repo or history.
- No raw vendor data committed, except TSMOM's 5.6 MB Yahoo CSV.
- MIT LICENSE everywhere.
- CI green on HEAD for tsmom, commodity, orderflow and quant-backtest-framework, checked through the Actions API. Not checked live for spot or semiconductor.

## 5. Decisions needed

These are judgment calls the evidence could not settle. Each lists the options and a recommendation. Several would change what a README says or what number is reported, so they need the owner's approval before Phase 2.

1. **The profile verdict for the TSMOM core.**
   - (a) Keep "CONFIRMED".
   - (b) "SUPPORTED: CI excludes 0; not independently confirmed".
   - (c) (b) plus "prospective test live since 2026-09-13".

   **Recommend (c).** (a) contradicts the repo's own governing label, and a reviewer sees it immediately.
2. **The philosophy line** (`README.md:5`).
   - (a) Delete it.
   - (b) Scope it to where it holds, e.g. "later studies pre-registered; BH-FDR within each registered family; truncation-invariance tests".

   **Recommend (b).** It stays distinctive and becomes true.
3. **The overlay verdict vocabulary.** Keep FALSIFIED, or match the repo's `not_promoted (rejected at premise)`. **Recommend matching the repo.** It drew the distinction deliberately in commit `8a63ef0`, and crash-defense and breakout have no inference to support "falsified".
4. **The "three statistical illusions" sentence.** Yield ("entirely") is contradicted by the CSVs; Monday is misattributed to BH-FDR; the fake winner is accurate but omits the reused OOS window and a second OOS winner (M2).
   - (a) Fix the wording in place.
   - (b) Keep only the fake winner, with its caveats, plus a correctly worded multiple-testing example (RealEstate, or "1 of 18 raw p < 0.05 as expected").

   **Recommend (b).**
5. **quant-backtest-framework: fix the engine and re-run, or disclose only.** Fixing both look-ahead paths changes the reported numbers, which this audit may not touch.
   - (a) Fix and re-run with the licensed data, publishing an addendum.
   - (b) Keep the numbers and add a limitations note.

   **Recommend (a).** Until then, remove "proven zero look-ahead" from the README and the profile. The verdict very likely stays FALSIFIED because the biases favour the strategy.
6. **commodity-carry-research: calendar, OI field and execution lag.**
   - (a) Verify `stat_type` against raw data, fix the calendar, and re-run all phases under a dated addendum.
   - (b) Disclose the √(252/313) scaling and the volume-based roll without re-running.

   **Recommend (a) for the calendar and OI.** For the execution lag, amending the prereg text to the same-settlement convention (which the code follows) and adding the lagged version as a sensitivity is defensible. Both approaches change the reported numbers, so they need owner approval.
7. **The TSMOM headline cost basis.** 2 bps (0.75) or the repo's own "realistic" 5 bps (0.70 [0.24, 1.18]). **Recommend showing both side by side.** Commit the turnover series first, so the 5 and 20 bps numbers become verifiable; they were not verified here.
8. **The spot-MFI profile key stats.** Relabel "IS 1.07" as full-sample (required). For Variant A, keep "PBO 0.84" tagged as post-hoc, or switch to the pre-registered failing gates (CI [−0.19, 1.62], permutation p = 0.10). **Recommend the pre-registered gates.** The repo itself says PBO "cannot revise either verdict".
9. **The orderflow verdict label.** "FALSIFIED / null", or the prereg's taxonomy "null (H1, H2); underpowered (H3, H6)". **Recommend the taxonomy.** The prereg forbids merging the two outcomes.
10. **semiconductor-yield-screening in the profile.**
    - (a) Leave it out; it is not a trading hypothesis.
    - (b) Add a separate "Other work" line.
    - (c) Add a row to the table.

    **Recommend (b).** It is the most reproducible repo in the portfolio and is currently invisible.
11. **Committing derived artifacts under vendor licences** (HistData, Databento, Yahoo). **Recommend committing derived statistics only**: per-config tables, monthly strategy returns and walk-forward window arrays. Remove or justify the raw Yahoo price file in TSMOM. Check each vendor's terms first; these were not reviewed.
12. **The statistics-helper consolidation model.** A shared versioned package, or vendoring with provenance headers and hash tests. **Recommend vendoring with hash tests.** The sealed TSMOM extensions need immutable code, and a shared package would break that.
13. **The TSMOM governance volume** (`PROJECT_STATE.md` 121 KB, `qros-state.yaml` 81 KB, about 2.8 MB of markdown). Keep it on `main`, or move it to `docs/governance/` or a branch. **Recommend moving it**, so the core table, its caveats and the tests are what a reviewer sees first.
14. **`CLAUDE.md` in public repos and the co-author trailers.** **Recommend deleting the stale `CLAUDE.md` files and keeping the history as it is.** Never rewrite history.
15. **Disclosing the orderflow 10k-rep seed flip** (commit `e4a5d09`). **Recommend disclosing it** in DEVIATIONS and the README, together with "smallest BH-adjusted q = 0.109". It turns an undisclosed near miss into evidence of good process.
