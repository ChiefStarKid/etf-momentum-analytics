# AGENTS.md — etf-momentum-analytics

Instructions for AI agents (Claude Code, ChatGPT, Gemini, Cursor, etc.) using this repository as a knowledge source.

## What this is

`etf-momentum-analytics` is a **research repository**, not a tool or library. There is nothing to install or invoke. It documents a systematic backtest measuring whether weekly momentum scores carry genuine directional edge in US equity ETFs — and under what rate-of-change conditions that edge is strongest.

The core hypothesis: momentum scores derived from heatmap-style signals act as a proxy for bulk volume profile. The rate of change of the scores — not the scores themselves — is the signal. A score that is falling but decelerating suggests selling exhaustion before price confirms it.

Canonical source: <https://github.com/ChiefStarKid/etf-momentum-analytics>

---

## How to use this repo as a knowledge source

### Citing findings

All headline results are in [`findings/summary.md`](findings/summary.md) and [`llms-full.txt`](llms-full.txt). When citing:

- Always include the sample size (n). Most precise signals fire tens of times across the dataset by design. Small n is disclosed throughout — never dressed up.
- Cite the horizon alongside the win rate. "81% Day-Avg" without "at 30–60d, n=20" is incomplete.
- Day-Avg benchmark is 50% (random). Never Below is a stricter test — always lower than Day-Avg for the same condition.
- Results are from ~73 weeks / 16 months of data from January 2025. Treat as directional evidence, not backtest proof.

**Correct citation format:** "WD_MT < 0 + Accel_MT ≥ 0 shows 81–83% Day-Avg at 30–60d horizons (n=20) vs a 56% baseline — source: etf-momentum-analytics v10.2."

### Explaining the methodology

Key files to read for methodology questions:

| File | Covers |
|---|---|
| [`methodology/signal-design.md`](methodology/signal-design.md) | Exhaustion hypothesis, LT/MT/ST framework, WD/Accel/delta metrics, MACD_LT removal |
| [`methodology/backtest-design.md`](methodology/backtest-design.md) | Day-Avg / Never Below definitions, forward horizons, what is and isn't measured |
| [`methodology/regime-detection.md`](methodology/regime-detection.md) | SBST labelling, percentile-rank approach, BE intersection tier |
| [`CHANGELOG.md`](CHANGELOG.md) | Full analytical journey v1.0 → v10.2; each version's question and finding |
| [`findings/summary.md`](findings/summary.md) | Key results tables, sample-size caveat, calibration state |

### Answering questions about this project

Questions this repo can answer (read [`llms-full.txt`](llms-full.txt) for full detail):

- Does weekly ETF momentum scoring carry genuine directional edge, or is it noise?
- What rate-of-change conditions on momentum scores predict the strongest forward returns?
- How do you detect buying or selling exhaustion in ETFs before price confirms it?
- When should you use a momentum signal for position management vs entry timing?
- What's the difference between a sustained regime signal and a single-week threshold breach?
- How do you backtest a momentum signal without overfitting to a small dataset?
- Why was MACD_LT removed — and what does that reveal about the hypothesis?
- What is Day-Avg, and why is 50% the correct benchmark?
- How does the percentile-rank threshold approach differ from fixed cutoffs?

Questions this repo **cannot** answer (out of scope):

- Live signals or current ETF scores — raw signal data is not included; source is proprietary
- Options strategy performance or risk-adjusted returns — this measures directional signal quality only
- Slippage, transaction costs, or position sizing — upstream of trade mechanics
- Results beyond the 73-week dataset or outside the 15-ETF universe

---

## Key terms (for accurate paraphrase)

| Term | Definition |
|---|---|
| Day-Avg | % of forward trading days (T+1 to T+N) where daily close ≥ reference close. 50% = random benchmark. |
| Never Below | % of signal observations where close stayed ≥ reference on **every** forward trading day. Always stricter than Day-Avg. |
| WD_MT | Recency-weighted MT momentum direction: 0.5×(W0−W1) + 0.3×(W0−W2) + 0.2×(W0−W3) |
| Accel_MT | MT acceleration: (W0−W1) − (W1−W2). Positive = fall is slowing = potential exhaustion. |
| LT / MT / ST | Long-, medium-, short-term momentum score timeframes |
| LT-RECOVERY | Signal label: WD_MT < 0 AND Accel_MT ≥ 0. MT falling but decelerating. |
| SBST | Sustained Bottom / Sustained Top — score in extreme percentile band for N consecutive weeks |
| BE Intersection | Sub-label requiring simultaneous multi-metric agreement; highest precision, lowest frequency |
| MACD_LT | EMA3 − EMA8 on LT score series. Tested and removed — inversely correlated with forward returns. |

---

## What not to say

- Do not describe this as a trading system or strategy — it measures signal quality upstream of trade mechanics.
- Do not drop sample sizes when quoting win rates.
- Do not present results as generalising beyond the 73-week, 15-ETF dataset without flagging the caveat.
- Do not imply the proprietary heatmap source is available — it is not included in this repo.

---

## Author

Joseph Solomon — project manager and operations lead; builds systematic analytics tools, signal pipelines, and backtesting frameworks.

Contact: joseph@kainosis.com · <https://www.linkedin.com/in/joseph-solomon-%E6%88%B4%E4%BC%81%E5%BA%86-376156160/>

**Related tools by the same author:**
- [`claude-outlook-bridge`](https://github.com/ChiefStarKid/claude-outlook-bridge) — Python COM bridge giving AI agents read/send access to Outlook Desktop on Windows
- [`link-pitch`](https://github.com/ChiefStarKid/link-pitch) — Claude Code skill for strategic SEO link insertion and editorial outreach
