# Indicator Integration Audit – Wonder2, Volumatic, Adaptive & EZAlgoTrader

> Reviewed files:  
> • `BuySellIndicators/BUYSELLSIGNALS/Wonder2.pine`  
> • `Trend/Volumatic.pine`  
> • `Trend/Adaptive.pine`  
> • `EZAlgoTrader.pine`

---

## 1 · Purpose
The EZAlgoTrader strategy relies on **external indicator plots** to feed its multi-signal engine (signals are connected via the TradingView “Indicator → Source” picker).  Any indicator that lacks a clean numeric plot (1/0, >0, etc.) will silently fail – the strategy will see the *default close price* instead, which is filtered out (`src != close`).

We inspect the three indicators currently under consideration and assess:
1. Whether they emit proper numeric plots.  
2. Which plots map to *entry* vs *trend-confirmation* signals.  
3. Required code changes to make them compatible.  
4. Any logic issues that could impact EZAlgoTrader’s performance.

---

## 2 · TL;DR Compatibility Matrix
| Indicator | Entry Long Plot | Entry Short Plot | Trend Plot(s) | Ready for EZAlgoTrader? | Severity |
|-----------|-----------------|------------------|---------------|-------------------------|----------|
| **Wonder2** | `buySignal` ✔️ | `sellSignal` ✔️ | `bullishTrend` / `bearishTrend` ✔️ | **Yes** – emits numeric plots (0/1). | — |
| **Volumatic** | **None** (only plotshape) | **None** | `is_trend_up` bool but *not plotted* | **No** – needs 3 `plot()` lines added. | 🟥 |
| **Adaptive (AlgoAlpha SuperTrend)** | **None** (only plotshape) | **None** | `dir` (±1) numerically available but not plotted | **No** – needs explicit signal plots. | 🟥 |

---

## 3 · Detailed Findings
### 3.1 Wonder2.pine
* Lines 62-66:
  ```pine
  buySignal  = isAbove  ? 1 : 0
  sellSignal = isBelow  ? 1 : 0
  bullishTrend = flag_green ? 1 : 0
  bearishTrend = flag_red   ? 1 : 0
  ```
* Lines 70-74 plot those series.
* **Integration mapping:**
  | EZAlgo field | Select this plot |
  |--------------|-----------------|
  | Signal Long Source | `Buy Signal` |
  | Signal Short Source | `Sell Signal` |
  | Trend1LongSrc | `Bullish Trend` |
  | Trend1ShortSrc | `Bearish Trend` |
* **No code changes required.**

### 3.2 Volumatic.pine
* Only `plotshape()` calls at lines 174-176.
* Numeric boolean variables exist: `trend_cross_up`, `trend_cross_down`, `is_trend_up`.
* **Problem:** Without a `plot()` the series cannot be picked in the strategy.
* **Recommended patch:**
  ```pine
  plot(trend_cross_up  ? 1 : 0, title="Volumatic Long Signal",  display=display.data_window)
  plot(trend_cross_down? 1 : 0, title="Volumatic Short Signal", display=display.data_window)
  plot(is_trend_up      ? 1 : 0, title="Volumatic Trend Up",    display=display.data_window)
  ```
* **Severity 🟥** – currently unusable → signals ignored.

### 3.3 Adaptive.pine (ML Adaptive SuperTrend)
* Uses `plotshape(ta.crossunder(dir, 0) …)` where `dir` flips between +1 (bear) and −1 (bull).
* No numeric signal plots.
* Variables available:
  * `ta.crossunder(dir, 0)` → **Bullish Trend Shift** (long entry)
  * `ta.crossover(dir, 0)`  → **Bearish Trend Shift** (short entry)
  * `dir < 0` (bull trend), `dir > 0` (bear trend) → could serve as trend filters.
* **Recommended patch:**
  ```pine
  plot(ta.crossunder(dir, 0) ? 1 : 0, title="Adaptive Long Signal",  display=display.data_window)
  plot(ta.crossover(dir, 0)  ? 1 : 0, title="Adaptive Short Signal", display=display.data_window)
  plot(dir < 0 ? 1 : 0, title="Adaptive Trend Up", display=display.data_window)
  plot(dir > 0 ? 1 : 0, title="Adaptive Trend Down", display=display.data_window)
  ```
* **Severity 🟥** – unusable until plots added.

---

## 4 · EZAlgoTrader Integration Checklist
1. **Connect Wonder2 plots** as described (works immediately).
2. **Patch Volumatic & Adaptive** with numeric plots; republish or use Pine’s “Add to chart as strategy” trick.
3. **Update strategy inputs** (Trend2/Trend3) to point at new plots.
4. Consider **standardising**: all trend indicators should output:
   * EntryLongSignal (0/1)
   * EntryShortSignal (0/1)
   * TrendBull (0/1) / TrendBear (0/1)

---

## 5 · Production-Readiness Impact
| Risk | Effect if not fixed |
|------|--------------------|
| Volumatic & Adaptive missing plots | Strategy silently ignores these indicators; any confluence logic relying on them is bypassed. Hybrid/TCE may never trigger. |
| Inconsistent plot naming | User may connect wrong plot (e.g. chooses `Trend Up` as entry). Leads to unexpected trade direction. |

**Overall:** EZAlgoTrader is only as good as the connected indicators.  Ensure every external script outputs *numeric 0/1 series* for each role.  Add unit tests (TradingView alerts) to confirm signals fire before live trading.

---

## 6 · Action Items
1. **Add plot lines** to `Volumatic.pine` and `Adaptive.pine` (see code snippets).  
2. **Republish or import** fixed indicators; reconnect in EZAlgoTrader inputs.  
3. **Verify** in data-window that the selected source shows 0/1 values, not price.  
4. **Run a 30-day back-test**; confirm trade counts now reflect signals and trend filters work.  
5. **Document** naming conventions for any future indicator integration. 