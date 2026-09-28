# Phase 2 report: fixes applied

**Date:** 2026-09-27.
**Scope:** items 1–52 from `00-SUMMARY.md` §3, P2 items 54, 57, 58 and 59, and the provenance-header and hash-test part of item 53. The §5 decisions were applied as the owner made them.
**Branch:** every repo has a branch `claude/audit-fixes`, taken from its default branch. Nothing was merged, no pull request was opened, and no history was rewritten. The profile branch starts from `claude/repo-audit`, not `main`: that is `main` plus the Phase 1 `audit/` commit, and this report has to sit beside those findings.

## How to read this

**Evidence labels:**
- **REPRODUCED:** I, the orchestrating session, ran the check myself.
- **REASONED:** I read the diff or the artifact and inferred the result.
- **SELF-REPORTED:** the claim comes from a builder subagent's report, and I did not re-run it.

**How the work was split.** Six builder subagents each worked one research repo and committed locally. I then reviewed each diff and ran each check listed below. I also fixed what they missed before pushing. A final read-only subagent swept every repo for leftover echoes of the corrected claims. I fixed its must-fix findings and most of its minor ones.

**Checks I ran on each repo (REPRODUCED):**
- the full test suite at the pushed head;
- for each P0 code fix, its new test against the pre-fix source, restored with `git show <base>:<file>`. Every one failed there and passed at the head;
- ruff on the touched files, with no new violations against the base;
- `git status`, which was clean before every push.

**Commit abbreviations.**

| Repo | Base | Commits on `claude/audit-fixes` |
|---|---|---|
| multi-asset-tsmom-research | `b8404e7` | `02a562c`, `2f25c2d`, `70946a1`, `487a50c`, `e7e2592` |
| commodity-carry-research | `f0847d7` | `8c63ac9`, `f7011ef`, `b9f11e5`, `0bb1b44`, `c90df8e`, `acbee64` |
| semiconductor-yield-screening | `d64e644` | `40afbff`, `dfa4b40`, `3f30cd4`, `7fa6318`, `79074ea`, `702286a` |
| spot-mfi-btc-perp-research | `a90add7` | `02ca065`, `64b83af`, `670e832`, `36cec00` |
| orderflow-research-engine | `e10c588` | `dce1816`, `61454fb`, `f5f4e9c`, `f0549a2`, `3b49111` |
| quant-backtest-framework | `ad6df24` | `b8e043b`, `072fcc4`, `8690904`, `0fb6126`, `0bc1302`, `631f33d` |
| AaroNLaU0307 (profile) | `361a2b1` | `9ebcdec`, `8434053`, and the commit that adds this report |

## Items

### P0 (1–30)

| # | Repo | Status | Commit | Evidence | Note |
|---|---|---|---|---|---|
| 1 | Profile | done | `9ebcdec`, `8434053` | REPRODUCED | The verdict reads **SUPPORTED**: CI excludes 0; not independently confirmed; prospective test live since 2026-09-13. The date is linked to `CA_OPERATIONAL_STATE.json` (`2026-09-13T18:33:11Z`). The tagline is changed to match. The same echo was fixed in the "related research" sections of all five sibling repos (orderflow `f5f4e9c`, spot `02ca065`, qbf `0bc1302`, commodity `c90df8e`, tsmom `70946a1`). |
| 2 | qbf | done | `b8e043b` | REPRODUCED | `nearest_opposing_swing` now gates on `close_time(s.index + k)`, with k the lookback of the swing series used. Test: `tests/test_lookahead_asof.py` (swing truncation invariance over several cut points, ordinary and major swings). On the old `context.py` it fails, including `test_swing_not_usable_before_its_confirmation_bar_closes`. A re-run is pending (runbook). |
| 3 | qbf | done | `b8e043b` | REPRODUCED | The fill is now resolved before close-based invalidation, and a stop inside the fill bar books a stop-out (`Position.on_fill_bar`). Test: `tests/test_backtester.py`; on the old `backtester.py` it fails with `assert 0 == 1` (no trade) and `'tp' == 'stop'`. The old test that asserted the buggy order was replaced. A re-run is pending. |
| 4 | Profile | done | `9ebcdec` | REASONED | The philosophy line is scoped. Pre-registration is claimed for most studies, excluding the TSMOM core, crash-defense and breakout. BH-FDR is claimed within each registered family. Look-ahead is "checked with truncation-invariance tests", and the text names the two qbf leaks, fixed with a re-run pending. |
| 5 | commodity | done (code); re-run pending | `8c63ac9` | REPRODUCED | Statistics are now keyed on `ts_ref` (the CME trade date). Weekend trade dates are dropped and counted, and DELETE and undated records are handled. The calendar keeps only trade dates that have a settlement or OI. Test: `tests/test_statistics_calendar.py` (synthetic DBN files); 5 of 5 fail on the old `pipeline.py`. The published numbers are unchanged and flagged as pre-fix. |
| 6 | commodity | done (code + amendment); re-run pending | `f7011ef` | REPRODUCED | Adds an `execution_lag` parameter; the default 0 is the same-settlement convention the code followed. The lag-1 sensitivity is registered in `preregistration/AMENDMENT_2026-09-27.md`. The pre-registration itself is byte-identical to base. The new tests fail on old code with a `TypeError` (unknown keyword). The old test name, which claimed "no same-day signal-to-trade", is corrected. |
| 7 | commodity | done (code); re-run pending | `8c63ac9` | REPRODUCED | Selection uses `StatType.SETTLEMENT_PRICE` and `StatType.OPEN_INTEREST` from the pinned enum. `test_cleared_volume_is_never_taken_as_open_interest` fails on old code. Checking against raw data is step 2 of `RERUN_RUNBOOK.md`. |
| 8 | spot | done | `02ca065` | REPRODUCED | The 1.07 and the DSR 0.959/0.961 are relabelled full-sample in the README, the report bodies and the generators. The generator test fails on the old `run_05`/`run_A5`. The sealed phase reports are corrected in erratum §1. |
| 9 | spot | done | `02ca065` | REASONED | The full-sample IC is disclosed in the README limitations, the REPORT threats section and erratum §2. The verdict stands. |
| 10 | spot | done | `02ca065` | REPRODUCED | The gate table is now generated by `src/gates.py` from the registered gate list: 3 of 7 pass (gates 3, 6 and 7). I read it against `research/PREREGISTRATION.md:57-66`. |
| 11 | spot | done | `02ca065` | REPRODUCED | The count is 127 (42 + 49 + 36), now computed by `run_B2_pbo.py`. The test fails on the old `run_B2_pbo.py`. |
| 12 | orderflow | done | `dce1816`, `f5f4e9c` | REASONED | The text is now "no OOS return statistic computed or reported". Beyond the item, the runner drops OOS events and bars before any forward return is computed. For in-sample rows this is equivalent, by reasoning and a synthetic test, but it has not been re-run on data. |
| 13 | orderflow | done | `f5f4e9c` | REASONED | The README describes all three DEVIATIONS entries. |
| 14 | orderflow | done | `f5f4e9c` | REPRODUCED (q) / SELF-REPORTED (commit message) | New DEVIATIONS entry 3 (dated 2026-09-27) discloses the unseeded 10k-rep run that passed H1's gate. The README places it next to the smallest BH-adjusted q, 0.1092, which I recomputed from `reports/event_study_btc_cells.csv`. The earlier entries are untouched. |
| 15 | orderflow | done (prose) | `f5f4e9c` | REPRODUCED | H1 has N = 4,609 at h = 1/3/6 and 4,608 at h = 12/48. Quarantine nulling is per horizon; this is corrected in the README and in CORRECTIONS §2. |
| 16 | tsmom | done | `70946a1` | REPRODUCED | Dropping 2022–24 keeps 26–68% of Δ, with the sign kept in 6 of 6 cells. There are 12–13 episodes, and 2022–24 is 19–21% of flat-state days. Both are recomputed in `tests/test_headline.py` and restated in every living doc. Sealed records are covered by ERRATA §1. |
| 17 | tsmom | done | `70946a1` | REPRODUCED | `git cat-file` fails on both hashes. ERRATA §3 corrects the sealed report, and no living doc cites the hashes. |
| 18 | tsmom | done (generator); report not regenerated | `02a562c`, `70946a1` | REPRODUCED | The labels are now computed from the CIs. The test fails on the old generator (3 failed). The study is sealed, so the report is corrected in ERRATA §4. |
| 19 | tsmom + profile | done | `70946a1`, `e7e2592`, `9ebcdec` | REASONED | Living docs and the profile say `not_promoted` with each study's qualifier. The dated research maps and the drawdown report are covered by ERRATA §5. |
| 20 | tsmom | done | `70946a1` | REASONED | The "cost the headline" and anti-snooping claims are deleted. |
| 21 | tsmom | done | `70946a1` | REASONED | Now: q = 0.10 for seasonality and yield, α = 0.05 for XSMOM. |
| 22 | commodity + profile | done | `0bb1b44`, `c90df8e`, `9ebcdec` | REASONED | "12 registered variants (all run)" is stated and backed by a tested stat (0/12 reach 0.30). "Long-leg gains offset short-leg losses" cites +1.87%/yr and −1.82%/yr from `ROBUSTNESS_REPORT.md:181-182`. |
| 23 | commodity | done | `0bb1b44` | REASONED | The README retracts "every deviation logged before the computation ran". Addendum §6–7 gives a dated retroactive record of items 1, 4 and 5. `DEVIATIONS.md` is untouched. |
| 24 | qbf | done | `b8e043b`, `8690904` | REPRODUCED | The README now says 170 tests: 161 run in CI and 9 need licensed data. At the head: 161 passed, 9 skipped. The end-to-end prefix-invariance test now runs in CI on a synthetic fixture. |
| 25 | qbf | done | `8690904` | SELF-REPORTED (script output) | Relabelled everywhere as one synthetic trade per instrument (−1.02 to −1.04 R). The median from real trades is a runbook step. |
| 26 | qbf | done (wording); percentiles pending re-run | `8690904`, `072fcc4` | REASONED | New wording: "better than random entries, but not enough to overcome costs (XAUUSD only)". Percentiles are written to `output/robustness/random_entry.csv` at the re-run. |
| 27 | qbf | done | `8690904`, `631f33d` | REASONED | The high-slippage claim is removed; that run is registered as optional in the runbook. Test coverage is stated as it actually is. The unrun `tp_first` claim is also removed. |
| 28 | semiconductor | done | `3f30cd4` | REPRODUCED (SELF-REPORTED read of `replication.md`) | The range now reads +0.22 to +0.53. |
| 29 | semiconductor | done | `3f30cd4` | REPRODUCED | The sentence is computed from the §2 table: "9 of the 10 charted sensors". The test fails on the old `run_pipeline.py` (2 failed). `spc.md` was regenerated and that sentence is its only change. |
| 30 | semiconductor | done (retargeted) | `3f30cd4` | SELF-REPORTED | The DOE is generated by committed code, so its factors were changed to the replicating sensors 103 and 510, labelled post-hoc. |

### P1 (31–52)

| # | Repo | Status | Commit | Evidence | Note / blocker |
|---|---|---|---|---|---|
| 31 | tsmom | partly done | `2f25c2d` | REPRODUCED (tests) | `CORE_END_DATE` = 2026-06-12; the panel sha256 is checked (a warning, fatal with `--verify-panel`). **Blocked:** committing the core `output/monthly_returns.csv` needs the owner's price cache. The headline cites the committed `tsmom_net` series (sha256 `90cf79b6…`). |
| 32 | tsmom | done | `2f25c2d`, `70946a1` | REPRODUCED | `==` pins, with the Python 3.13 header. The README states one environment, and the suite gives 169 passed. |
| 33 | tsmom | partly done | `70946a1` | REPRODUCED (2 bps) / SELF-REPORTED (5 bps) | 2 bps and 5 bps are shown side by side. The 5 bps figure is labelled "repo-reported, not reproduced" (decision 7). **Blocked:** the excess-return Sharpe needs DGS3MO, which is not committed. |
| 34 | tsmom | done | `70946a1` | REASONED | "Not deflated; historical trial count unknown" appears beside every CI. |
| 35 | tsmom | partly done | `2f25c2d` | REPRODUCED (tests) | The trim is codified in `universe.py` and tested; the final 17 are unchanged. **Blocked:** the screening report needs git-ignored outputs from the price cache. |
| 36 | tsmom | done | `70946a1` | REPRODUCED | The branch is absent (`ls-remote`). The claim is removed from living docs, and the handoff is covered by ERRATA §6. |
| 37 | tsmom | done | `70946a1` | SELF-REPORTED | The jackknife rule change is disclosed wherever the jackknife is cited: 3 of 6 cells pass under the registered rule (ERRATA §2 table). |
| 38 | tsmom | done | `70946a1` | REASONED | Softened to "premise not supported (descriptive comparison, no inference)". |
| 39 | commodity | done; result CSVs at the re-run | `f7011ef` | REPRODUCED (grep, tests) | No `C:\Users` paths remain; `DATA_DIR`/`WORKSPACE` are used instead. New single entry point `scripts/run_all.py`. The result CSVs and JSON go to `results/` and are committed at the re-run. |
| 40 | commodity | done (code); re-run pending | `f7011ef` | REPRODUCED (tests fail on old code) | Leverage comes from the pre-cost series, and daily leverage turnover is costed. Also found and fixed: roll costs were being credited to short positions. |
| 41 | commodity | done (code); re-run pending | `f7011ef` | SELF-REPORTED | The DSR now uses sample moments and the empirical dispersion of trial Sharpes. The implied threshold, SR ≈ 0.76 (0.7574), is stated. |
| 42 | spot | partly done | `64b83af` | REASONED | The manifest is written on every pull and checked on every read. **Blocked:** a manifest of the data actually analysed needs the owner's original `data_cache/`; Kraken's 720-candle window means a re-pull differs. |
| 43 | spot | done | `64b83af` | REASONED | Disclosed: M2 reached 0.717 against 0.457 without being tested; Variant A reused the OOS windows; the registered benchmark is 0.313. |
| 44 | spot | done | `64b83af` | REASONED | Erratum §6 is the amendment. It reconstructs the registered T grid, states the edits and lists the 6 registered checks that were never run. The pre-registrations are untouched. |
| 45 | spot | done | `02ca065` | REPRODUCED | `src/gates.py` reads the thresholds from `config.py`. The report tables are generated between markers, and a test checks the committed tables match the generator. |
| 46 | orderflow | partly done | `dce1816` | REPRODUCED | The loader now fails closed; the test fails on old code. **Blocked:** `data/quarantine_windows.json` is not committed. The exact millisecond bounds are in no committed file, so they were not guessed. |
| 47 | orderflow | deferred | — | REASONED | **Blocked on the owner's machine:** the ETH 2023-05 repair step and the QA JSONL logs exist in no commit (`git log --all`). The README and CORRECTIONS §3 mark them as pending. |
| 48 | orderflow | done | `dce1816` | REPRODUCED | The REST snapshot book is persisted as parquet; the test fails on old code. |
| 49 | qbf | done; derived CSVs at the re-run | `072fcc4` | REPRODUCED (suite) | `==` pins with `requires-python`. `scripts/run_all.py` adds git/code/config-hash columns, and resuming onto rows from other code now raises. `.gitignore` admits the derived CSVs only. |
| 50 | qbf | done (code); value pending re-run | `072fcc4` | SELF-REPORTED (unit tests) | `period_sign_test` runs on the 12 calendar periods, and the block CI is the headline. |
| 51 | semiconductor | done | `7fa6318` | REPRODUCED (`test_headline` recomputes it) | Exhaustive circular-shift null: 34 of 1,095 shifts reach 32, p = 0.0319. It is labelled a post-hoc sensitivity, and "decisive" is dropped. |
| 52 | semiconductor | done | `40afbff`, `dfa4b40` | REPRODUCED (suite) / SELF-REPORTED (pin search) | Pinned with `==` and Python 3.11. No pin set reproduced the old last-digit CSV/JSON bytes (they depend on OpenBLAS), so those were regenerated in a commit that says so. The stable sort changes which tied sensors fill the 10th row of the QA ten-worst table. |

### P2 in scope

| # | Status | Commit | Evidence | Note |
|---|---|---|---|---|
| 53 (headers + hash tests) | done | tsmom `2f25c2d`, commodity `f7011ef`, semi `79074ea`, spot `670e832`, orderflow `61454fb`, qbf `072fcc4` | REPRODUCED (suites) | Each stats module now has a provenance docstring (origin, BH level, CI method) and a `tests/test_stats_provenance.py` that pins each helper's sha256. Spot's copy is vendored from `multi-asset-tsmom-research@b8404e7:src/xsmom_stats.py`; the upstream hashes are recorded and the differences listed. No shared package (decision 12). |
| 54 | partly done | tsmom `2f25c2d` | REPRODUCED | CI now runs the benb, value and x01-inference suites (94 passed, 2 skipped). **Blocked:** f6, vrp, ta, ca and the other x01 suites need git-ignored vendor data or the sibling checkout, and they are sealed test files. |
| 57 | partly done | qbf `0fb6126`, spot `670e832`, `36cec00` | REASONED | Both stale `CLAUDE.md` files are deleted, and the deletion is recorded where sealed docs still cite them. **Blocked:** trimming `docs/DECISION_LOG.md:10-13` (spot) would edit a decision log in place. |
| 58 | done | orderflow `f5f4e9c`, qbf `8690904`, commodity `0bb1b44`/`c90df8e`, tsmom `e7e2592` | REPRODUCED | Every stated count matches CI: orderflow 124 in CI of 131; qbf 161 of 170; commodity 168; tsmom 169. |
| 59 | done | tsmom `2f25c2d`, commodity `0bb1b44`, orderflow `f5f4e9c`, semi `40afbff`, qbf `072fcc4` | REASONED | Declared through `requires-python` where a `pyproject.toml` exists (qbf), otherwise a `requirements.txt` header plus the README setup line. The owner ruled out new top-level files, so there is no `.python-version`. |

### Decisions in §5 not tied to a numbered item

| Decision | Status | Commit | Note |
|---|---|---|---|
| 11: remove the TSMOM Yahoo prices | done | tsmom `2f25c2d` | `data/xsmom_universes_prices.csv` was removed with an ordinary commit. Its sha256 is `5b098a2c0eaa9d90524c46b100ed98302cbc732f146a301b49c8fb03464fd1d7` (REPRODUCED). `data/README.md` records the 47 tickers, the range 1996-03-18 → 2026-06-18, the pull window and the fetch path, and the runner checks the hash on re-fetch. **History is not touched: the file is still in earlier commits.** |
| 13: TSMOM governance volume | deferred by the owner | — | Not moved. |
| 14: CLAUDE.md | done | see item 57 | |
| S1: `results/headline.json` in every repo | done | last headline commit per repo | Every repo has a `tests/test_headline.py` that checks each stat against its artifact: CSV stats are recomputed and markdown stats are found on the cited line. The profile's `scripts/render_readme.py` renders the table and the "Other work" line from the six files, at the commits pinned in `scripts/headline_sources.json`. `--check` passes, and all 34 links resolve (REPRODUCED). The brief says "seven files"; there are six, one per research repo, because the profile repo has no result of its own. |

### Additions outside the item list

These were found during the work and are disclosed in each repo:
- **commodity:** roll costs were credited to short positions, in `f7011ef`.
- **commodity:** a one-name sector was shorted outright in robustness item 1, fixed in `b9f11e5`. Its test fails on the old code.
- **commodity:** the lag-1 pair counts toward N_trials, which goes from 14 to 16. This is recorded in the amendment.
- **tsmom:** `verdict_label` used to print "CONFIRMED" even for an all-negative CI.
- **orderflow:** the seed-invariance rationale sentence was corrected in `3b49111`.

## Handoff

### Pushed heads (branch `claude/audit-fixes`)

| Repo | Head |
|---|---|
| multi-asset-tsmom-research | `e7e2592d0caa10f32db1a1fb05245bec2368e165` |
| commodity-carry-research | `acbee64ee7dd7df11d1a44403b038945bf0439d6` |
| semiconductor-yield-screening | `702286ad450d7ee9471bea4fbf18bdc4a7ba8d13` |
| spot-mfi-btc-perp-research | `36cec00e828e6237fc7dfc42a522abb28537ccda` |
| orderflow-research-engine | `3b491114be37f1cf3abfacb6445a18c122d22d82` |
| quant-backtest-framework | `631f33d26a5b79e835b73061b3ef228c18b90a18` |
| AaroNLaU0307 | the commit that adds this file, on top of `8434053` |

**Test status at those heads (REPRODUCED):**
- tsmom: 169 passed, plus 94 passed / 2 skipped in the CI extension suites.
- commodity: 168 passed.
- semiconductor: 78 passed; the em-dash check is clean.
- spot: 111 passed.
- orderflow: 124 passed, 7 deselected (the CI selection).
- qbf: 161 passed, 9 skipped.

**Links:** the profile table links to these branch commits. If a branch is squash-merged and then deleted, re-pin `scripts/headline_sources.json` to the merged commits and re-run `python scripts/render_readme.py`. The same applies to each `source_commit`.

### Owner decisions still open

1. **The re-runs:**
   - quant-backtest-framework: the five grids, replication, the L1 walk-forward, L3 and the real-trade stop-out medians (`RERUN_RUNBOOK.md`).
   - commodity-carry-research: the data check, then every phase after the calendar, field-mapping, lag, cost and DSR changes (`RERUN_RUNBOOK.md`).

   Both need licensed data that is not in this environment. Until they run, the profile marks those figures ‡.
2. **Item 13:** the TSMOM governance volume (deferred).
3. **Owner-machine artifacts:**
   - orderflow: `data/quarantine_windows.json`, the QA JSONL logs and the ETH repair step (items 46–47);
   - spot: a manifest of the original `data_cache/` (item 42);
   - TSMOM: the core `output/monthly_returns.csv` and the screening report (items 31 and 35), and a DGS3MO series (item 33).
4. **The spot `docs/DECISION_LOG.md:10-13` trim** (item 57). It is a decision log, so it can only be done by an explicit owner edit or left as is.
5. **commodity N_trials, 14 → 16:** counting the lag-1 pair as trials is the conservative reading of prereg §10. The owner may prefer 14, with lag-1 as a non-counted diagnostic.

### Things I was unsure about

- **orderflow OOS guard.** `runners/phase3_event_study.py` now computes forward returns on in-sample data only. That is equivalent for in-sample rows by argument and by a synthetic test, but the committed results were produced by the old path, and nobody has re-run it on data.
- **Two thresholds the builders chose:**
  - TSMOM's computed "demean collapses" label uses "a positive baseline at least halves";
  - semiconductor's DOE retargeting to sensors 103/510 is labelled post-hoc.

  Both are disclosed, and both are judgement calls the owner may revisit.
- **Semiconductor regeneration.** Its CSV and JSON last digits depend on the CPU's OpenBLAS kernel. The determinism test allows rtol 1e-9 on those files and requires exact bytes on the `.md` reports.
- **Commodity feed assumptions.** The rerun rests on three assumptions the corrected pipeline makes about the feed: `ts_ref` is the trade date, the last-received settlement is final, and OI arrives before the next settlement. They are unverified without raw data, and each has a runbook check or a diagnostic counter.
- **Minor items left as they are:**
  - "crisis alpha" wording in the TSMOM living docs; the profile says "raw return, not benchmark-adjusted".
  - qbf "mark-to-market" labels (per-repo P1, outside 31–52).
  - commodity `PUSH_CHECKLIST.md` citations and the per-section OI caveats in `DESIGN_DECISIONS.md`, which are covered by the README banner and the addendum.
  - orderflow "unseen data" wording in the ROADMAP.
- **Notification.** The orchestrating session is notified with `create_trigger` / `fire_trigger`, carrying the pushed heads and the item counts above. If that delivery fails, this section is the summary.

**Item counts:** 57 items in scope (1–52, the header and hash-test part of 53, 54, 57, 58, 59).
- 49 done;
- 7 partly done, each with a named blocker: 31, 33, 35, 42, 46, 54, 57;
- 1 deferred: 47.

Code-complete items whose numbers wait on a re-run count as done for this phase: 2, 3, 5, 6, 7, 26, 40, 41, 50.

## Phase 3: merge

**Date:** 2026-09-27.
**What was done:** I fast-forwarded each repo's default branch to its `claude/audit-fixes` head (`git merge --ff-only`) and pushed it with a plain `git push`. There were no merge commits, rebases, amends or force-pushes, and no pull requests. Every default branch was still at the Phase 2 base commit, so every fast-forward went through, and all commit hashes are unchanged. The `claude/audit-fixes` and `claude/repo-audit` branches are left in place.

**Local suite on the merged default branch (REPRODUCED).** Each suite ran in a fresh `uv` venv built from the repo's pinned `requirements.txt`, on the Python version it declares:

| Repo | Default | Base → merged head | Python | Local result |
|---|---|---|---|---|
| multi-asset-tsmom-research | `main` | `b8404e7` → `e7e2592d0caa10f32db1a1fb05245bec2368e165` | 3.13 | 169 passed; CI extension suites 94 passed, 2 skipped |
| commodity-carry-research | `main` | `f0847d7` → `acbee64ee7dd7df11d1a44403b038945bf0439d6` | 3.13 | 168 passed |
| semiconductor-yield-screening | `main` | `d64e644` → `702286ad450d7ee9471bea4fbf18bdc4a7ba8d13` | 3.11 | 78 passed; em-dash check clean |
| spot-mfi-btc-perp-research | `main` | `a90add7` → `36cec00e828e6237fc7dfc42a522abb28537ccda` | 3.13 and 3.12 | 111 passed on each |
| orderflow-research-engine | `master` | `e10c588` → `3b491114be37f1cf3abfacb6445a18c122d22d82` | 3.13 | 124 passed, 7 skipped (full suite); 124 passed, 7 deselected (the CI selection, `-m "not data"`) |
| quant-backtest-framework | `main` | `ad6df24` → `631f33d26a5b79e835b73061b3ef228c18b90a18` | 3.13 | 161 passed, 9 skipped (each skip is "seeded cache not present") |
| AaroNLaU0307 (profile) | `main` | `7f4f734` → `5a52286d8775a4422ce207369f7899c232d34936` | n/a | `render_readme.py --check` passed, both from GitHub and with `--local-root` |

**CI on the default branch.** This is the first run on `main`/`master` for these changes.

| Repo | Run | Conclusion |
|---|---|---|
| multi-asset-tsmom-research | https://github.com/AaroNLaU0307/multi-asset-tsmom-research/actions/runs/36310108987 | success |
| commodity-carry-research | https://github.com/AaroNLaU0307/commodity-carry-research/actions/runs/36310157598 | success |
| semiconductor-yield-screening | https://github.com/AaroNLaU0307/semiconductor-yield-screening/actions/runs/36310351901 | success |
| spot-mfi-btc-perp-research | https://github.com/AaroNLaU0307/spot-mfi-btc-perp-research/actions/runs/36310398744 | success (the Ubuntu/Windows/macOS × 3.12/3.13 matrix) |
| orderflow-research-engine | https://github.com/AaroNLaU0307/orderflow-research-engine/actions/runs/36310481953 | success |
| quant-backtest-framework | https://github.com/AaroNLaU0307/quant-backtest-framework/actions/runs/36310555889 | success |
| AaroNLaU0307 (profile) | no workflow in the repo | n/a |

**CI fixes:** none were needed. Every run was green on the first attempt, and no test, workflow, skip condition, number or document was changed.

**Profile links.** `scripts/render_readme.py` has no link check, so I extracted every link from `README.md` and fetched each with `curl -L`. All 37 unique URLs returned 200. (Phase 2 counted 34 links in the generated table; the 37 includes links outside it.) No HTML or reference-style links exist.

**One ordering note.** I pushed the profile `main` while the quant-backtest-framework CI run was still in progress. Its local suite and `--check` had already passed, and the run finished green a few minutes later. Nothing depended on that ordering, because the profile pins commit hashes, not branch heads.

**Still open:** the owner decisions and re-runs listed under Phase 2 "Handoff". Phase 3 changes none of them.

## Phase 4: re-runs

**Date:** 2026-09-28.
**What was done:** the owner re-ran the two studies whose engine or data pipeline changed in Phase 2, on the machine that holds the licensed data, and pushed each re-run as a branch `rerun/2026-09-27`. The reviewing session accepted both. I fast-forwarded each default branch to that branch (`git merge --ff-only`) and pushed it with a plain `git push`. Both default branches were still at their Phase 3 heads, so both fast-forwards went through. Then I removed every sentence that still called either result pre-fix or pending, and re-pinned the profile. There were no merge commits, rebases, amends or force-pushes, no pull requests, and no deleted branches; the `rerun/2026-09-27` branches are left in place. No research number changed beyond those the re-run branches carry.

**Verdicts (from each repo's `results/headline.json`, REASONED):**
- quant-backtest-framework: **FALSIFIED**, held. 0/210 cross-instrument BH-FDR survivors, 0/42 configs positive-and-significant on two or more instruments; L1 walk-forward pooled OOS E[R] −0.339 → −0.329 R, window-block CI [−0.416, −0.228], 11/12 calendar periods negative (one-sided p = 0.0032). Artifacts commit `10ee563`. The gold study's one-time 2023–2025 OOS figure predates the fix and is not recomputed, by design; the headline caveats say so.
- commodity-carry-research: **NOT PROMOTED**, held. H1 net Sharpe −0.003 → 0.059, 95% CI [−0.41, 0.52], 0/9 registered variants reach the 0.30 gate. H2 now closes at the sign-only premise gate (coefficient −0.000143, t = −0.07), so no backtest was run. Artifacts commit `8d637a6`.

**Merge and CI (REPRODUCED).** Each suite ran in a fresh `uv` venv on Python 3.13, from the repo's pinned `requirements.txt`.

| Repo | Default | Phase 3 head → merged head | Local suite | CI on the merged head |
|---|---|---|---|---|
| quant-backtest-framework | `main` | `631f33d` → `bc4095c06344c7e2b2a23813f6e42299cb721261` | 166 passed, 9 skipped | https://github.com/AaroNLaU0307/quant-backtest-framework/actions/runs/36450396243 success |
| commodity-carry-research | `main` | `acbee64` → `9071e18f0a60f6a3632df64c42dd2dcff17c065a` | 172 passed, 1 skipped | https://github.com/AaroNLaU0307/commodity-carry-research/actions/runs/36450547843 success |

Every skip needs licensed data or a seeded cache. No CI fix was needed.

**Stale qualifiers.** I searched every `.md` and `.json` in the four repos for "pending", "predate", "pre-fix" and "before the 2026-09-27", and judged each hit.

| Repo | Commit | What changed | CI |
|---|---|---|---|
| multi-asset-tsmom-research | `66d8cfeb153912ab80997f29b50f0e7c5c840e3a` | `README.md` related-research entry for quant-backtest-framework; `STUDY_SUMMARY.md` intro and "Across projects" bullet. Each now states the held verdict and links the re-run artifact (`results/headline.json`, `output/grid/master_table.csv`) | https://github.com/AaroNLaU0307/multi-asset-tsmom-research/actions/runs/36450808977 success (local: 169 passed; extension suites 94 passed, 2 skipped) |
| orderflow-research-engine | `c3807e91998864ea0a272adbb302c72d9efca4c8` | `README.md` related-research entry for quant-backtest-framework | https://github.com/AaroNLaU0307/orderflow-research-engine/actions/runs/36450934476 success (local: 124 passed, 7 deselected) |
| commodity-carry-research | `e710a353a5ec2e7cff5da8d8a1cf3f97a0c4fdef` | `README.md` related-research entry for quant-backtest-framework (found by the search; not in the brief's list) | https://github.com/AaroNLaU0307/commodity-carry-research/actions/runs/36450839212 success (local: 172 passed, 1 skipped) |
| quant-backtest-framework | none | The re-run branch had already updated its living documents. What remains is the dated addendum (history), `RERUN_RUNBOOK.md` (instructions), and accurate caveats that the one-time OOS figure and the legacy-fidelity comparison predate the fix | n/a |

**Left as they are, on purpose:**
- **Dated history.** Both repos' `ADDENDUM_2026-09-27.md` state the pending status as it stood that day, with the re-run appended as its own dated section (qbf §11–12, commodity §10). Also left: `preregistration/`, the decision logs, and commodity's Phase 1 data reports, whose "pending" items are unrelated ledger or QA entries.
- **tsmom planning records.** `research/extensions/TSMOM_EXTENSION_RESEARCH_MAP.md` and `_v2.md`, and `research/extensions/diagnostics/EDGE_DIAGNOSTICS.md`, quote the published commodity figure (net Sharpe −0.003). They are dated planning and diagnostic records inside that repo's sealed research lineage. They do not describe anything as pending, so updating them would rewrite the lineage.

**Profile.** `scripts/headline_sources.json` now pins quant-backtest-framework at `bc4095c` and commodity-carry-research at `9071e18`, whose `source_commit`s are `10ee563` and `8d637a6`. The re-rendered table has the re-run figures, no ‡ mark, and the time-series carry row reads "closed at the sign-only premise gate". `render_readme.py` now prints the ‡ legend sentence only when a rendered stat carries ‡, so the legend no longer says a re-run is pending. `--check` passes. I also updated two living texts that described the re-runs as outstanding: the audit-record line in `README.md` and the "What is still open" paragraph of `audit/README.md`. All 39 unique GitHub URLs in `README.md` returned 200 with `curl -L`. To check the spot and semiconductor links, I attached those two repos read-only, because the session proxy returns 403 for repos not attached to it. Nothing in those two repos was changed.

**Still open:** the Phase 2 handoff items other than the re-runs (item 13, the owner-machine artifacts, the spot decision-log trim, and the commodity N_trials choice). The orderflow OOS-guard note also stands.
