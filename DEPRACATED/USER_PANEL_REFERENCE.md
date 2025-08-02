# EZAlgoTrader User Panel Reference

This document is a **living specification** for the EZAlgoTrader settings panel.  It lists every visible group, each setting inside, the expected UI element, and the effect it has inside the code-base.  Use this as the single source of truth when auditing or refactoring the panel.

> 📌  **How to read:**  Each section corresponds to a `group` argument in the `input.*()` calls.  For every entry you will see the variable name (`id` inside the script), the label you see in TradingView, the control type, default value, tooltip (if any), and the logic location where it is consumed.  _If you spot a mismatch, typo, or missing control, leave an inline comment and we will update both the code and this file._

---

## 🚀 Desired Final Panel Order (Target Layout)
1. **General Trading Parameters**
   • Position Size & Pyramid Limit  
   • Futures Multiplier (auto-detect tick size/value in future)
2. **Entry Signals**
   • Multi-Signal matrix (names / usage / *Required-for-Trend* flag)
3. **Primary Exit Systems**
   • Smart Profit Locker (SPL)  
   • Fixed SL / TP  
   • Moving-Average Exit
4. **Hybrid / Trend Mode Controls**  
   – Auto-Hybrid FSM & thresholds
5. **Filters**
   • Bollinger-Band Filter  
   • RBW Filter  
   • Additional future filters
6. **Emergency Exits** *(placeholder)*  
   – e.g., time-based close, global drawdown cap
7. **Visual Settings**
   • Status Table position (incl. Middle-Left/Right)  
   • Mode Colors & Background intensity  
   • Compact mode, debug labels
8. **Advanced / Debug**
   • Debug level  
   • Phantom auto-close  
   • Master-panel toggles
9. **Back-Testing & Virtual Accounts**
   • Show/hide back-test table  
   • Virtual-account parameters

> The list above reflects the *desired* location for each section after refactor. Actual code still uses the legacy order documented below.

## 🔧 Debug Settings
| Var | Label | Type | Default | Logic Reference | Notes |
|-----|-------|------|---------|-----------------|-------|
| `debugLevel` | Debug Level | dropdown (`input.string`) | "Basic" | Rendering ‑ drives `debugEnabled`, `showDebugLabels`, etc. | Off = no labels, Full = maximum detail. |
| `phantomAutoCloseEnable` | 🛡️ Auto-Close Phantom Positions | checkbox (`input.bool`) | `true` | Phantom detection FSM around lines ~328 | Closes orphan positions automatically. |

## 🎨 Visual Settings
| Var | Label | Type | Default | Logic Reference | Notes |
| `tablePosition` | Status Table Position | dropdown | "Top Right" | Status table renderer | Affects placement of summary table. |
| `compactMode` | Compact Mode (Mobile) | checkbox | `false` | Render gates around tables & labels | Reduces footprint on small screens. |

## Strategy Mode Colors
| Var | Label | Type | Default | Notes |
|-----|-------|------|---------|------|
| `trendModeColor` | Trend Mode Color | color picker | `#089981` | Background & badge fill when in Trend mode |
| `scalpModeColor` | Scalp Mode Color | color picker | `#f23645` | Background & badge fill when in Scalp mode |
| `hybridModeColor` | Hybrid Mode Color | color picker | `#2157f3` | Background & badge fill when in Hybrid mode |
| `flatModeColor`  | Flat/No Position Color | color picker | `#808080` | Background when flat |
| `modeBackgroundIntensity` | Background Transparency | slider (`input.int`, 50-95) | 60 | Sets alpha channel for mode highlights |
| `atrLength`, `rangeMultiplier` | ATR Length / Range Multiplier | int / float | 20 / 1.5 | Used by background highlight envelope |

## 🎯 Smart Profit Locker (SPL)
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `smartProfitEnable` | 🎯 Enable Smart Profit Locker | checkbox | `true` |
| `smartProfitType` | Distance Type | dropdown (ATR / Points / Percent) | ATR |
| `smartProfitVal`  | Distance Value | float | 2.0 |
| `smartProfitOffset` | Trail Offset | float | 0.1 |

Logic lives around **lines ~2470-2490** where `strategy.exit('Smart-Long'/'Smart-Short')` is issued when `tradeMode != "TREND"`.

## 🛡️ Fixed SL/TP
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `fixedEnable` | Enable Fixed SL/TP | checkbox | `false` |
| `fixedStop`  | Stop Loss (ATR) | float | 1.5 |
| `tp1Enable`  | Enable Take Profit | checkbox | `false` |
| `tp1Size`    | Take Profit (ATR) | float | 2.0 |
| `fixedUnit`  | Unit (ATR / Points) | dropdown | ATR |

Consumed in **lines ~2490-2505** within guarded `strategy.exit("Fixed-Long/Short")`.

## 📊 MA Exit
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `maExitOn` | Enable MA Exit | checkbox | `false` |
| `maLen`    | MA Length | int | 21 |
| `maType`   | MA Type | dropdown (SMA/EMA/WMA/VWMA/SMMA) | EMA |

Logic in **lines ~398-404** (MA calc) and **MA exit block ~2400-2430**.

## 📊 BB Exit
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `bbExitTightness` | BB Exit Tightness | float | 0.5 |
| `bbLength` | BB Length | int | 20 |
| `bbMultiplier` | BB Mult | float | 3.0 |
| `bbEntryFilterEnable` | 🚫 BB Entry Filter | checkbox | false |
| `bbExitEnable` | ⚡ BB Exit Logic | checkbox | false |

Used inside SPL tightening logic and entry filters.

## 💼 Position Size
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `positionQty` | Contracts per Signal | int | 1 |
| `pyramidLimit` | Max Total Contracts | int | 5 |

Controls pyramiding checks (`entryAllowed`) and position sizing.

## 🔍 Backtesting Panels
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `showBacktestTable` | 📊 Individual Signal Backtest | checkbox | true |
| `futuresMultiplier` | 💰 Futures Multiplier | int | 20 |

Affects virtual-account P&L conversions.

## 🎯 Hybrid Trend Mode
| Var | Label | Type | Default |
|-----|-------|------|---------|
| `trendExitEnable` | Enable Trend Exit Logic | checkbox | true |
| `autoHybridMode` | Enable Auto Hybrid FSM | checkbox | true |
| `trendPowerThreshold` | Signal-Power % | int | 60 |
| `trendSizeThreshold` | Contracts Threshold | int | 0 (off) |
| `requireBothConditions` | Require BOTH Power & Size? | checkbox | true |
| `hybridSwitchThreshold` | Auto-Hybrid: Switch at Contracts | int | 2 |

These variables feed the mode-switching FSM (see **HYBRID SYSTEM** block).

## 🔄 Hybrid System (deprecated naming)
Keys reused above; will be merged in next refactor.

## 📊 RBW Filter
Settings from lines **415-426** controlling an optional bandwidth filter.

## 💼 Virtual Account Settings
| Var | Label | Type | Default |
| `commissionPerTrade` | 💰 Commission Per Trade | float | 2.50 |
| `slippagePerTrade` | 📉 Slippage Per Trade | float | 0.50 |
| `virtualPositionSize` | 📊 Virtual Position Size | int | 1 |

Drive the per-signal virtual-account back-tester.

---

## Known UI/UX Issues (2025-07-31 review)
1. **Group Ordering** – Critical inputs like *Position Size* and *Futures Multiplier* appear mid-panel; they must be moved to the very top.  Visual/debug options belong at the bottom.
2. **Status Table Position Choices** – Needs **Middle-Left / Middle-Right** in addition to Top-Left/Right and Bottom-Left/Right.  The *tablePosition* input will be expanded.
3. **Mixed Emoji Usage** – Some groups have emojis (📊, 🎯) while others don’t.  Choose a single style (user leans toward minimal/no emojis except badge icons).
4. **Duplicate / Obsolete Groups** – *Hybrid System* group is deprecated; variables should be removed after confirming no residual code references.
5. **Strategy Mode Color Background** – Color-fill background is rendered too high above candles; investigate envelope offset calculation and `plot` z-order.
6. **Hidden Dependencies** – e.g. `bbExitEnable` silently tightens SPL; UI must hint at this.  Similar audit needed for RBW filter and other internals.
7. **Filter Blocks Out-of-Date** – BB and RBW filters may have drifted from original logic; perform code audit and update panel wording/tooltips.
8. **Emergency Exit Section** – Placeholder to be added after Filters (future feature).


### Implementation Roadmap (v2.0 target)
Phase 1 – Input Extraction & Tagging  
• Copy every `input.*()` to a single block at top (feature-flag branch).  
• Comment each with its original group + `// TODO newOrder:X`.

Phase 2 – Re-order & Rename  
• Re-arrange to match *Desired Final Panel Order*.  
• Insert placeholder **Emergency Exits** group (behind `showEmergencyInputs` flag).  
• Add per-signal **Required-for-Trend** checkbox to Multi-Signal matrix.

Phase 3 – Enhanced Controls  
• Extend `tablePosition` dropdown with *Middle-Left* / *Middle-Right*.  
• Add auto-detected `futuresMultiplier` (tickSize * tickValue) fallback.  
• Start RBW / BB filter code audit and sync tooltips.

Phase 4 – Tooltip & Emoji Audit  
• Ensure all 188 inputs have concise tooltips.  
• Decide on minimal emoji style (section-header only).  
• Update labels accordingly.

Phase 5 – Legacy Code Cleanup  
• Remove **Hybrid System** variables & dead trend-exit flags.  
• Compile, run lint check (no unused var warnings).

Phase 6 – QA & Performance  
• Visual diff vs. pre-refactor panel.  
• Back-test BTCUSDT 1 h, compare equity curve (±0.1%).  
• Memory profile: keep objects <1200.  
• Confirm debug objects gated by `debugLevel`.

Deliverables:  
• Panel Version label `v2.0` with change-log.  
• Updated docs & screenshots.

> Timeline ~2.5 dev days assuming smooth compile passes.

---

### Next Steps
* Review this reference and annotate any inaccuracies.
* Decide on final section order & naming conventions.
* Implement a **panel-refactor branch** after approval.
