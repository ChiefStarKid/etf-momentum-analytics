# ETF Momentum Analytics — Signal Quality Backtest

This project measures the **directional edge** of momentum signals applied to US equity ETFs. Rather than backtesting a trading strategy, it isolates signal quality: given a signal fired at time T, how reliably does price hold above the reference close across forward horizons of 15 to 120 days?

The goal is to establish whether **LT/MT/ST regime scores** derived from a proprietary weekly heatmap source carry statistically meaningful forward return predictability — and if so, under what conditions that edge is strongest.

---

## What This Project Does

- Extracts LT (long-term), MT (medium-term), and ST (short-term) momentum scores from a weekly heatmap signal source covering 15 US equity ETFs (SPY, QQQ, DIA, IWM, and 11 sector ETFs)
- Computes derived metrics: weighted momentum direction (WD), acceleration (Accel), and delta across timeframes
- Labels signals using a systematic regime-detection framework (LT-RECOVERY, SBST Bear/Bull)
- Measures forward signal quality using **Day-Avg** (% of forward trading days above reference close) and **Never Below** (% of trades with zero down-closes) across 8 horizons
- Runs sweep analyses across percentile-rank thresholds, window sizes, and metric combinations to find stable, generalisable labelling criteria

---

## Key Findings

1. **WD_MT < 0 + Accel_MT ≥ 0 is the strongest single two-metric condition.** When MT momentum is falling but decelerating (recovering), Day-Avg hits 81–83% at 30–60 day horizons (n=20), versus a 56% baseline for the same WD_MT < 0 group without the Accel filter.

2. **LT-RECOVERY signals show persistent edge across all measured horizons.** Day-Avg ranges from 76% at 15 days to 88% at 60 days (n=15–18 depending on horizon). The edge does not decay at longer horizons within the measurement window, suggesting the signal captures a durable regime shift rather than a short-term reversion.

3. **MACD_LT is inversely correlated with forward returns and was removed.** High MACD_LT (strong LT bull momentum) predicted *weaker* forward performance. This finding reversed the initial assumption and shaped the signal design going forward.

4. **Percentile-rank thresholds outperform fixed thresholds in stability.** From v5 onward, replacing absolute metric cutoffs with rolling percentile-rank bands reduced false positives and improved consistency across different sample windows.

---

## Repository Structure

```
README.md                       ← this file
CHANGELOG.md                    ← analytical version history (v1.0 → v9)
methodology/
  signal-design.md              ← LT/MT/ST framework, metric definitions
  backtest-design.md            ← measurement methodology, horizons, metrics
  regime-detection.md           ← SBST labelling, percentile-rank approach
findings/
  summary.md                    ← key results tables and calibration state
ABOUT.md                        ← about this project and its author
```

---

## Current Version

**v9 — Weekly Calibrated** (2026-06-07)

Percentile-rank labelling across all signal types. Sweep analyses cover band width, combo selection, window size, stride, and temporal consistency. BE Intersection Tier introduced as a high-precision sub-label. See [CHANGELOG.md](CHANGELOG.md) for the full analytical journey.

---

## Data and Reproducibility

Raw signal data and the underlying heatmap source are proprietary and not included in this repository. The methodology, metric definitions, and findings are fully documented and independently reproducible given a comparable weekly momentum score series.

Price data uses publicly available OHLC data (yfinance), adjusted for splits and dividends.
