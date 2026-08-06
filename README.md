# Mountain Biking & Inflation

Interactive dashboard comparing mountain bike and sporting-goods price trends against
overall US inflation, built on FRED, BLS, and BEA data. Part of the [Post Millennium Renaissance MTB](https://postmillenniumrenaissance.com) tool suite.

**Live:** <https://postmillennium-mtb.github.io/MTB-inflation/>
**Embedded at:** <https://postmillenniumrenaissance.com/MTB-inflation/>

## What's inside

Six tabs, all driven by the same underlying dataset:

| Tab | What it shows |
|---|---|
| **Annual % Change** | Year-by-year inflation rate — US CPI vs. the BLS bikes/sports-vehicles CPI — plus the spread between them. The Play button sweeps the chart left to right. |
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

### On the headline "US CPI since 2000" figure

The dashboard reports US CPI growth since 2000 as **+89%**, calculated by compounding the
`fredCPI` series (FRED FPCPITOTLZGUSA) year by year. An earlier version of this tool
displayed **+83%**, which came from CPI-U index levels — a different series than the one the
Cumulative Index chart actually plots. The card and the chart beneath it therefore disagreed.

Both figures are defensible; they just answer the question with different data. The tool now
computes the number from the series it charts, so the two can't drift apart again. If the
CPI-U basis is preferred, that's the value to change — see "Numbers that compute themselves"
below.

## Tech notes

Single self-contained `index.html`. Plain JavaScript — no React, no JSX, no build step.
Charts are [Chart.js](https://www.chartjs.org/) v4, loaded from cdnjs (a jsDelivr mirror URL
is noted in a comment beside the script tag); everything else has zero external dependencies.

**Why vanilla JS:** this repo previously held a raw React/JSX file with no HTML wrapper
around it. GitHub Pages has no build step, so it just served the source code as text
instead of a working page. A single plain-JS file sidesteps that entirely — it runs the
instant GitHub serves it, nothing to compile.

**Why `target="_top"` on the PMR button:** the tool is displayed inside an iframe on
postmillenniumrenaissance.com. Without `target="_top"` the PMR link loads the homepage
*inside the frame* — the page visibly changes, but the browser's address bar still reads
`/MTB-inflation/`. Any future link meant to leave the tool needs the same attribute.
(Outbound links to FRED and articles use `target="_blank"`, which is already frame-safe.)

## How the file is organised

Everything lives in `index.html`, in this order. Each section is marked with a banner
comment you can search for.

| Search for | What's there |
|---|---|
| `const PALETTE` | Every accent colour in the tool, named once |
| `const THEMES` | The three colour schemes |
| `// DATA` | All the FRED/BLS/BEA series |
| `const CONFIG` | Base years, thresholds, animation timings |
| `// STATE` | Everything the UI can change at runtime |
| `// CHART.JS PLUGIN` | Reference lines, and the Play sweep |
| `// TAB 0` … `// TAB 5` | One block per tab |
| `const TAB_CONFIG` | The list of tabs |

### Colours and themes live in one place

`PALETTE` and `THEMES` sit in a `<script>` block at the top of `<head>`. That script
*generates* the CSS custom properties (`--orange`, `--panel`, `--grid`, …) at page load —
there are no hardcoded colour values in the stylesheet or in the chart code. `PALETTE.orange`
in JavaScript and `var(--orange)` in CSS are guaranteed to be the same colour.

**To add a colour scheme:** copy one entry in `THEMES`, change its `key`, `name`, `dot`, and
`vars`. That's the only edit. The theme dots in the header build themselves from the array,
and open charts re-read the new values when a dot is clicked.

### Adding a tab

1. Add a `<section class="tab-panel" id="tab-6">` to the HTML.
2. Write an `initTab6()` function.
3. Add one entry to `TAB_CONFIG`: `{ title:"…", panelId:"tab-6", init:initTab6 }`.

The nav buttons and the lazy-loading both read from `TAB_CONFIG`. A tab's `init` runs the
first time it's opened, not on page load, so charts for tabs nobody visits are never built.

### Adding a chart

Use the shared factories rather than writing Chart.js options from scratch —
`makeLineChart(canvasId, {...})` and `makeBarChart(canvasId, {...})`, with `lineDataset()`
for individual series. They carry the theming, tooltips, fonts, and axis styling, so a new
chart matches the rest automatically and a styling change applies everywhere at once.
Canvas heights use the named classes `.chart-lg` / `.chart-md` / `.chart-sm` / `.chart-xs`.

### Updating data for a new year

All data lives in one block near the top of `index.html` (search for `// DATA`). Each series
is a plain `{year: value, ...}` object — add a new year's key to the relevant object(s) and
the charts, tables, and scorecards pick it up automatically.

**Numbers that compute themselves** (no manual edit needed): the header stat cards for US CPI
and Bikes CPI, the "gap" card and the two figures in the Cumulative Index paragraph (all from
`TOTALS`), the Compare tab's CPI baselines and traffic-light colours, and the Affordability
hero cards, calculator, and snapshot table (all from `AFFORD_ROWS` / `BIKE_PRICES`).

**Numbers that do NOT update themselves** — revisit these if a new year changes the story:

- The six **Key Findings** cards (`initTab3`) — hand-written takeaways
- The **COVID-peak** hero card on Affordability — a written takeaway, unlike the three beside it
- The **1978–2026 long-run averages** (1.73%/yr, 3.43%/yr) — outside the range of data in this file
- The three header cards sourced outside this file: **Sports Tickets**, **EU Sport Equipment**, **MTB MSRP Surge**
- Each metric's `recentVal` / `recentNote` in `COMPARE_METRICS`
- The `note` text on each series in `EXTRA_SERIES`

### Deploying

Commit `index.html` straight to `main` — that's the branch GitHub Pages serves from for
this repo. No build step, no CI, nothing else to run.

## Structure

```
MTB-inflation/
├── index.html            ← everything: data, styling, charts, all six tabs
├── favicon.ico
├── favicon-32x32.png
├── apple-touch-icon.png
└── README.md
```
