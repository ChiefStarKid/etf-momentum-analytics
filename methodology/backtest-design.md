# Backtest Design

## Objective

This backtest measures **signal quality** in isolation — not strategy performance. The question is: given that a signal fired at time T, how reliably does price hold above the reference close over the following N days?

Trade mechanics (entry sizing, stop placement, exit rules) are deliberately excluded. This keeps the signal quality assessment independent of any particular trade structure.

---

## Reference Price

**Friday close** on the week the signal fires. All forward return measurements are relative to this price.

---

## Forward Horizons

Eight horizons at 15-day intervals:

| Horizon | Calendar days |
|---|---|
| H1 | 15d |
| H2 | 30d |
| H3 | 45d |
| H4 | 60d |
| H5 | 75d |
| H6 | 90d |
| H7 | 105d |
| H8 | 120d |

Longer horizons (90d+) show reduced sample sizes in early versions of the backtest because the dataset did not extend far enough back to provide 90+ days of forward data for older signals. These populate as the dataset grows.

---

## Measurement Metrics

Two metrics are computed per signal observation per horizon:

### Day-Avg

The percentage of forward trading days (from T+1 to T+N) where the daily Close was at or above the reference close.

- **Benchmark:** 50% = random (a coin-flip process would score 50%)
- **Interpretation:** A Day-Avg of 80% means that on 80% of trading days within the hold window, the ETF closed above the signal's reference price.

### Never Below

The percentage of signal observations where the daily Close remained at or above the reference close on *every single trading day* within the hold window — no down-closes below the reference at any point.

- **Interpretation:** A Never Below of 40% means that 40% of signals had zero down-closes relative to the reference price over the entire horizon.
- Never Below is always lower than Day-Avg. It is a stricter test of clean, uninterrupted holds.

---

## Price Data

Daily OHLC data sourced from yfinance, adjusted for splits and dividends. Covers SPY, QQQ, DIA, IWM, and 11 sector ETFs. Data range begins 2025-01-01.

---

## Sample Size Disclosure

The signal dataset is live and grows weekly. Sample sizes (n) are reported alongside all results. Findings at small n (< 15) should be treated as directional rather than conclusive. Results tables note when longer horizons have truncated n due to the measurement window boundary.

---

## What This Backtest Does Not Measure

- Win rate under specific entry/exit rules
- Risk-adjusted return (Sharpe, drawdown)
- Options strategy performance
- Slippage, transaction costs, or liquidity

For those questions, the signal quality results here serve as an upstream input — establishing whether the signal carries directional edge before layering in trade mechanics.
