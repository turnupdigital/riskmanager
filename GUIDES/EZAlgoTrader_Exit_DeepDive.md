# EZ Algo Trader – Exit Logic Deep-Dive

> Focus:  **Smart Profit Locker · Fixed SL/TP · MA Exit · Bollinger-Band Exit · Basic Trend-Change Exit · Advanced Trend Exit (Linear Regression Candles) · Hybrid Exit Supervisor**  
> Artefacts inspected:  
> • `EZALGO_TRADER_DOCS.md` (design spec)  
> • `EZAlgoTrader.pine` (current main)  
> • `EZAlgoTrader copy.pine` (alternate build)

---

## Why this document?
This write-up is meant to be a **quick-start orientation** for anyone asking:

*“How do all the different exit systems in EZ Algo Trader really work, and **why are they not behaving the same across my two script versions?***

It takes the design notes in `EZALGO_TRADER_DOCS.md` and the two live code files – the main `EZAlgoTrader.pine` and the slightly diverged `EZAlgoTrader copy.pine` – then:

1.  Explains **each exit mechanism in plain English** (what it is supposed to do).
2.  Shows **how it is actually implemented** in the code.
3.  Flags **bugs, omissions, or divergences** that would stop it from working.

If you are new to the project, start with the high-level verdict below, then dive into the per-system tables.

---

### TL;DR – Is the exit logic “production ready”?

* **Core trade management (MA Exit, Fixed SL/TP, Smart Profit Locker)** – *mostly functional* but suffers from some missing variable resets (e.g. `strategyEntryPrice`) and unused UI inputs (`tp2/3`).
* **Bollinger-Band Tight Exit** – *UI only*: debug label prints but **no real stop tightening occurs**.
* **Basic Trend-Change Exit (legacy)** – never triggers because trend variables are never updated.
* **Advanced Trend Exit (Linear Regression Candles)** – logic present but will **not engage** unless user wires external LRC plots; also confirmation logic silently bypassed.
* **Hybrid Supervisor** – correctly toggles between SPL & Advanced TCE, but only the main script has the extra guards (`entryAllowed`, phantom fix).

> **Overall rating:**  
> • **Operational for live demo/backtest**, provided you rely on MA Exit, Fixed SL/TP or SPL.  
> • **Not complete** if you intend to use BB tight exits, legacy Trend-Change or Advanced LRC exit out-of-the-box.

---

## 1. Smart Profit Locker (SPL)

| Item | Implementation | Potential Bugs / Divergence |
|------|----------------|-----------------------------|
| **Trigger** | `shouldRunSPL` is `true` when `smartProfitEnable` **and** a position is open.  
Hybrid system can override via `hybridUsingSPL`. | 1. `strategyEntryPrice` is referenced when **long** distance is computed:  
```pinescript
smartDistance := ... ? ... : strategyEntryPrice * smartProfitVal / 100
```  
`strategyEntryPrice` is **never set** on entry in either script ⇒ possible *na* → SPL de-activates or uses stale value.  
2. `trailExitSent` is set once but **never reset** after flat → debug spam suppressed on subsequent trades. |
| **Action** | `strategy.exit()` with `trail_points` & `trail_offset`. | Works as long as `smartDistance` > 0.  *If* `smartDistance` is `na` the call is silently ignored (no exit!). |
| **Copy vs Main** | identical SPL code; issue is shared. |

---

## 2. Fixed SL / TP

| Item | Implementation | Potential Bugs |
|------|----------------|---------------|
| **Trigger** | `fixedEnable` & position open. | `tp2Size` & `tp3Size` inputs exist in UI but **never used** → user thinks 3 targets exist.  
`tp1Enable` must be **on** to get any take-profit; SL always active, TP optional. |
| **Action** | `strategy.exit("Fixed-Long/Short", stop, limit)` | OK.  Only the **first** exit fires; no multi-scale-out. |

---

## 3. MA Exit

Simple cross of price and chosen MA.

*Works as designed.*  No code differences.

Potential nit: recalculates only on bar close (intrabar exits removed) – doc still mentions "intrabar".

---

## 4. Bollinger-Band Exit (BB-Tight-Mode)

| Item | Spec (`DOCS`) | Code Reality |
|------|---------------|--------------|
| Purpose | When price tags an extreme band **during a trade** tighten the trailing stop aggressively. | Variable `bbExitTriggered` is set **and a debug label drawn**, but **no change** is applied to SPL or any exit distances.  The only reference afterwards is UI (“BB TIGHT” display line).  **Effectively NOP.** |

> ❗ **Bug:** Feature advertised in docs is not functional.

---

## 5. Basic Trend-Change Exit (legacy)

* Enabled by: `trendChangeExitEnable` **and** *not* using advanced TCE.  
* Detection relies on `trend1Long/Short`, `trend2Long/Short`, `trend3Long/Short` which are declared **but never assigned** (always `false`).

> ❗ **Bug:** Exit will **never fire**.  The status panel may show “TCE ON” yet nothing happens.

Copy & Main share the problem.

---

## 6. Advanced Trend Exit (Linear Regression Candles)

### Intended Flow (from docs)
1. Enter **Phase 0** on position open.  
2. **Phase 1** Trailing-stop guard until price moves `initialTrailingStop × ATR` in favour.  
3. **Phase 2** Profit-mode: wait for LRC *color change* or *MA cross* depending on `exitMode`.  
4. Optional **Re-Entry** after color flips back, provided supporting trends confirm.

### Why it may not fire
| Reason | Evidence |
|--------|----------|
| **LRC signal never true** | Inputs default to `close`.  Logic requires `lrcBullColorChange != close`. Unless user explicitly wires the LRC indicator, booleans stay `false`. |
| **Phase 1 never reached** | Needs `close >= entry + trailingStopDistance` (long) – if ATR large or market mean-reverting the threshold might be unreachable. |
| **`strategyEntryPrice` not updated** | Phase-1 distance uses `trendExitPrice`, which is set correctly. OK here. |
| **Flags not cleared** | After a color-change exit, `waitingForReEntry`