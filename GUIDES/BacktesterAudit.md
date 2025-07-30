# EZ Algo Trader – Back-Testing Subsystem Technical Audit

> Artefacts inspected:  
> • `EZAlgoTrader.pine` (1826 lines, current build)  
> • `EZALGO_TRADER_DOCS.md` (design intent)  
>
> Focus area: **Virtual Account / Individual-Signal Back-Testing framework** – how it collects long/short stats, populates the on-chart table, and tracks per-signal performance.

---

## Why this document?
The strategy boasts “perfect attribution” – ten separate *virtual accounts* that measure each signal’s long/short performance.  Users report that **longs and shorts are not separated correctly** in the table.  This audit explains the intended design, reviews the code, and pin-points logic gaps.

## TL;DR Verdict
| Section | Status | Notes |
|---------|--------|-------|
| VirtualAccount type | ✅ OK – fields for long/short counts & PnL exist | but several never updated correctly |
| Entry processing (`processVirtualTrade`) | 🟧 *Partial* | long/short *counts* recorded, but **`current_trade_signals` flag never set**, stats arrays later empty |
| Exit processing (`processVirtualExit`) | 🟧 *Works* but **double-substracts** commission&slippage, inflating losses |
| Session statistics arrays (`signal_strategy_*`) | 🟥 Broken | rely on `current_trade_signals` flags that are never turned on |
| Back-test table build (lines 982-1057) | ✅ Layout fine | shows long/short columns but reflects bad data from above |
| Long/Short separation bug | 🟥 Root cause | longPnL/shortPnL OK, **but rows for disabled signals may shift** and arrays for main strategy (`signal_*`) never increment |
| Production readiness | 🔶 **Not ready** – will mis-report performance; commission double-count; no reset of flags |

---

## 1. Intended Design (per docs)
1. **VirtualAccount struct** stores full metric set including:
   * `longTrades`, `shortTrades`, `longWins`, `shortWins`  
   * `longPnL`, `shortPnL`, `totalPnL`, `maxDrawdown`, etc.
2. On every **entry** the appropriate virtual account:
   * flips `inPosition` & `direction`
   * increments `longTrades` or `shortTrades`  
   * debits commission/slippage.
3. On **exit** it:
   * calculates raw PnL, allocates to `longPnL` or `shortPnL`  
   * updates wins & loss buckets.
4. A live **table** (11×12) shows long/short counts, win-rate & PnL for each enabled signal.

## 2. Implementation Walk-Through
### 2.1 VirtualAccount type (lines 96-120)
```pine
float longPnL  // long aggregate
float shortPnL // short aggregate
int   longTrades, shortTrades, longWins, shortWins
```
Fields are present – ✔️.

### 2.2 Entry handler `processVirtualTrade` (231-209)
```pine
if not account.inPosition
    if longSignal
        account.inPosition := true
        account.direction  := "long"
        account.longTrades += 1
        account.commission += commissionPerTrade
        account.slippage   += slippagePerTrade
```
Issues:
1. **`current_trade_signals` flag not set**  
   Intended to mark which signal opened the *real* strategy trade, later used in global arrays – missing.
2. Commission & slippage recorded here – fine.

### 2.3 Exit handler `processVirtualExit` (231-270)
```pine
if account.direction == "long"
    rawPnL := (close - entryPrice) * size * pointValue
else
    rawPnL := (entryPrice - close) * size * pointValue
finalPnL := rawPnL - commissionPerTrade - slippagePerTrade - account.commission - account.slippage
```
🔴 **Double charge**: it subtracts a *second* commissionPerTrade+slippagePerTrade **plus** the amounts already stored in account.commission/slippage (which represent the first deduction). Result = trades always look worse than reality.

### 2.4 Stats arrays (`signal_tradeCounts`, etc.) update block (1214-1244)
Relies on `current_trade_signals[i] == true`.  But flag never set at entry, so **no per-signal performance updates**.  Table shows zeros for win% & PnL if you disable the VirtualAccount columns.

### 2.5 Table creation (982-1057)
Uses the following *per-account* fields – long/short separation is actually correct **inside each VirtualAccount**:
```pine
longTrades, longWins, longPnL
shortTrades, shortWins, shortPnL
```
…but since the double-commission bug skews PnL and stats arrays are empty, the user experiences “not separated”.

---

## 3. Logic Gaps & Bugs
| 🟥 Critical | Description | Quick Fix |
|-------------|-------------|-----------|
| Double commission/slippage | Final PnL subtracts fees **twice** | Remove `- commissionPerTrade - slippagePerTrade` from `finalPnL` calc. Fees already in `account.commission/slippage`. |
| No `current_trade_signals` flag | Global stats arrays never updated; later analytics blank | In `processVirtualTrade` set `array.set(current_trade_signals, signalIndex, true)` and record `current_trade_entry := close` for first contributing signal. |
| Missing `current_trade_entry` update | Draw-down & PnL per trade use `na` | On real *strategy* entry set `current_trade_entry := close`, `current_trade_peak := close`, `current_trade_trough := close`. |

| 🟧 Medium | Description | Fix |
|-----------|-------------|-----|
| Long/Short win-rate skew | Wins counted **only** if finalPnL>0; break-even trades count as loss | Add separate `else if finalPnL == 0` no-action or treat as tie. |
| Flags never reset | `trailExitSent`, etc. stay true between positions | Reset when account flat. |
| Table row mis-alignment | If some signals disabled rows shift, summary row overwrites data | Build table with *fixed* 10-row index rather than `currentRow` skip. |

| 🟨 Minor | Description | Fix |
|----------|-------------|----|
| ProfitFactor 999 magic | Hard-codes 999 when no losses | Use `nan` or display “∞” for clarity. |
| Unused UI inputs | e.g. `virtualPositionSize` never shown in table | Add to system row. |

---

## 4. Production-Readiness Assessment
With the above critical issues the back-testing numbers are **not reliable**: PnL is understated, per-signal analytics arrays never fill, and long/short column counts look wrong.  Treat current build as *beta / diagnostic only* until repairs are applied.

---

## 5. Next Steps / Quick-Fix List
1. **Patch commission double-subtraction** in `processVirtualExit`.
2. **Set `current_trade_signals[i]` & `current_trade_entry`** inside `processVirtualTrade` (first signal only).
3. **Reset debug flags & per-account commission/slippage** at flat state (some already done; add resets for entry flags).
4. Add guard so `current_trade_peak/trough` initialise on entry.
5. Optional: normalise table row building to prevent mis-alignment.
6. Re-run back-test and verify longs/shorts populate correctly.

Once those are addressed, the virtual back-testing subsystem should deliver accurate, separated long/short stats per signal.  Only then is it ready for production optimisation decisions.