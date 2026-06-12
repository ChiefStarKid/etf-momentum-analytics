# Changelog

This project evolved through 10 major versions over approximately three weeks. Each version addressed a specific analytical question. This log documents what was asked and what was found — including findings that overturned prior assumptions.

---

## v10 — Bullish-Move Label and PCS Profiling (2026-06-08 to 06-09)

**Question:** Can a "Bullish Move" label — indifferent to interim dips, unlike the Bottom-Exhaustion label which penalises any drop below reference — isolate setups suited to short-dated put credit spreads?

**What changed:** Introduced BM labels in two variants (maximum drawup, and price-volume mass above a raised bar), swept across six forward windows and multiple gain thresholds. v10.1 tested BM+BE combinations for higher signal volume. v10.2 profiled the live BM signal end to end: daily gain path, drawdown-before-peak, day-of-peak distribution, and a put-credit-spread win-rate matrix across DTE and OTM buffer.

**Finding:** The highest-conviction BM signal is rare — single-digit occurrences across roughly 16 months — but its gain path is tight and its interim drawdowns shallow, which is exactly the profile a short-dated put credit spread wants. This is consistent with the pattern that runs through the whole project: tighter filters buy precision at the cost of frequency, and the rarest signals must be read with their small n in mind.

---

## v9 — Weekly Calibrated (2026-06-07)

**Question:** Can percentile-rank labelling replace hard thresholds across all signal types, and does it hold up under multi-dimensional sweep analysis?

**What changed:** Replaced fixed metric cutoffs with rolling percentile-rank (prank) thresholds across WD, Accel, and delta metrics. Ran sweeps across band width, combo selection, window size, stride length, and temporal consistency. BE Intersection Tier introduced as a high-precision sub-label requiring agreement across multiple metric conditions.

**Finding:** Prank-based labelling stabilises across window and band variations. The BE Intersection Tier produces fewer signals but higher hit rates. Combo landscape analysis identifies which two- and three-metric combinations are most robust.

---

## v8 — Daily Data Integration (2026-05-30)

**Question:** Can daily price data replace the weekly cadence for forward return measurement?

**What changed:** Shifted from weekly-close reference prices to daily OHLC data. Introduced the CAW (Condition-Adjusted Window) sweep for threshold calibration across daily granularity. Threshold sweep v8 and CAW sweep run in parallel.

**Finding:** Daily data is viable and adds resolution to shorter horizons (15–30d). The CAW sweep methodology provides a more systematic basis for threshold selection than manual tuning.

---

## v6–v7 — Sustained Bottom/Top and Threshold Optimisation (2026-05-26)

**Question:** Does requiring *sustained* extreme readings (not just a single-week threshold breach) improve signal purity?

**What changed:** Introduced SBST (Sustained Bottom / Sustained Top) labels — requiring consecutive weeks at extreme percentile levels rather than a single-week filter. v7 tested the 85th-percentile threshold specifically.

**Finding:** SBST filtering improves signal purity (higher hit rates) but reduces signal frequency meaningfully. The 85th-percentile threshold is more selective than 75th with a modest precision improvement. Frequency vs precision tradeoff explicitly documented.

---

## v5.0–v5.1 — Relative Tiers and Two-Condition Filtering (2026-05-26)

**Question:** Can relative tiers (where a score sits within its own distribution) outperform absolute thresholds (fixed cutoff values)?

**What changed:** Replaced fixed WD/Accel cutoffs with percentile-rank tiers derived from the rolling score distribution. v5.1 tested two-condition Regime Ending (RE) filters requiring both a tier condition and a directional confirmation.

**Finding:** Relative tiers are more stable across different sample windows. Two-condition RE filtering reduces false positives — confirming that combining a percentile condition with a directional metric is stronger than either alone.

---

## v3.0–v4.3 — Multi-Stride, Combos, and Adaptive Thresholds (2026-05-24–25)

**Question:** Do three-metric combinations or adaptive (data-driven) thresholds outperform the two-metric WD+Accel base finding?

**What changed:** v3–v4 series introduced: daily in-depth analysis, multi-stride WD (WD computed across different lookback windows), 3-metric combo sweep, adaptive threshold search, and rolling percentile framing.

**Finding:** 3-metric combos outperform 2-metric combos on precision but not always on frequency-adjusted basis. Adaptive thresholds (derived from the data distribution) outperform fixed cutoffs, foreshadowing the full percentile-rank approach in v5+. Rolling percentile adds meaningful stability versus static thresholds.

---

## v2.0–v2.1 — Signal Quality Framework and Symmetric Regime Detection (2026-05-23)

**Question:** Can the backtest be reframed as a pure signal quality test, decoupled from any specific trade structure? And can it detect regime change symmetrically — both bear→bull and bull→bear?

**What changed:** Introduced two distinct measurement metrics:
- **Day-Avg** — % of forward trading days where Close ≥ reference close (benchmark: 50% = random)
- **Never Below** — % of trades where Close stayed ≥ reference close on *every* forward day (stricter)

Formalised three signal types: Regime Ending, Regime Sustaining, Regime Starting. Confirmed symmetric scope: both Bear→Bull and Bull→Bear transitions in scope.

**Finding:** Decoupling signal quality from trade mechanics clarified which findings were signal-driven and which were artifacts of entry/exit assumptions. LT-RECOVERY signals showed Day-Avg of 76–88% across 15–60d horizons, establishing the core result that carried forward.

---

## v1.0 — Initial Metric Backtest (2026-05-21)

**Question:** Do the derived momentum metrics (WD_MT, Accel_MT, MACD_LT) predict forward returns at all?

**Sample:** 11 weeks × 15 ETFs = 165 observations (2026-03-06 → 2026-05-15)

**What was tested:** Bucket analysis across WD_MT levels, Accel_MT refinement within WD buckets, and MACD_LT (EMA3 − EMA8 on the LT score series).

**Finding — what held up:** WD_MT alone has modest predictive power, but combining WD_MT < 0 (MT falling) with Accel_MT ≥ 0 (deceleration / recovery) produced a 81–83% hit rate at 30–60d versus a 56% baseline for the same WD_MT group without the Accel filter. This two-metric combination became the foundation of the signal design.

**Finding — what was overturned:** MACD_LT is *inversely* correlated with forward returns. High MACD_LT (strong LT bull momentum) predicted weaker performance. This reversed the initial assumption. MACD_LT was removed from all subsequent signal definitions.
