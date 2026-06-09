# Signal Design

## The Exhaustion Hypothesis

Weekly LT/MT/ST momentum scores derived from heatmap-style signals are lagging by construction. They aggregate historical price behaviour — by the time a score turns, the price move that caused the turn has already happened. Using them as trade entry signals is therefore late by design.

This project takes a different angle: the scores are treated as a proxy for bulk volume profile analysis. What a volume profile shows over time — accumulation zones, distribution levels, exhaustion at extremes — is expensive to compute systematically across 15 ETFs at weekly resolution. The hypothesis is that the LT/MT/ST scores, despite being lagging, encode some of that structural information in aggregate form.

The critical insight is that **the scores themselves are not the signal — the rate of change of the scores is.** A score that is falling but decelerating suggests that selling pressure is dissipating before price has confirmed the turn. A score reaching a sustained extreme suggests regime persistence, not just a transient spike. These conditions are what the signal labels are designed to detect.

This hypothesis has not been directly tested against volume profile or other exhaustion indicators. The backtest results are consistent with it, but distinguishing exhaustion-driven edge from other mechanisms requires further work.

---

## Data Source

Weekly LT (long-term), MT (medium-term), and ST (short-term) momentum scores are extracted from a proprietary weekly heatmap signal source. Each score is a numeric value on a continuous scale. Scores are available for 15 US equity ETFs:

**Broad market:** SPY, QQQ, DIA, IWM
**Sectors:** XLK, XLF, XLV, XLY, XLC, XLI, XLP, XLE, XLRE, XLB, XLU

Scores are extracted weekly (Friday) and stored in a structured time-series format. The sample begins 2026-03-06 and grows with each weekly update.

---

## Derived Metrics

Six rate-of-change metrics are computed from the raw LT/MT/ST scores. W0 = current week, W1 = one week prior, W2 = two weeks prior, W3 = three weeks prior.

| Metric | Formula | What it measures |
|---|---|---|
| **WD_MT** | `0.5×(MT_W0−MT_W1) + 0.3×(MT_W0−MT_W2) + 0.2×(MT_W0−MT_W3)` | Recency-weighted MT momentum direction. Positive = MT rising; negative = MT falling. |
| **Accel_MT** | `(MT_W0−MT_W1) − (MT_W1−MT_W2)` | MT acceleration. Positive = fall slowing or rise accelerating. Under the exhaustion hypothesis, Accel_MT ≥ 0 during a falling WD_MT period is the key deceleration signal. |
| **LT_delta** | `LT_W0 − LT_W1` | Single-week change in LT score. |
| **LT_Accel** | `(LT_W0−LT_W1) − (LT_W1−LT_W2)` | LT acceleration. Positive = LT fall slowing. |
| **ST_delta** | `ST_W0 − ST_W1` | Immediate ST direction. |
| **ST_Accel** | `(ST_W0−ST_W1) − (ST_W1−ST_W2)` | ST acceleration. |

The acceleration metrics (Accel_MT, LT_Accel, ST_Accel) are the primary exhaustion proxies. They measure whether the rate of score change is itself changing — slowing momentum in a declining score is the signature this project is trying to isolate.

---

## MACD_LT — Tested and Removed

MACD_LT (EMA3 − EMA8 applied to the LT score series) was included in the initial metric set as a trend-confirmation measure. Bucket analysis showed it is *inversely* correlated with forward returns: high MACD_LT (LT momentum still accelerating) predicted weaker forward performance; low MACD_LT predicted stronger outcomes.

Under the exhaustion hypothesis, this makes sense. High MACD_LT means the LT trend is still building momentum — no exhaustion has set in. Low MACD_LT means the trend is losing momentum, which is where exhaustion conditions begin to appear. The inverse relationship confirmed the hypothesis directionally and led to MACD_LT being removed from all signal definitions — it was measuring the wrong thing.

---

## Signal Labels

### LT-RECOVERY

Fires when MT momentum is falling but decelerating — the rate of selling pressure is slowing before price has confirmed a turn. Under the exhaustion hypothesis, this is the earliest detectable signal that a bear phase is losing force.

Core condition: `WD_MT < 0` (MT falling) AND `Accel_MT ≥ 0` (deceleration — the fall is slowing)

Note: this signal is named for what it anticipates (LT recovery), not for what the current LT score shows. At signal time, LT is still depressed. The signal fires on MT deceleration as an early read on exhaustion.

From v5 onward, the hard cutoff on WD_MT was replaced by a percentile-rank condition (WD_MT in the lower X-th percentile of its rolling distribution), making the threshold adaptive rather than fixed.

### SBST (Sustained Bottom / Sustained Top)

SBST labels require a score to remain in an extreme percentile band for a minimum number of consecutive weeks — not just a single-week breach. The sustained condition is what makes these regime labels rather than noise filters.

- **Sustained Bottom (SB):** LT or MT score in the lower percentile band for N consecutive weeks. Indicates a bear regime with persistent depressed readings — structural, not transient.
- **Sustained Top (ST):** LT or MT score in the upper percentile band for N consecutive weeks. Indicates a bull regime with persistent elevated readings.

Sustained extremes are consistent with the volume profile proxy interpretation: a score stuck at the bottom for several weeks reflects accumulated selling that has not yet resolved, analogous to a high-volume distribution zone on a price chart.

Threshold and window parameters are calibrated via sweep analysis (see [regime-detection.md](regime-detection.md)).

### BE Intersection Tier (v9+)

A high-precision sub-label requiring simultaneous agreement across multiple metric conditions. Produces fewer signals than the base SBST label but at a higher hit rate. Defined by the intersection of band conditions across two or more metrics — essentially requiring multiple independent exhaustion indicators to align at once.
