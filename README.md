# Aaron Lau — Quantitative Research

Falsification-first quant research. One confirmed edge; everything else honestly rejected — with the mechanism for each.

**Research philosophy:** pre-register before looking · BH-FDR across every family · no-look-ahead proven by truncation-invariance · negatives reported with the same prominence as positives.

## The archive

| Hypothesis | Repo | Verdict | Mechanism | Key stat |
|---|---|---|---|---|
| Multi-asset TSMOM core (17 ETFs, vol-targeted) | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | **CONFIRMED** | diversified trend premium; crisis alpha in 2008/2020 | net Sharpe 0.75, 95% CI [0.29, 1.23] |
| MTF SMC price action replicates across instruments | [quant-backtest-framework](https://github.com/AaroNLaU0307/quant-backtest-framework) | FALSIFIED | eye-catching single-instrument cells are small-sample noise; effective 3.45/5 independent instruments | 0/210 BH-FDR; WF E[R] −0.339 R, 21/24 windows < 0 |
| Crash-defense overlay | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | FALSIFIED (Phase 0) | trigger anti-aligned with realized drawdown regimes | signal 0.94–0.95 in 2008/2020 vs 0.49 in real drawdowns |
| Vol-compression breakout overlay | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | FALSIFIED (Phase 1B) | mechanical artifact; no directional premise | vol expansion ~1.31×, efficiency-ratio Δ≈0 |
| Seasonality / calendar overlay | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | FALSIFIED | headline Monday effect = multiple-testing false positive, caught | 0/18 BH-FDR |
| Yield-curve regime overlay | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | FALSIFIED | single-episode illusion — 2022–24 dominates inverted-curve days | 0/6 cells |
| XSMOM adds an independent edge | [multi-asset-tsmom-research](https://github.com/AaroNLaU0307/multi-asset-tsmom-research) | FALSIFIED | +0.42 corr with TSMOM = same-source alpha; lead-lag term never shown non-trivial | 0/5 universes |
| Order-flow footprint signals (4 tested; 2 data-blocked, disclosed) | [orderflow-research-engine](https://github.com/AaroNLaU0307/orderflow-research-engine) | FALSIFIED / null | best cell ~2bp gross vs ~18bp cost bar; OOS never opened | 0/20 BH-FDR at 2M-rep bootstrap |
| Spot-MFI momentum predicts perp returns | [spot-mfi-btc-perp-research](https://github.com/AaroNLaU0307/spot-mfi-btc-perp-research) | FALSIFIED | IC≈0 before any strategy; IS plateau 1.07 → OOS 0.29 < B&H 0.46 | 0/42 BH-FDR |
| MFI × funding divergence (Variant A) | [spot-mfi-btc-perp-research](https://github.com/AaroNLaU0307/spot-mfi-btc-perp-research) | INCONCLUSIVE, leaning FALSIFIED | fake winner: OOS 0.68 beat B&H, then failed bootstrap CI + permutation + persistence | PBO 0.84 via CSCV, 12,870 splits |

**Three statistical illusions caught in the act:** a multiple-testing false positive (Monday seasonality looked real in isolation, evaporated under BH-FDR), a single-episode illusion (a yield-curve "edge" was entirely the 2022–24 inversion), and a fake winner (MFI × funding divergence beat buy-and-hold out-of-sample, then failed bootstrap CI, permutation, and per-fold persistence).

Data-defect catches, found by watching output rather than by a passing test: a Binance archive gap (2022-09-06) and a same-ID/revised-quantity aggTrades mismatch, both in orderflow-research-engine; a gold-calibrated absolute-price constant silently producing −25R/trade on EURUSD in quant-backtest-framework.

**Currently:** MSc Data Science (Monash), targeting quantitative research roles. (https://www.linkedin.com/in/aaron-lau-b65b22224/)
