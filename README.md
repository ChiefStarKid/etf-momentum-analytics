# ETF Momentum Analytics — Exhaustion Detection via Rate-of-Change Analysis

If you trade US equity ETFs — whether using options strategies like put credit spreads, or managing directional exposure across sector ETFs — one of the hardest problems is identifying when a trend is losing force before price confirms it. This project is a systematic attempt to solve that problem using rate-of-change analysis on weekly momentum scores.

It's built for quantitative traders, options strategists, and systematic investors working with ETF momentum signals who want an earlier read on exhaustion than price action alone provides. The backtest covers 15 US equity ETFs (SPY, QQQ, DIA, IWM, and 11 sector ETFs) over multi-year weekly data, with forward return measurements at 15–120 day horizons.

---

Weekly momentum scores derived from heatmap-style signals are, by design, lagging indicators. They reflect accumulated price history — not forward prediction. This project starts from that constraint and asks a different question: **not whether the scores predict direction, but whether their rate of change reveals buying or selling exhaustion that price action alone does not expose.**

The hypothesis is that heatmap scores function as a proxy for bulk volume profile analysis. A score that is falling but decelerating suggests that selling pressure is dissipating — the trend may still be intact on the chart, but the underlying force sustaining it is weakening. Catching that transition early, before price confirms it, is the analytical problem this project works on.

The backtest presented here does not directly test exhaustion against an alternative measure. What it does is measure forward signal quality for rate-of-change conditions derived from LT/MT/ST scores — and finds results consistent with the exhaustion hypothesis. Directly comparing these signals against price-action exhaustion indicators (RSI divergence, volume climax, etc.) is identified as future work.

---

## What This Project Does

- Extracts LT (long-term), MT (medium-term), and ST (short-term) momentum scores from a proprietary weekly heatmap signal source covering 15 US equity ETFs (SPY, QQQ, DIA, IWM, and 11 sector ETFs)
- Computes rate-of-change metrics: weighted momentum direction (WD), acceleration (Accel), and delta across timeframes — the signals that capture *how fast* scores are moving, not just where they are
- Labels conditions using a regime-detection framework (LT-RECOVERY, SBST Bear/Bull) built around deceleration and sustained extremes
- Measures forward signal quality using **Day-Avg** (% of forward trading days above reference close) and **Never Below** (% of trades with zero down-closes) across horizons of 15 to 120 days
- Runs sweep analyses across percentile-rank thresholds, window sizes, and metric combinations to find stable, generalisable labelling criteria

---

## Key Findings

1. **WD_MT < 0 + Accel_MT ≥ 0 is the strongest two-metric condition.** MT momentum is falling, but the rate of fall is decelerating — a selling exhaustion pattern. Day-Avg hits 81–83% at 30–60 day horizons (n=20), versus 56% for the same WD_MT < 0 group without the Accel filter. The Accel condition — the deceleration filter — is what drives the gap.

2. **LT-RECOVERY signals show persistent edge across all measured horizons.** Day-Avg ranges from 76% at 15 days to 88% at 60 days (n=15–18). The edge does not decay at longer horizons within the measurement window, which is consistent with a regime-level exhaustion read rather than a short-term reversion.

3. **MACD_LT is inversely correlated with forward returns and was removed.** High MACD_LT means LT momentum is still accelerating — no exhaustion signal. This predicted *weaker* forward performance, consistent with the hypothesis: where the trend is still building, exhaustion has not set in. The finding reversed the initial assumption and removed MACD_LT from all subsequent signal definitions.

4. **Percentile-rank thresholds outperform fixed thresholds in stability.** From v5 onward, replacing absolute metric cutoffs with rolling percentile-rank bands produced more consistent labelling across different sample windows — important for a signal that must remain calibrated as market conditions shift.

---

## What These Results Do and Do Not Show

The forward return results are consistent with the exhaustion hypothesis. They show that rate-of-change conditions identify moments where price tends to hold. They do not yet establish *why* — whether the edge comes from exhaustion specifically, or from some other property of these score transitions. That distinction requires comparing these signals against independent exhaustion measures, which is future work.

The scores themselves are not useful for trade entry timing — by the time a score turns, the move has typically begun. The rate-of-change metrics (WD, Accel) are earlier reads, but should be treated as exit and position management inputs rather than entry triggers until further validated.

---

## Repository Structure

```
README.md                       ← this file
CHANGELOG.md                    ← analytical version history (v1.0 → v10.2)
methodology/
  signal-design.md              ← LT/MT/ST framework, exhaustion hypothesis, metric definitions
  backtest-design.md            ← measurement methodology, horizons, metrics
  regime-detection.md           ← SBST labelling, percentile-rank approach
findings/
  summary.md                    ← key results tables and calibration state
ABOUT.md                        ← about this project and its author
```

---

## Current Version

**v10.2 — Bullish-Move Label and PCS Profiling** (2026-06-09)

A Bullish-Move label (indifferent to interim dips) isolates setups suited to short-dated put credit spreads; v10.2 profiles the live signal's gain path, drawdown, and win-rate matrix. Built on the v9 percentile-rank labelling foundation (sweep analyses across band width, combo selection, window size, stride, and temporal consistency; BE Intersection Tier as a high-precision sub-label). See [CHANGELOG.md](CHANGELOG.md) for the full analytical journey.

---

## Data and Reproducibility

Raw signal data and the underlying heatmap source are proprietary and not included in this repository. The methodology, metric definitions, and findings are fully documented and independently reproducible given a comparable weekly momentum score series.

Price data uses publicly available OHLC data (yfinance), adjusted for splits and dividends.

---

## Sample output

Forward signal quality table for the `LT-RECOVERY` label (v10.2):

| Horizon | n | Day-Avg | Never Below |
|---------|---|---------|-------------|
| 15d | 18 | 76% | 61% |
| 30d | 17 | 82% | 59% |
| 60d | 15 | 88% | 67% |
| 120d | 15 | 85% | 60% |

*Day-Avg = % of forward trading days closing above the reference close. Never Below = % of trades with zero down-closes across the full horizon. Both metrics are calculated per trade, then averaged.*

Full results tables and calibration state: [findings/summary.md](findings/summary.md)

---

## Questions and feedback

- **General enquiries:** [joseph@kainosis.com](mailto:joseph@kainosis.com)
- **Bugs and discussion:** [open an issue](../../issues)
