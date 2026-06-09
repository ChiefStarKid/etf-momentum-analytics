# Signal Design

## Data Source

Weekly LT (long-term), MT (medium-term), and ST (short-term) momentum scores are extracted from a proprietary weekly heatmap signal source. Each score is a numeric value on a continuous scale. Scores are available for 15 US equity ETFs:

**Broad market:** SPY, QQQ, DIA, IWM
**Sectors:** XLK, XLF, XLV, XLY, XLC, XLI, XLP, XLE, XLRE, XLB, XLU

Scores are extracted weekly (Friday) and stored in a structured time-series format. The sample begins 2026-03-06 and grows with each weekly update.

---

## Derived Metrics

Six metrics are computed from the raw LT/MT/ST scores. W0 = current week, W1 = one week prior, W2 = two weeks prior, W3 = three weeks prior.

| Metric | Formula | What it measures |
|---|---|---|
| **WD_MT** | `0.5×(MT_W0−MT_W1) + 0.3×(MT_W0−MT_W2) + 0.2×(MT_W0−MT_W3)` | Recency-weighted MT momentum direction. Positive = MT rising; negative = MT falling. |
| **Accel_MT** | `(MT_W0−MT_W1) − (MT_W1−MT_W2)` | MT acceleration. Positive = fall slowing or rise accelerating. |
| **LT_delta** | `LT_W0 − LT_W1` | Single-week change in LT score. |
| **LT_Accel** | `(LT_W0−LT_W1) − (LT_W1−LT_W2)` | LT acceleration. Positive = LT fall slowing. |
| **ST_delta** | `ST_W0 − ST_W1` | Immediate ST direction. |
| **ST_Accel** | `(ST_W0−ST_W1) − (ST_W1−ST_W2)` | ST acceleration. |

---

## MACD_LT — Tested and Removed

MACD_LT (EMA3 − EMA8 applied to the LT score series) was included in the initial metric set. Bucket analysis showed it is *inversely* correlated with forward returns: high MACD_LT (strong LT bull trend) predicted weaker outcomes; low MACD_LT predicted stronger outcomes.

This reversed the initial assumption that MACD_LT would confirm LT momentum. MACD_LT was removed from all signal definitions after v1.0.

---

## Signal Labels

### LT-RECOVERY

Fires when LT momentum is negative (falling) but showing deceleration — the LT regime is deteriorating but the rate of deterioration is slowing. This captures the early stage of a bear→bull transition before the LT score itself reverses.

Core condition: `WD_MT < 0` AND `Accel_MT ≥ 0`

From v5 onward, the hard cutoff on WD_MT was replaced by a percentile-rank condition (WD_MT in the lower X-th percentile of its rolling distribution), making the threshold adaptive to the current score environment.

### SBST (Sustained Bottom / Sustained Top)

SBST labels require a score to remain in an extreme percentile band for a minimum number of consecutive weeks, not just a single-week breach.

- **Sustained Bottom (SB):** LT or MT score in the lower percentile band for N consecutive weeks → bear regime signal
- **Sustained Top (ST):** LT or MT score in the upper percentile band for N consecutive weeks → bull regime signal

The sustained condition filters out transient spikes and focuses on regime persistence. Threshold and window parameters are calibrated via sweep analysis (see [regime-detection.md](regime-detection.md)).

### BE Intersection Tier (v9+)

A high-precision sub-label requiring agreement across multiple metric conditions simultaneously. Produces fewer signals than the base SBST label but at a higher hit rate. Defined by the intersection of band conditions across two or more metrics.
