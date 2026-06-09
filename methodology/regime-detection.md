# Regime Detection

## Framing

This project treats regime detection as a symmetric problem. The backtest evaluates both bear→bull and bull→bear transitions with equal rigour. There is no bias toward long-only signals — the framework is designed to identify when a regime is ending, regardless of direction.

Three signal types are in scope:

| Type | Question |
|---|---|
| **Regime Ending** | Is the current trend losing structural support? |
| **Regime Sustaining** | Is the current trend intact and likely to continue? |
| **Regime Starting** | Is a new trend establishing itself? |

---

## SBST Framework

SBST (Sustained Bottom / Sustained Top) is the core labelling framework from v6 onward.

**Sustained Bottom (SB):** The LT or MT score has remained in the lower percentile band for at least N consecutive weeks. This identifies bear regimes that are not transient dips but structurally depressed readings.

**Sustained Top (ST):** The LT or MT score has remained in the upper percentile band for at least N consecutive weeks. This identifies bull regimes with persistent elevated momentum.

The sustained condition is what distinguishes SBST from simpler single-week threshold filters. A score that dips to the 10th percentile once and recovers is noise; a score that stays below the 20th percentile for four consecutive weeks is a regime signal.

---

## Percentile-Rank Threshold Approach

From v5.0 onward, fixed metric cutoffs (e.g., WD_MT < −1.5) were replaced with rolling percentile-rank thresholds. The cutoff adapts to the current score distribution rather than being calibrated to a fixed historical value.

**Why this matters:** ETF momentum scores are not stationary. A WD_MT of −1.5 may represent a moderate reading in a high-volatility market and an extreme reading in a low-volatility market. Percentile-rank thresholds normalise for this, making labels more comparable across different market environments.

**Implementation:** For each metric and each week, compute the rolling percentile rank of the current value within a lookback window of W weeks. A prank of 0.10 means the current reading is in the bottom 10% of the rolling window.

---

## Sweep Methodology

Rather than manually selecting threshold values, v8+ uses systematic sweep analyses to find stable parameter regions:

### Threshold Sweep
Run the signal labelling at each percentile cutoff from the 5th to the 40th percentile (in 5-point steps). For each cutoff, compute signal frequency, Day-Avg, and Never Below. Identify the region where precision is high and frequency is not critically low.

### Band Sweep
Rather than a single percentile cutoff, test a band (e.g., prank ≤ 0.15) vs a narrower band (prank ≤ 0.10) vs a wider band (prank ≤ 0.25). Band width affects frequency/precision tradeoff.

### Window Sweep
Test lookback window sizes from 8 weeks to 26 weeks. Shorter windows are more adaptive but noisier; longer windows are more stable but slower to respond to structural shifts.

### Combo Sweep
Test two- and three-metric combinations. For each combination, measure whether the intersection condition (all metrics in their respective bands simultaneously) improves precision versus any single metric condition alone.

### Temporal Consistency
Verify that the labelling logic produces consistent results across different sub-periods of the dataset. A label that only works in one market phase is not robust.

---

## CAW (Condition-Adjusted Window) Sweep

Introduced in v8. The CAW sweep adds a second dimension to the threshold sweep: for each percentile cutoff, it also varies the required number of consecutive weeks meeting the condition (the "window" component of SBST). This produces a two-dimensional grid of (threshold × window) combinations, from which stable precision regions are identified.

---

## BE Intersection Tier

Introduced in v9.1. The BE Intersection Tier is a sub-label that requires agreement across two separate metric conditions — the signal must qualify under both a primary band condition and a secondary metric condition simultaneously.

The intersection reduces frequency significantly but produces the highest-precision signals in the dataset. It is designed for use cases where selectivity is more important than coverage.
