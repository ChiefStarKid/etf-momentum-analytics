# Findings Summary

Current as of v9 (2026-06-07). All results use Day-Avg and Never Below as defined in [backtest-design.md](../methodology/backtest-design.md). Benchmark for Day-Avg: 50% = random.

---

## Core Finding — v1.0 (2026-05-21)

The most important result from the initial backtest: combining WD_MT < 0 (MT momentum falling) with Accel_MT ≥ 0 (the fall is decelerating) produces a substantially stronger signal than either condition alone.

**WD_MT < 0, segmented by Accel_MT** (n=52 total)

| Condition | n | 15d Day-Avg | 30d Day-Avg | 45d Day-Avg | 60d Day-Avg | 75d Day-Avg |
|---|---|---|---|---|---|---|
| Accel_MT < 0 (deepening fall) | 32 | 53% | 56% | 58% | 59% | 59% |
| **Accel_MT ≥ 0 (recovering)** | **20** | **76%** | **81%** | **83%** | **83%** | **83%** |

The 81–83% Day-Avg at 30–60d for the recovering condition versus 56–59% for the deepening condition is the largest performance gap identified in the entire backtest. This two-metric combination (WD_MT < 0 + Accel_MT ≥ 0) became the basis for the LT-RECOVERY signal label.

---

## LT-RECOVERY Signal Quality — v2.0 (2026-05-23)

Signal quality measured using the revised Day-Avg / Never Below framework (reference: Friday close; horizons: 15–60d).

**LT-RECOVERY (all variants, n=15–19 depending on horizon)**

| Horizon | Day-Avg | Never Below | n |
|---|---|---|---|
| 15d | 76% | 39% | 18 |
| 30d | 80% | 41% | 17 |
| 45d | 85% | 40% | 15 |
| 60d | 88% | 40% | 15 |

Edge does not decay across the measurement window. The 88% Day-Avg at 60d is the highest single result in the dataset. Never Below plateaus around 39–41%, reflecting that while price almost always holds above reference on average, clean uninterrupted holds are achieved in roughly 2 in 5 trades.

---

## MACD_LT Inverse Correlation — v1.0

**WD_MT across MACD_LT buckets** (n=165 total observations)

| MACD_LT bucket | n | 15d Day-Avg | 30d Day-Avg | 45d Day-Avg | 60d Day-Avg | 75d Day-Avg |
|---|---|---|---|---|---|---|
| MACD > +0.5 (strong bull LT) | 9 | 55% | 55% | 55% | 55% | 55% |
| MACD 0 to +0.5 | 70 | 57% | 61% | 62% | 63% | 63% |
| MACD −0.5 to 0 | 78 | 61% | 63% | 68% | 71% | 72% |
| **MACD < −0.5 (strong bear LT)** | **8** | **77%** | **72%** | **70%** | **70%** | **70%** |

Strong LT bull momentum (high MACD_LT) predicted the weakest forward returns. Strong LT bear momentum predicted the strongest. This inverse relationship led to MACD_LT being removed from all signal definitions.

---

## v9 Calibration State

Percentile-rank sweep analyses across threshold, band, window, and combo dimensions show that:

- **Threshold region 10th–20th percentile** produces the most stable Day-Avg results across different window sizes (8–20 week lookbacks)
- **Band width of 1–2 percentile points** around the core threshold reduces noise without critically reducing signal frequency
- **Two-metric combo conditions** consistently outperform single-metric conditions on precision; three-metric combos further improve precision but reduce frequency to levels that limit statistical confidence at current sample sizes
- **Temporal consistency** is maintained across the available sample period — labelling behaviour does not significantly shift between the first and second halves of the dataset

Full sweep results are in the v9 Reports folder (not included in this public repository).

---

## Sample Size Caveat

The signal dataset covers approximately 15–19 weeks of history at v9. At this sample size, longer-horizon results (75d+) have reduced n due to measurement window truncation. The findings above should be treated as directional evidence of edge rather than definitive quantitative results. Results will be updated as the dataset grows.
