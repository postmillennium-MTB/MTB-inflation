# Mountain Biking & Inflation

Interactive dashboard comparing mountain bike and sporting-goods price trends against
overall US inflation, built on FRED, BLS, and BEA data. Part of the
[Post Millennium Renaissance MTB](https://postmillenniumrenaissance.com) tool suite.

**Live:** https://postmillennium-mtb.github.io/MTB-inflation/

## What's inside

Six tabs, all driven by the same underlying dataset:

| Tab | What it shows |
|---|---|
| **Annual % Change** | Year-by-year inflation rate — US CPI vs. the BLS bikes/sports-vehicles CPI — plus the spread between them |
| **Cumulative Index** | Both series compounded forward from a 2000 = 100 baseline |
| **COVID Era** | A 2017–2025 zoom-in with markers at the COVID, price-peak, and bust years, plus a timeline of the tariff/freight/demand events behind them |
| **Key Findings** | Six written takeaways, plus a comparison table against golf equipment, sporting-event tickets, and EU sport equipment |
| **Compare** | 10 adjacent FRED/BLS/BEA series, re-indexable to any base year, plus a ranked bar chart of % change since that year |
| **Affordability** | Hours-of-work-to-buy a bike by tier (entry/mid/premium), a live calculator using your own hourly wage, and a year-by-year snapshot table |

## Data sources

| Series | FRED ID | Source |
|---|---|---|
| US All-Items CPI | [FPCPITOTLZGUSA](https://fred.stlouisfed.org/series/FPCPITOTLZGUSA) | World Bank / FRED |
| Sports Vehicles / Bikes CPI | — (BLS direct) | BLS |
| PPI: Sporting Goods Mfg | [PCU339920339920](https://fred.stlouisfed.org/series/PCU339920339920) | BLS / FRED |
| PCE: Sporting Equipment Spending | [DSPGRC1A027NBEA](https://fred.stlouisfed.org/series/DSPGRC1A027NBEA) | BEA / FRED |
| PPI: Golf Equipment | [PCU3399203399204](https://fred.stlouisfed.org/series/PCU3399203399204) | BLS / FRED |
| PPI: Sporting Goods Retailers | [PCU451110451110](https://fred.stlouisfed.org/series/PCU451110451110) | BLS / FRED |
| PCE Price Idx: Sports & Rec | [DODRRG3A086NBEA](https://fred.stlouisfed.org/series/DODRRG3A086NBEA) | BEA / FRED |
| PCE Price Idx: Other Sport Goods | [DORIRG3A086NBEA](https://fred.stlouisfed.org/series/DORIRG3A086NBEA) | BEA / FRED |
| Admission to Sporting Events CPI | — (BLS direct) | BLS |
| Real PCE: Sporting Equipment | [DSPGRX1A020NBEA](https://fred.stlouisfed.org/series/DSPGRX1A020NBEA) | BEA / FRED |
| Average Hourly Earnings (wages) | [AHETPI](https://fred.stlouisfed.org/series/AHETPI) (BLS CES0500000008) | BLS / FRED |

Representative bike MSRP by tier — entry hardtail (Trek Marlin / Rockhopper class), mid-range
full-suspension (Stumpjumper Comp / Fuel EX class), and premium enduro (Yeti SB150 / Megatower
class) — comes from Bicycle Association, PeopleForBikes, PinkBike, and Singletracks market
reporting, not a FRED/BLS series.

### A note on precision

Not every number here carries the same confidence:

- **Exact** — FRED series values, BLS CPI figures
- **† Approximate** — the two PPI series (Sporting Goods Mfg, Golf Equipment) are annual
  averages derived from monthly BLS data
- **‡ Estimated** — pre-2021 PCE figures are approximated from BEA NIPA tables; PCE Sport
  Equip ($) is nominal spending (price × volume), not a pure price index, so it moves for
  reasons beyond inflation alone
- Bike MSRP figures are representative-model estimates, not a formal price index

Series coverage runs through 2024–2025 depending on the metric; written commentary is
current to May 2026.

## Tech notes

Single self-contained `index.html`. Plain JavaScript — no React, no JSX, no build step.
Charts are [Chart.js](https://www.chartjs.org/) v4, loaded from jsDelivr's CDN; everything
else has zero external dependencies.

**Why vanilla JS:** this repo previously held a raw React/JSX file with no HTML wrapper
around it. GitHub Pages has no build step, so it just served the source code as text
instead of a working page. A single plain-JS file sidesteps that entirely — it runs the
instant GitHub serves it, nothing to compile.

### Updating data for a new year
All data lives in one block near the top of `index.html` (search for `─── DATA`). Each
series is a plain `{year: value, ...}` object — add a new year's key to the relevant
object(s) and the charts, tables, and scorecards pick it up automatically. The exception
is the four Affordability hero cards and the six Key Findings cards: those are hand-written
takeaways and won't update themselves, so revisit their numbers if a new year changes the
story.

### Deploying
Commit `index.html` straight to `main` — that's the branch GitHub Pages serves from for
this repo. No build step, no CI, nothing else to run.

## Structure

```
MTB-inflation/
└── index.html   ← everything: data, styling, charts, all six tabs
```
