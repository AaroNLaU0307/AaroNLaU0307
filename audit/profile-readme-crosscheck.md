# Profile README cross-check

This file checks every cell of the summary table in `README.md`, plus the prose claims around it, against the artifacts in the linked repos.

- **Profile README audited:** `README.md` at `7f4f734` (last edited 2026-08-19).
- **Linked repos audited at:**
  - multi-asset-tsmom-research `b8404e7`
  - quant-backtest-framework `ad6df24`
  - orderflow-research-engine `e10c588`
  - spot-mfi-btc-perp-research `a90add7`
  - commodity-carry-research `f0847d7`
- **Status values:**
  - **consistent**: the profile matches the artifact.
  - **inconsistent**: they differ. Both values are quoted.
  - **no supporting artifact found**: the claim appears only in prose. It may be in a README, but no committed CSV, generated report or code output backs it.
  - **consistent (caveat)**: the number matches, but a label or scope is misleading. The caveat is stated.
- **Paths:** every path is relative to the named repo's root.

Numbers were recomputed where the committed data allowed it. Otherwise they were read from committed generated reports. The TSMOM headline (0.7496, CI [0.2919, 1.2294]) was recomputed independently from `research/xsmom/xsmom_monthly_returns.csv`, column `tsmom_net`.

---

## Row 1: Multi-asset TSMOM core (17 ETFs, vol-targeted), `README.md:11`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Hypothesis: "17 ETFs, vol-targeted" | 17 ETFs | consistent | `universe.py` (final 17), `src/sizing.py:60,73` (60-day vol target), `src/portfolio.py:152-187` |
| Repo link | multi-asset-tsmom-research | consistent (resolves) | — |
| Verdict | **CONFIRMED** | **inconsistent.** The repo says "SUPPORTED — NOT INDEPENDENTLY CONFIRMED". The prospective confirmation study has N_scored = 0, and the cross-vendor re-check is on HOLD. | `README.md:42`, `README.md:257-258`, `PROJECT_STATE.md:14`, `PROJECT_STATE.md:1349`, `research/TSMOM_PROGRAMME_HANDOFF_2026-09.md:108-109` |
| Mechanism: "diversified trend premium" | — | consistent (descriptive only). Sleeve Sharpes are 0.60 / 0.69 / 0.55 / 0.18 / 0.11, all below the portfolio's 0.75. | `research/diagnostic/DRAWDOWN_ATTRIBUTION_REPORT.md:32-38` |
| Mechanism: "crisis alpha in 2008/2020" | — | consistent (caveat). It is raw cumulative return over two hand-picked windows, not a benchmark-adjusted alpha: GFC +11.58% vs buy-and-hold −27.40%, COVID +7.28% vs −13.04%. The 2009 momentum-crash window (−4.5%) is in the same CSV but not mentioned. | `research/xsmom/xsmom_crisis_windows.csv` rows "GFC 2008", "COVID 2020", "Mom-crash 2009"; windows at `config.py:210-214` |
| Key stat: net Sharpe | 0.75 | consistent (caveats below) | Recomputed 0.7496 from `research/xsmom/xsmom_monthly_returns.csv` col `tsmom_net`, 218 rows (2008-05-31 → 2026-06-30) |
| Key stat: 95% CI lower | 0.29 | consistent | Recomputed 0.2919 (iid percentile bootstrap, 10k, seed 7, `src/validation.py:20`) |
| Key stat: 95% CI upper | 1.23 | consistent | Recomputed 1.2294 |

Caveats on the key stat:
- **Cost basis.** 0.75 is at 2 bps. The repo calls 5 bps "realistic", and at 5 bps the figures are 0.70 [0.24, 1.18] (`STUDY_SUMMARY.md:138,153`; `rp_comparison.py:29`).
- **No committed pipeline output.** The core pipeline's own output (`output/monthly_returns.csv`) is git-ignored (`.gitignore:22`). The only committed copy of the series is an XSMOM output file.
- **Partial month.** The 218 months include a partial June 2026. On 217 complete months the figures are 0.751 [0.31, 1.23].
- **Risk-free rate.** rf = 0, and there is no financing on up to 3× gross exposure (`config.py:184,201`).

## Row 2: MTF SMC price action replicates across instruments, `README.md:12`

**Repo:** quant-backtest-framework

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Repo link | quant-backtest-framework | consistent (resolves) | — |
| Verdict | FALSIFIED | consistent with docs; **not verified against a clean engine**. Two confirmed look-ahead paths favour the strategy (`mtf_smc/strategy/context.py:109`, `mtf_smc/engine/backtester.py:123-129`), so the verdict very likely survives a re-run, but the reported numbers were produced by the leaking engine. | `docs/REPORT_MULTI_ASSET.md:16-21`, `docs/REPLICATION.md:87-89` |
| Mechanism: "eye-catching single-instrument cells are small-sample noise" | — | partly supported. The gold +2.00 R cell has N = 17 and CI [−0.63, +5.27]. No committed artifact gives N for the GBPJPY +1.05 R cell. Both are HTF_level configs, which is exactly the path affected by the take-profit look-ahead. | `docs/REPORT.md:70-71`, `docs/REPLICATION.md:25-31` (`output/replication/replication_grid_N.csv` is git-ignored) |
| Mechanism: "effective 3.45/5 independent instruments" | 3.45 | consistent. It is an eigenvalue participation ratio, recomputed as 3.451 from the published matrix. | `mtf_smc/robustness/replication.py:24-35`, `docs/REPLICATION.md:13-21` |
| Key stat: 0/210 BH-FDR | 0/210 | consistent. Generated markdown is the only artifact; there is no CSV. The family of 42 configs × 5 instruments matches the prose. | `docs/REPLICATION.md:9` (written by `scripts/run_replication.py:113-114`); `mtf_smc/robustness/replication.py:127-142` |
| Key stat: WF E[R] | −0.339 R | **no supporting artifact found.** It appears only in docs, which agree with each other. The generating script's output `output/legacy_walkforward/walkforward_summary.txt` is git-ignored. | `README.md:20,77`, `docs/MERGE_REPORT.md:36`; script `scripts/run_legacy_walkforward_report.py:86` |
| Key stat: 21/24 windows < 0 | 21/24 | 24: consistent (12 windows × 2 instruments, `scripts/run_legacy_walkforward.py:50,72-81`). 21: **no supporting artifact found** (docs only). | `docs/MERGE_REPORT.md:46` |

## Row 3: Crash-defense overlay, `README.md:13`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED (Phase 0) | **inconsistent (label).** The repo status is `not_promoted (rejected at Phase 0)`, relabelled on 2026-09-13 (after the profile's last edit). The artifact verdict is "Systemic claim NOT confirmed", from a descriptive comparison with no inference. | `README.md:363`, commit `8a63ef0`; `research/crash_defense/PHASE0_SYSTEMIC_VERIFICATION.md:46-48` |
| Mechanism: "trigger anti-aligned with realized drawdown regimes" | — | consistent (descriptive only). There is no CI, and only 2 crisis windows are used (7 and 3 month-ends vs 47 drawdown months). | `research/crash_defense/PHASE0_SYSTEMIC_VERIFICATION.md:37-42`; `verify_systemic.py:180-187` |
| Key stat: 0.94–0.95 | 0.94–0.95 | consistent. `port_vol_pctile` is GFC 0.95 and COVID 0.94 (causal trailing rank). | `PHASE0_SYSTEMIC_VERIFICATION.md:40-41`; `src/regime.py:31-36` |
| Key stat: "in 2008/2020" | — | consistent | `config.py:210-214` |
| Key stat: 0.49 in real drawdowns | 0.49 | consistent | `PHASE0_SYSTEMIC_VERIFICATION.md:39` |

## Row 4: Vol-compression breakout overlay, `README.md:14`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED (Phase 1B) | **inconsistent (label).** The repo status is `not_promoted`. The +0.02 decision bar was written in the results commit, and the verdict uses point estimates only. | `README.md:373`; `research/vol_breakout/BREAKOUT_PHASE1B_PREMISE.md:51`; `run_breakout_phase1b.py:80-90` (commit `12429da`) |
| Mechanism: "mechanical artifact; no directional premise" | — | consistent with the report. The "mechanical artifact" explanation is untested (no null simulation). | `BREAKOUT_PHASE1B_PREMISE.md:49-53` |
| Key stat: vol expansion ~1.31× | 1.31× | consistent (caveat). `expansion_comp` = 1.3107, but the unconditional base is 1.0989, so the expansion relative to base is ≈1.19×. | `research/vol_breakout/breakout_premise_by_sleeve.csv` row `Pooled,10,0.2` |
| Key stat: efficiency-ratio Δ≈0 | ≈0 | consistent (ER_delta +0.0040) | same row, col `ER_delta` |

## Row 5: Seasonality / calendar overlay, `README.md:15`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED | **inconsistent (label).** The repo status is `not_promoted (rejected at premise, 0/18)`. | `README.md:388` |
| Mechanism: "headline Monday effect = multiple-testing false positive, caught" | — | **inconsistent (overstated).** The pooled ("headline") Monday cell was never significant (p_raw 0.118). The one sub-0.05 cell (Bond, p_raw 0.026) also fails the pre-registered magnitude (3.41 < 5 bps) and stability gates, so BH-FDR is not what rejects it. BH-FDR is the sole binding gate only for the RealEstate cell (p_raw 0.078). | `research/seasonality/seasonality_premise_family.csv` rows `E3_Monday/*` (cols `p_raw`, `pass_magnitude`, `pass_stable`, `p_bh`) |
| Key stat: 0/18 BH-FDR | 0/18 | consistent. `fdr_reject` is False in all 18 rows. The recomputed minimum p_BH is 0.476, and the family in code (`src/seasonality.py:284-318`) equals the prereg family. | same CSV; `research/seasonality/PREREGISTRATION.md:133` |

## Row 6: Yield-curve regime overlay, `README.md:16`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED | **inconsistent (label).** The repo status is `not_promoted (rejected at premise, 0/6)`. | `README.md:414` |
| Mechanism: "single-episode illusion — 2022–24 dominates inverted-curve days" | — | **no supporting artifact found / overstated.** The 69% / 97% inverted-day shares are prose only; no code or CSV produces them. The state actually tested (tercile "flat") has 12–13 episodes, and 2022–24 is only 19–21% of its observations. Dropping 2022–24 leaves 26–68% of Δ, with the sign preserved in 6/6 cells. The jackknife "fail" depends on a binding-episode rule changed after the first run: under the pre-registered rule, 3/6 cells pass the jackknife. | `research/yield_spread/PREREGISTRATION.md:24-27`; `research/yield_spread/yield_spread_episodes.csv` (`n_flat_obs`); `research/yield_spread/yield_spread_premise_family.csv` (`delta_ann`, `delta_drop_2022_24_ann`); `run_yield_premise.py:94-96` vs `PREREGISTRATION.md:164-166` |
| Key stat: 0/6 cells | 0/6 | consistent. `CONFIRMED` is False ×6 and p_fdr is 0.602–0.673. | `yield_spread_premise_family.csv` |

## Row 7: XSMOM adds an independent edge, `README.md:17`

**Repo:** multi-asset-tsmom-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED | consistent (the repo status is `falsified`) | `README.md:249`; `research/xsmom/xsmom_universes_map.csv` col `confirmed` |
| Mechanism: "+0.42 corr with TSMOM" | +0.42 | consistent (0.419 recomputed) | `research/xsmom/xsmom_monthly_returns.csv` corr(`xsmom_tercile_net`, `tsmom_net`) |
| Mechanism: "= same-source alpha" | — | **not established.** The Lo–MacKinlay own-autocovariance term (term1) has a CI containing 0 in 5/5 universes, so the decomposition does not show the shared source. The repo's own text claiming "term1 reliably present" is contradicted by its CIs. The per-universe corr vs TSMOM in Phase 2 is only +0.12 to +0.31. | `research/xsmom/xsmom_universes_decomposition.csv`; `research/xsmom/XSMOM_UNIVERSES_REPORT.md:31` (text hard-coded at `run_xsmom_universes.py:373,512`); `xsmom_universes_map.csv` col `corr_vs_tsmom` |
| Mechanism: "lead-lag term never shown non-trivial" | — | consistent (term2 CI brackets 0 for U1–U5) | `xsmom_universes_decomposition.csv` col `term2_leadlag` |
| Key stat: 0/5 universes | 0/5 | consistent (`confirmed` False ×5, min q 0.357) | `xsmom_universes_map.csv` |

## Row 8: Order-flow footprint signals, `README.md:18`

**Repo:** orderflow-research-engine

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Hypothesis: "4 tested; 2 data-blocked, disclosed" | 4 + 2 | consistent. (The repo's GitHub description still says "six classic order-flow signals".) | `README.md:21-33`; `preregistration/PREREGISTRATION.md:250-278`; `reports/event_study_btc_gates.csv` |
| Repo link | orderflow-research-engine | consistent (resolves) | — |
| Verdict | FALSIFIED / null | **partly inconsistent.** H1 and H2 are informational nulls. H3 (N = 62) and H6 (N = 286) fail the 300-event gate, which the prereg classifies as "underpowered" and explicitly forbids merging with null. | `reports/event_study_btc_gates.csv`; `preregistration/PREREGISTRATION.md:241-243`; `README.md:35-38` |
| Mechanism: "best cell ~2bp gross" | ~2 bp | consistent (caveat). The H1 h=12 mean is 2.1207 bp. "Best" means best among cells with raw p < 0.05; the highest raw mean is H3 h=48 at 16.0 bp (N = 62), excluded as noise. | `reports/event_study_btc_cells.csv` row `H1,12` col `observed_mean_bp`; `runners/phase5_final_report.py:93-94` |
| Mechanism: "~18bp cost bar" | 18 bp cost | **inconsistent (label).** 18 bp is the materiality bar, 1.5× the cost. The round-trip cost is 12 bp (2 × (5 bp taker + 1 bp impact)). | `src/orderflow/config.py:74-80` |
| Mechanism: "OOS never opened" | never opened | **inconsistent at code level.** The headline runner loads all 48 months, runs the detectors on OOS bars, and computes per-event OOS forward returns in memory before filtering to IS. OOS event counts are published. No OOS return statistic reaches any committed artifact, so there is no leakage into results, but "never opened" is not what the code does. | `runners/phase3_event_study.py:117-143`; `src/orderflow/eventstudy.py:54-57`; `reports/event_counts_by_half_year.csv`; `reports/event_study_btc.md:15-20` |
| Key stat: 0/20 BH-FDR | 0/20 | consistent (independently recomputed; smallest BH-adjusted q = 0.109, so a near miss) | `reports/event_study_btc_cells.csv` col `bh_significant_q10`; `runners/phase3_event_study.py:96-111` |
| Key stat: "2M-rep bootstrap" | 2M | consistent. It applies to the 20 primary cells only. The method is a day-cluster (iid over event-days) bootstrap, not the "stationary block bootstrap" the repo README calls it. 10k → 2M is a logged deviation. | `src/orderflow/config.py:94`; `src/orderflow/stats.py:32-74`; `preregistration/DEVIATIONS.md:9-46` |

## Row 9: Spot-MFI momentum predicts perp returns, `README.md:19`

**Repo:** spot-mfi-btc-perp-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Repo link | spot-mfi-btc-perp-research | consistent (resolves) | — |
| Verdict | FALSIFIED | consistent | `output/REPORT.md:3,83` |
| Mechanism: "IC≈0" | ≈0 | consistent (caveat). The non-overlapping IC is insignificant (p 0.30–0.46), but the point estimates run 0.021–0.086. The IC is also computed with one day more lag than the traded signal. | `output/phase1_eda.md:28-33`; `src/eda.py:23-25,45,57` vs `src/backtest.py:47-48` |
| Mechanism: "before any strategy" | — | **no supporting artifact found.** The pre-registration, results and reports share one root commit (`50d3f2f`), deliberately squashed (`docs/DECISION_LOG.md:224-226`). The ordering is self-attested only. | `research/PREREGISTRATION.md:1-4` |
| Mechanism: "IS plateau 1.07" | IS 1.07 | number consistent (1.070); **label inconsistent.** 1.07 is the peak of a full-sample grid (2020-05 → 2025-12, about 82% of which is the walk-forward OOS window), not an in-sample figure. The true per-fold IS Sharpes are 1.045–1.764. | `output/phase4_optimize.md:5,12,19,28-32`; `src/walkforward.py:28-31`; `run_04_optimize.py:89` |
| Mechanism: "OOS 0.29" | 0.29 | consistent (0.294) | `output/phase4_optimize.md:35`; `output/phase5_validation.md:17` |
| Mechanism: "< B&H 0.46" | 0.46 | consistent (caveat; 0.457). This benchmark is perp open-to-open price return ex-funding, labelled "spot". The pre-registered benchmark (B&H perp net of funding) is 0.313. | `output/phase4_optimize.md:36`; `src/backtest.py:79`; `research/PREREGISTRATION.md:52-54` |
| Key stat: 0/42 BH-FDR | 0/42 | consistent. The family is 42 M1 configs, full-sample, one-sided PSR p for SR > 0. The 49-config M2 grid was also run and is not in the family. | `output/phase5_validation.md:13`; `run_05_validate.py:43-47`; `run_04_optimize.py:107` |

## Row 10: MFI × funding divergence (Variant A), `README.md:20`

**Repo:** spot-mfi-btc-perp-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | INCONCLUSIVE, leaning FALSIFIED | consistent | `output/REPORT_variantA.md:3,64` |
| Mechanism: "fake winner: OOS 0.68" | 0.68 | consistent (0.684). The OOS windows are the same ones already viewed in the base study, and Variant A was designed after them. | `output/variantA_phase4.md:23-31` vs `output/phase4_optimize.md:28-32`; `output/REPORT.md:102-103` |
| Mechanism: "beat B&H" | — | consistent (caveat). It is not unique: base-study M2 also beat B&H OOS (0.717 vs 0.457) and never went through the bootstrap/permutation tests. | `output/variantA_phase4.md:31`; `output/phase4_optimize.md:41-42` |
| Mechanism: "failed bootstrap CI" | — | consistent (CI [−0.194, 1.619]) | `output/variantA_phase5.md:13` |
| Mechanism: "failed permutation" | — | consistent (caveat; p = 0.100). The permutation runs on gross returns (observed 0.768), not the net 0.684 series. | `output/variantA_phase5.md:14`; `src/stats.py:157-177` |
| Mechanism: "failed persistence" | — | consistent qualitatively. It is a post-hoc diagnostic with no pass/fail threshold and not a registered gate. | `run_A5_validate.py:4-5,51-52`; `research/PREREGISTRATION_variantA.md:52-59` |
| Key stat: PBO 0.84 | 0.84 | consistent (0.842). It is post-hoc; the repo README says so, the profile row does not. | `output/phase_B2_pbo.md:44` |
| Key stat: "via CSCV" | — | consistent | `src/pbo.py:75-121` |
| Key stat: 12,870 splits | 12,870 | consistent (S = 16, C(16,8) = 12,870, all valid) | `run_B2_pbo.py:41`; `output/phase_B2_pbo.md:44,52` |

## Row 11: Cross-sectional commodity carry (18 CME futures), `README.md:21`

**Repo:** commodity-carry-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Hypothesis: "18 CME futures" | 18 | consistent | `README.md:1,6,11` |
| Repo link | commodity-carry-research | consistent (resolves) | — |
| Verdict | FALSIFIED | consistent (repo label "NOT PROMOTED", all gates FAIL) | `reports/PRIMARY_REPORT.md:35-43,129` |
| Mechanism: "no confirmable edge net of costs" | — | consistent | `reports/PRIMARY_REPORT.md:16-18,37-41` |
| Mechanism: "null uniform across ~30 registered variants" | ~30 | **inconsistent.** The prereg registers 12 constructed variants (14 with the primaries), and all were run. Figure 1 plots 26 points and the figure script says ~28. No artifact has ~30. "Uniform" is also overstated: H1 variants range from −0.19 to +0.22, and they have no CIs. | `preregistration/PREREGISTRATION.md:190-205`; `reports/ROBUSTNESS_REPORT.md:13-24`; `scripts/generate_readme_figures.py:72-99` |
| Mechanism: "long/short failure symmetric" | — | **inconsistent (substance).** The long leg made +1.87%/yr and the short leg lost −1.82%/yr, so they offset each other rather than both failing. The decomposition is unregistered and has no CI. | `reports/ROBUSTNESS_REPORT.md:179-184` |
| Key stat: net Sharpe | −0.003 | consistent (−0.0031); see the annualisation caveat below | `reports/PRIMARY_REPORT.md:16` |
| Key stat: CI lower | −0.44 | consistent (−0.4417) | `reports/PRIMARY_REPORT.md:17` |
| Key stat: CI upper | 0.43 | consistent (0.4287) | `reports/PRIMARY_REPORT.md:17` |

Annualisation caveat, applying to rows 11 and 12: the daily index includes every Sunday–Friday calendar day. 5,031 rows equals exactly the non-Saturday days from 2010-06-06 to 2026-06-30. Annualisation still uses √252 (`src/config.py:44`), so every reported Sharpe and CI is scaled by about √(252/313) ≈ 0.90. The verdict is unaffected, but the published numbers are mis-scaled.

## Row 12: Time-series commodity carry (own-basis sign), `README.md:22`

**Repo:** commodity-carry-research

| Cell | Profile says | Status | Artifact checked |
|---|---|---|---|
| Verdict | FALSIFIED | consistent (NOT PROMOTED, 5/5 gates FAIL) | `reports/PRIMARY_REPORT.md:91-99,130` |
| Mechanism: "premise already ≈0 (t=0.30)" | t = 0.30 | consistent (coef +0.000672, clustered t 0.3000, p 0.7642) | `reports/PREMISE_REPORT.md:73-75` |
| Mechanism: "no edge gross of costs either" | — | consistent (gross −0.1288). Gross is below net because costs feed into the vol-targeting leverage. | `reports/ROBUSTNESS_REPORT.md:177`; `src/primary.py:194-196` |
| Key stat: net Sharpe | −0.126 | consistent (−0.1255); annualisation caveat as in row 11 | `reports/PRIMARY_REPORT.md:72` |
| Key stat: CI lower | −0.56 | consistent (−0.5580) | `reports/PRIMARY_REPORT.md:73` |
| Key stat: CI upper | 0.32 | consistent (0.3227) | `reports/PRIMARY_REPORT.md:73` |

---

## Prose claims outside the table

| Profile line | Claim | Status | Evidence |
|---|---|---|---|
| `README.md:3` | "One confirmed edge" | **inconsistent** | The repo's own label for the core is "SUPPORTED — NOT INDEPENDENTLY CONFIRMED" (row 1). |
| `README.md:5` | "pre-register before looking" | **inconsistent for the headline row.** The core TSMOM result had no pre-registration (`research/extensions/TRIAL_LEDGER.md:104,175`; `research/extensions/SAMPLE_REUSE.md:74`). For every other study, the prereg and its results share a commit or are squashed together, except commodity carry (server-side Actions timestamp before the data pull) and orderflow (manifest ingestion times). | per-repo files, "Pre-registration" findings |
| `README.md:5` | "BH-FDR across every family" | **inconsistent.** There is no multiplicity treatment for the core TSMOM (45-cell grid, 3 aggregations, a discarded no-trade band; the trial count is recorded as UNKNOWN at `research/extensions/TRIAL_LEDGER.md:106-108`). Crash-defense and vol-breakout have no inference at all. In spot-MFI the 49-config M2 grid sits outside the family. | `multi-asset-tsmom-research.md`, `spot-mfi-btc-perp-research.md` |
| `README.md:5` | "no-look-ahead proven by truncation-invariance" | **inconsistent for quant-backtest-framework.** Two look-ahead paths are confirmed, and there is no truncation test on the take-profit path (`mtf_smc/strategy/context.py:109`; `tests/test_lookahead_primitives.py:32-74`). Consistent for TSMOM core, orderflow and spot-MFI, though "proven" rests on single cut points and synthetic fixtures. | `quant-backtest-framework.md` |
| `README.md:5` | "negatives reported with the same prominence as positives" | consistent in spirit. The table lists 11 negatives. | — |
| `README.md:24` | Monday "looked real in isolation, evaporated under BH-FDR" | **overstated.** Only Bond has raw p < 0.05, and that cell also fails the magnitude and stability gates without any FDR correction. | Row 5 |
| `README.md:24` | yield-curve "edge was entirely the 2022–24 inversion" | **inconsistent.** 26–68% of Δ remains after dropping 2022–24, and the "edge" was never significant (p_raw 0.18–0.67). | Row 6 |
| `README.md:24` | fake winner "failed bootstrap CI, permutation, and per-fold persistence" | consistent (caveats). The persistence check is post-hoc, the OOS window was reused, and base M2 was a second OOS winner. | Row 10 |
| `README.md:26` | Binance archive gap (2022-09-06) | date consistent (orderflow `reports/QA_SUMMARY.md:45,57,87,92`). **Guarded only partly.** The quarantine window lives in an untracked, hand-made `data/quarantine_windows.json`, and the loader silently returns `{}` if it is missing (`src/orderflow/quarantine.py:20-22`). The repo characterises it as an exchange-side gap present in both archives, not an "archive gap". | `orderflow-research-engine.md` |
| `README.md:26` | same-ID / revised-quantity aggTrades mismatch | consistent. A regression test exists (`tests/test_backfill.py:96-126` → `src/orderflow/etl.py:298-317`), but no committed runner applies the repair to the ETH 2023-05 partial days. | `orderflow-research-engine.md` |
| `README.md:26` | "found by watching output rather than by a passing test" | consistent (orderflow commit `83c09e3`) | — |
| `README.md:26` | gold-calibrated constant "producing −25R/trade on EURUSD" | **no supporting artifact found** for the number. The buggy run output was never committed. The mechanism reproduces: the old 0.05 constant gives −49.6 R on a 10-pip stop and −10.96 R on a 50-pip stop, so −25 R is plausible. Regression test at `tests/test_instruments_multi.py:58-77`; it catches EURUSD/GBPUSD but would pass with the old constant on WTIUSD. | `quant-backtest-framework.md` |
| `README.md:28` | LinkedIn link | not checked (external) | — |
| — | Archive coverage | **gap.** `semiconductor-yield-screening` (public, first commit 2026-08-23) is not in the table or anywhere else in the profile. | See "Decisions needed" in `00-SUMMARY.md` |
