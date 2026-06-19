# Evergreen Liquidity Engine

A self-contained, interactive model of the core trade-off inside open-ended private-markets funds — built to illustrate the problem set the platform management teams at firms like Goldman Sachs XIG are actively solving as they scale evergreen vehicles to individual investors.

---

## What it models

Evergreen and semi-liquid funds promise individual investors quarterly liquidity on portfolios of illiquid, Level-3 assets. To honor that promise, managers hold a liquid sleeve — cash and near-cash instruments — against redemption demand they can't fully predict.

The tension is structural:

- Hold **too much** in the sleeve → cash drag bleeds returns against the private book
- Hold **too little** → the fund gates redemptions in a stress quarter, breaking the promise that sold it

This tool runs a **3,000-path Monte Carlo simulation** over 12 quarters to map that trade-off and find the efficient band: the smallest sleeve that keeps gating below 3% per quarter at the lowest possible drag cost.

---

## Features

- **Live frontier chart** — sweeps every sleeve size from 2% to 30% and plots gating probability vs. cash drag, with the efficient band highlighted
- **Fan chart** — shows the 10th/50th/90th percentile paths of the liquid sleeve over 3 years at your current settings
- **Four KPI readouts** — cash drag (bps/yr), gating probability per quarter, minimum liquidity coverage ratio, and expected ending sleeve
- **Did you know?** — 8 rotating facts covering evergreen fund mechanics, XIG context, and private markets structure
- All sliders recompute everything live in the browser — no server, no API

---

## How the model works

The engine runs entirely in the browser using vanilla JavaScript. Here is what happens on every slider change:

### 1. Inputs

Five parameters control the simulation:

| Parameter | What it represents |
|---|---|
| **Liquidity sleeve** | The share of NAV held in liquid instruments (cash, T-bills) ready to pay redeeming investors |
| **Expected net redemptions / quarter** | The average net outflow as a percentage of NAV each quarter — subscriptions minus redemptions |
| **Redemption volatility** | How unpredictable that net flow is; higher volatility means more stress quarters |
| **Net return on private book** | The annual return earned by the illiquid portfolio (private equity, credit, infrastructure) |
| **Yield on liquidity sleeve** | What the liquid portion earns while it waits — roughly the short-term rate environment |

### 2. Simulation loop

For each of the 3,000 independent paths, the model steps through 12 quarters:

**Growth** — the illiquid book grows at its net return and distributes a portion back to the sleeve each quarter (modeled at 5% annual distribution yield, representing capital returned from realizations and income).

**Redemption draw** — a net redemption demand is drawn from a normal distribution centered on your input mean with your input volatility. Negative draws represent net inflows, which grow the sleeve.

**Gating test** — redemptions are capped at 5% of NAV per quarter, the industry-standard semi-liquid gate. If the sleeve balance is smaller than the capped redemption amount, the quarter is recorded as a **gate event** — the fund cannot fully honor withdrawals.

**Rebalancing** — if the sleeve has grown above the target percentage (e.g. from distributions), the excess is redeployed into the illiquid book, keeping the portfolio at its intended allocation.

### 3. Output metrics

After all 3,000 paths complete, the model computes:

- **Cash drag (bps/yr)** — a deterministic calculation: `sleeve % × (private return − sleeve yield) × 10,000`. This is the annual return surrendered by holding liquid assets instead of deploying everything into the private book.
- **Gating probability (% per quarter)** — the share of all simulated quarters across all paths in which a gate event occurred. A well-managed evergreen fund targets this below 3%.
- **Minimum liquidity coverage ratio (LCR)** — the worst-case ratio of sleeve balance to capped redemption demand observed across a path, averaged across paths. A ratio below 1.0× means the sleeve was exhausted.
- **Ending sleeve (%)** — the average liquid share of NAV at the end of quarter 12.

### 4. The frontier

To draw the liquidity frontier, the model runs the full simulation at every sleeve size from 2% to 30% in 1% increments — 29 separate runs of 1,200 paths each. Each point on the curve is one candidate sleeve size. The **efficient band** is the contiguous range of sleeves where gating stays below 3% per quarter; the leftmost point in that band is the cheapest defensible configuration.

### 5. The fan chart

A separate run of 800 paths stores the full quarter-by-quarter sleeve trajectory for each path. The chart plots the 10th, 50th, and 90th percentile sleeve levels across those paths at each quarter, showing how the distribution of outcomes evolves over time and where the sleeve is most likely to run thin.

---

## Skills demonstrated

`Advanced financial modeling` · `Monte Carlo simulation` · `Liquidity & cash-flow analysis` · `Risk framework (LCR / gating)` · `Data visualization`

These map directly to the XIG Private Markets Platform Management analyst role skill set: advanced Excel-style modeling over large data inputs, liquidity models to avoid cash drag, Level-3 valuation context, and a risk management framework for fund-level controls.

---

## How to run

No build step. Open `evergreen-liquidity-engine.html` in any modern browser — or visit the live GitHub Pages link above.

---

## Context

Goldman Sachs XIG oversees $500B+ in client assets and is actively scaling its G-Series evergreen platform across private credit, private equity, infrastructure, real estate, and secondaries to wealth and individual investors. The liquidity sleeve sizing problem modeled here is the same one the Salt Lake City-based Platform Management team manages in production — balancing fund performance against the quarterly redemption promise.

Figures are illustrative. No proprietary or client data is used.

---

*Built by Allen Nelson · 2026*
