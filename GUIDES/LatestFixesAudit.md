# EZ Algo Trader – **Production-Critical Audit** (Post-Confluence Build)

> Requested via `LatestFixesAudit_Prompt.md` – review conducted with high urgency.  
> Code inspected: latest `EZAlgoTrader.pine` (1 898 lines).  
> External indicators: Wonder2, Volumatic, Adaptive, LinearRegressionCandles.  
> Time-stamp: 2025-??-??  (UTC)

---

## 1 · TL;DR – Can we deploy today?
**No.** Two “million-dollar” failures remain:
1. 🟥 **Intrabar Exits Disabled** – current settings force bar-close execution only.  All scalp profits disappear on small time-frames.  (See §2.)
2. 🟥 **Trend-Exit Signal Path Broken** – Advanced Trend Exit never fires because LRC signals are either not wired or still gated by unreachable logic.  (See §3.)

Additional blockers:
* 🟧 Hybrid logic still duplicates stand-alone system → perpetual trend mode.
* 🟧 Trade-count mismatch caused by filters and entryAllowed logic.
* 🟨 Performance / debug overhead on every bar.

### Deployment Risk Rating: **4 / 5 (High)**
Do **NOT** trade live until fixes applied.

---

## 2 · Intrabar Exit Integrity Audit
| Checkpoint | Observation | Status |
|------------|-------------|--------|
| `strategy()` flags | `process_orders_on_close = true`, `calc_on_every_tick = false` | 🔴 Forces **all** exits to wait for the *next* bar-close. |
| Comment in code | Lines 281-285 explicitly state intrabar exits were removed. | Confirms regression. |
| Exit call pattern | Each exit is wrapped **once-per-bar** under normal logic *and* further guarded by `strategy.position_size != 0` – so spam is avoided, but timing is still bar-close. | 🔴 |
| Anti-spam flags | `maExitSent`, `trailExitSent`, etc. reset on flat; fine. | — |

**Regression vs Reference:**  DeprecatedRiskManager implementation had `process_orders_on_close=false` and called exits every bar.  Current build regressed.

**Fix:**
1. Change strategy declaration: `process_orders_on_close = false`, `calc_on_every_tick = true` *or* retain `calc_on_every_tick=false` but rely on `bar_index` multi-bar update (less precise).  
2. Remove comment block that forbids intrabar exits.  
3. Ensure anti-spam flags reset on every **tick** not bar when intrabar enabled.

---

## 3 · Trend Indicator Signal Integration (LRC, Wonder2, Volumatic, Adaptive)
### 3.1 Linear Regression Candles (LRC) – Deep Dive
**Indicator internals (`TrendExits/LinearRegressionCandles.pine`):**
| Plot Title | Logic | Meaning |
|------------|-------|---------|
| `Bull Flip Signal` | Candle flips from red→green (`bull_flip`) | **Primary long-exit trigger** when in short; re-entry cue when flat |
| `Bear Flip Signal` | Candle flips from green→red (`bear_flip`) | **Primary short-exit trigger** when in long |
| `Bull MA Signal` | LinReg close crosses **above** signal MA | Secondary long-side trigger |
| `Bear MA Signal` | LinReg close crosses **below** signal MA | Secondary short-side trigger |

**Integration points in EZAlgoTrader:**
```pine
lrcBullColorChange  // user should connect to "Bull Flip Signal"
lrcBearColorChange  // "Bear Flip Signal"
lrcBullMAXross      // "Bull MA Signal"
lrcBearMACross      // "Bear MA Signal"
```

`lrcBullColor`/`lrcBearColor` booleans are derived correctly – they verify the source isn’t default `close` before treating >0 as true.  **No code changes needed in the indicator.**

**Why exits still may not fire:**
1. `Advanced Trend Exit` waits for **ProfitMode** (price ≥ entry ± ATR×mult). This is intentional so flips near entry don’t whipsaw.  Ensure your ATR multiplier (`initialTrailingStop`) is realistic for chosen timeframe.
2. If `hybridModeEnable` is off but global Hybrid/TCE flags interfere, exit block may be skipped → see §4.
3. Confirm the four LRC plots are *actually linked* in indicator-picker; otherwise booleans stay false.

### 3.2 Wonder2 – OK (see earlier audit).
### 3.3 Volumatic & Adaptive – **Still missing numeric plots → strategy sees `close`.**

---

## 4 · Hybrid Exit Logic Review
* Two independent systems: stand-alone Hybrid (Lines 565-583) **and** new Confluence layer (`hybridRequireConfluence`) patch.
* Condition to enter Trend-Mode: `(contracts >= threshold)` **AND** (optional confluence).  With 10 signals most bars start with ≥2 contracts ⇒ Trend-mode almost always true.
* **Recommendation:**
  1. Delete stand-alone Hybrid block; keep only Advanced Trend Exit’s hybridMode.  
  2. Raise `hybridSwitchThreshold` or base on *uniq* signals not contracts.

---

## 5 · Trade-Count Discrepancy Investigation
| Stage | Potential Drop | Evidence |
|-------|---------------|----------|
| Individual signals | OK – Wonder2 etc. fire (long). |
| Primary aggregation | OK. |
| Filter layer | `bbLongFilterOK` & `longDirectionalBias` may reject many shorts (RBW bias often neutral). |
| entryAllowed / pyramid | With pyramidLimit default 1, any rapid re-entry blocked. |
| **Virtual accounts** | **Ignore** filters → record trades that main strategy rejects – explains higher counts. |

**Fix:** Let virtual accounts honour same bias & BB filters, or compute filtered counts for fairness.

---

## 6 · Code Quality & Performance
* Debug labels created every bar when enabled → heavy.  
* Large tables recomputed at `barstate.islast` – OK.
* Variable sprawl still high; consider modules.

---

## 7 · Action Plan (Must-Fix before Live)
1. 🟥 **Re-enable intrabar exits** – set `process_orders_on_close=false`, `calc_on_every_tick=true`; retest on 1-min.  
2. 🟥 **Add numeric plots** to Volumatic & Adaptive; wire to Trend2/3 inputs.  
3. 🟥 **Remove stand-alone Hybrid Exit & TrendChangeExit**; rely on Advanced Trend Exit with `hybridModeEnable`.  
4. 🟧 **Reduce Trend-mode trigger** – switch based on *unique signals* or add min-ATR guard.  
5. 🟧 **Sync virtual account filters** with main strategy to align trade counts.  
6. 🟨 **Throttle debug system** with `if debugEnabled and barstate.isconfirmed`.  
7. 🟨 **Add optional LRC Debug Table** – checkbox in Debug group that shows:
   * Timestamp / bar-index of last `Bull Flip`, `Bear Flip`, `Bull MA`, `Bear MA` event.
   * Current LRC candle state (🟢/🔴).
   * Last trend flip time for each connected trend indicator.  Helps verify data flow.

---

## 8 · Production Readiness Scorecard
| Area | Weight | Score (0-5) |
|------|--------|-------------|
| Exit Timing (intrabar) | 30% | 1 |
| Trend Exit Reliability | 25% | 2 |
| Signal Integration | 20% | 2 |
| Hybrid Logic Clarity | 15% | 2 |
| Performance & Debug | 10% | 3 |
| **Weighted Total** | **—** | **1.9 / 5 → NOT READY** |

Deploy only after red items fixed and regressions tested on live tick replay. 