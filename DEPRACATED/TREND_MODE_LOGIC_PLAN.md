# Trend-Mode & Exit-Mode Logic – Desired vs Current

> Drafted 2025-07-31 – *work-in-progress reference for upcoming fixes*

---

## 1.  Desired / Intended Behaviour (per user spec)

1. **Entry logic – always on.**  
   • Every connected signal (`LuxAlgo`, `UTBot`, …) may fire LONG or SHORT at any bar.  
   • Pyramiding is allowed up to the global `pyramidLimit`. Each new *same-side* pulse adds `positionQty` contracts.

2. **Mode switching rules**  
   • **SCALP** – default while position < *Trend-Size Threshold* **OR** signal-power < *Trend-Power Threshold*.  
   • **TREND** – becomes active *only after* both conditions are met.  
   • Mode resets to SCALP immediately after flat (position size == 0).

3. **Exit systems by mode**

   | Mode   | Primary Exit Mechanism | Backup Exits (should be OFF) |
   | ------ | ---------------------- | ---------------------------- |
   | SCALP  | Smart Profit Locker ‑or- MA/fixed SL/TP – whichever user enables | n/a |
   | TREND  | *Pure* opposite-signal exits.  **Any** opposite pulse closes all open contracts & may reverse. | SPL / MA / fixed **must be disabled** so they cannot fight trend exits. |

4. **Intrabar requirements**  
   • All exits (*SPL & trend pulses*) must fire intrabar (`calc_on_every_tick=true`) and **no more than once per bar**.

---

## 2.  Current Implementation (EZAlgoTrader.pine – commit ´production-ready´)

Parameter inputs (≈ lines 450-540):
```
trendPowerThreshold    input.int(50)
trendSizeThreshold     input.int(2)
requireBothConditions  input.bool(true)
smartProfitEnable      input.bool(true)
maExitOn               input.bool(false)
fixedEnable            input.bool(false)
```

Finite-state machine (lines 870-880):
```
condPower = signalPowerPct >= trendPowerThreshold & same-side
condSize  = abs(position_size) >= trendSizeThreshold
promote   = requireBothConditions ? condPower & condSize : condPower | condSize
```

Exit generation:
* **SPL / MA / fixed** are *always* evaluated – even during TREND.  
* `exitLongPulse` / `exitShortPulse` aggregate opposite signals **only from sources whose Usage = "Exit …"**.

Execution path:
```
if tradeMode=='TREND' and exitLongPulse → strategy.exit("Trend", "Long")
```

Observed effects:
1. Promotion to TREND often occurs on 2nd contract (hits Size-Threshold).  
2. Because the code **filters opposite-side pulses by Usage="Exit …"**, if *no* signals are configured that way, the aggregated pulse never forms ⇒ **no exit**.  This filtering is unintended – all opposite signals should qualify.  
3. Meanwhile SPL/MA/fixed still *may* co-exist, causing double-exit or anti-spam flags to lock further exits.  
4. When exit never fires, strategy keeps adding until `pyramidLimit` and then all further entries are blocked (blocked-label flood).

## 2.1  Signal-Power Calculation

```pinescript
// Per-bar counts
activeTradableSignals := 0
for i = 0 to 9
    if array.get(signalEnables, i) and array.get(signalUsages, i) != 'Observe'
        activeTradableSignals += 1
longSignalCount  // # of enabled LONG pulses
shortSignalCount // # of enabled SHORT pulses

signalPowerPct = activeTradableSignals > 0 ?
                 max(longSignalCount, shortSignalCount) / activeTradableSignals * 100 : 0
```
*Stored in variable `signalPowerPct` and used directly in the promotion test (see 2.2).*

## 2.2  Promotion / Demotion Logic

```pinescript
condPower = signalPowerPct >= trendPowerThreshold and
            dominantSide == sign(strategy.position_size)
condSize  = trendSizeThreshold == 0 ? true :
            abs(strategy.position_size) >= trendSizeThreshold
promote   = requireBothConditions ? (condPower and condSize)
                                   : (condPower or condSize)
```
* First bar that returns `promote==true` → `tradeMode` flips from "SCALP" → "TREND".
* `tradeMode` resets to "SCALP" whenever `strategy.position_size` becomes 0.

## 2.3  Exit Engines by Mode (as coded today)

| Mode   | Exit conditions executed | Notes |
| ------ | ------------------------ | ----- |
| SCALP  | SPL (if `smartProfitEnable`)<br>MA trail (if `maExitOn`)<br>Fixed SL/TP (if `fixedEnable`) | `exitLongPulse/exitShortPulse` **ignored** because tradeMode≠"TREND" |
| TREND  | `exit…Pulse` (opposite signal with Usage = "Exit …") **plus** any enabled SPL/MA/Fixed | These extra exits should be **disabled** in Trend but are currently still active |

---

## 3.  Gap Analysis

| # | Requirement | Implementation | Gap / Issue |
| - | ----------- | -------------- | ----------- |
| 1 | TREND exits on *any* opposite signal | **Incorrect:** code filters by Usage='Exit …' so only tagged signals count | Remove filter so *all* enabled opposite pulses trigger exit |
| 2 | Other exits disabled in TREND | SPL/MA/fixed continue to fire | Should be wrapped with `if tradeMode!='TREND'` so **OFF** in Trend |
| 3 | Promotion should respect both thresholds but remain optional | Works, but default Size = 2 promotes too fast | Unintended early TREND |
| 4 | Intrabar single-fire | Anti-spam flags exist; appears OK | Accept |

---

## 4.  Proposed Fix Plan

1. **Simplify exit-pulse rule**  
   Replace Usage-filter with: `pulseLongExit = longSignalCount==0 && shortSignalCount>0` etc. – i.e., *any* opposite pulse from enabled signals.
2. **Disable SPL/MA/fixed while in TREND**  
   Wrap their `strategy.exit()` calls: `if tradeMode!='TREND'`.
3. **Add option** `trendModeEnable` (default *on*) for quick troubleshooting.
4. **Raise default `trendSizeThreshold` to ≥ 3** so casual 1-2-contract scalps stay in SCALP.
5. **Update status panels & blocking labels** to show current exit engine (SPL vs TrendPulse).
6. **Unit test** with 4 signals (Lux/UTBot etc.) toggling exit modes; verify drawdown & intrabar exit counts.

---

## 5.  Next Steps

* [ ] Review this spec with user and adjust thresholds / naming.  
* [ ] Implement code changes in incremental PRs.  
* [ ] Add visual exit-labels when a trend-pulse closes/reverses; preserve user’s classy badge style.

---

ADDED BY USER BECAUSE IT IS A GOOD RESPONSE

Those if statements are only conditioned on the user’s enable switches (smartProfitEnable, maExitOn, fixedEnable).
They are not wrapped in if tradeMode != "TREND" (or similar), so the code executes them every bar regardless of whether the strategy is currently in SCALP or TREND mode.

Consequences:

If SPL/MA/Fixed are enabled in the Inputs panel they will keep evaluating once per bar even while you are in TREND mode.
Any of them can trigger a strategy.exit() and close or reverse the position before an opposite-signal pulse arrives, defeating the “pure signal-following” behaviour you expect in TREND.
Because SPL exits are intrabar (calc_on_every_tick=true) they can fire mid-bar in TREND, again contradicting the desired design where only the opposite-signal pulse should handle exits (and at bar-close).
That’s why the Gap-Analysis table says we need to wrap these exit blocks in a condition that suppresses them when tradeMode == "TREND".
In the current script the Smart Profit Locker (SPL), Moving-Average trail, and Fixed SL/TP exits are coded in their own blocks that look roughly like this:

pinescript
if smartProfitEnable
    strategy.exit("SPL-Long",  "Long",  ...)   // intrabar permitted

if maExitOn
    strategy.exit("MA-Long",   "Long",  ...)   // bar-close

if fixedEnable
    strategy.exit("Fixed-Long","Long",  ...)   // bar-close
Those if statements are only conditioned on the user’s enable switches (smartProfitEnable, maExitOn, fixedEnable).
They are not wrapped in if tradeMode != "TREND" (or similar), so the code executes them every bar regardless of whether the strategy is currently in SCALP or TREND mode.

Consequences:

If SPL/MA/Fixed are enabled in the Inputs panel they will keep evaluating once per bar even while you are in TREND mode.
Any of them can trigger a strategy.exit() and close or reverse the position before an opposite-signal pulse arrives, defeating the “pure signal-following” behaviour you expect in TREND.
Because SPL exits are intrabar (calc_on_every_tick=true) they can fire mid-bar in TREND, again contradicting the desired design where only the opposite-signal pulse should handle exits (and at bar-close).
That’s why the Gap-Analysis table says we need to wrap these exit blocks in a condition that suppresses them when tradeMode == "TREND".

Feedback submitted
Close
9 hours ago
an hour ago
15 minutes ago
8 minutes ago
2 minutes ago
Press Enter again to interrupt and send a new message
