# Dynamic Signal Label Migration Plan

This document outlines **exactly** how we will port the dynamic signal-naming / label-display system from the reference script into `EzAlgoTraderRESURRECTED.pine` **without touching any protected logic**.  No code will be changed until you approve this plan.

---

## 0. Preconditions & Safety Guardrails
1. **Pine version stays `@version=6`** – mandatory.
2. Preserve all **Absolute Preservation Rules** & variables specified in memories.
3. Do **NOT** alter LRC exits, icon usage, or intrabar exit behaviour.
4. Keep the existing label-pool mechanism intact (MAX_LABELS, `getPooledLabel`, `updateLabel`).

---

## 1. Identify Source Components
| # | Section in Source | Purpose |
|---|-------------------|---------|
| A | **Signal name inputs** (`signal1Name` … `signal10Name`) | User-editable labels |
| B | **Dynamic name construction** (long/short) | Build `longSignalName` & `shortSignalName` |
| C | **Arrow plotting** (`plotshape`) | BUY/SELL arrows |
| D | **Dynamic entry labels** | Create text labels with `updateLabel` |
| E | **Label-pool helpers** | Memory-safe label reuse |

---

## 2. Map Insertion Points in `EzAlgoTraderRESURRECTED.pine`

| Source Section | Target Location (by category) |
|----------------|--------------------------------|
| A – Inputs     | Consolidated Input Panel (≈ lines 150-350 in RESURRECTED) |
| E – Helpers    | Existing *OPTIMIZED LABEL POOL SYSTEM* block (reuse; only verify functions present) |
| B – Name Construction | **Immediately before** entry/arrow plotting logic (~ where `longEntrySignal`/`shortEntrySignal` defined) |
| C – Arrow plotting | If arrows already plotted, keep titles but ensure they reference `longEntrySignal`/`shortEntrySignal`. Otherwise insert just after name construction. |
| D – Dynamic labels | Directly after arrow plotting. Must call `getSmartLabelY` & `updateLabel` just like source. |

---

## 3. Step-by-Step Implementation Sequence

1. **Add/Verify Signal Name Inputs**
   - Insert the 10 `signalXName` input lines in the existing *Entry Signals* group.
   - Ensure no duplicate variable names.

2. **Ensure Label-Pool Helpers Exist**
   - Confirm `MAX_LABELS`, `getPooledLabel()`, and `updateLabel()` are already present.
   - If absent, port the helper block **unchanged**.

3. **Dynamic Name Construction Block**
   - Declare `var string longSignalName = ""`, `var string shortSignalName = ""` plus `dynamicLongCount/ShortCount` counters.
   - Inside each bar, when `longEntrySignal` or `shortEntrySignal` is `true`, loop through `sigXLong`, `sigXShort` flags (1-10) **AND** their `signalXEnable` toggles to build names exactly as in the source.
   - Produce `dynamicLongText` / `dynamicShortText` (fallback "MULTI").

4. **Arrow Plotting** (if missing)
   - `plotshape(longEntrySignal, … title="BUY Signal" …)`
   - `plotshape(shortEntrySignal, … title="SELL Signal" …)`

5. **Dynamic Label Rendering**
   - Calculate Y-positions with `getSmartLabelY()` and half ATR offset.
   - Retrieve label via `getPooledLabel()` and call `updateLabel()` with style up/down, semi-transparent lime/red background, white text.
   - Guard with `shouldRenderCritical` flag to respect user’s chart-cleanliness prefs.

6. **Compilation Check (NO RUN)**
   - Temporarily comment new code blocks with `//TODO ENABLE AFTER TEST` to ensure zero functional change until approval.

7. **Testing Plan (post-approval)**
   1. Enable code blocks.
   2. Compile in TradingView.
   3. Trigger sample signals; verify arrows + text (e.g. “LuxAlgo+UTBot”).
   4. Ensure no effect on strategy statistics.

8. **Rollback Instructions**
   - All migrated code will be enclosed by `// === DYNAMIC LABEL START` & `// === DYNAMIC LABEL END` markers; delete or disable to revert.

---

## 4. Open Questions / Confirmations Needed
1. Should arrow colours/styles remain default lime/red triangles?
2. Keep fallback text "MULTI" or prefer another term?
3. OK to hide arrows via setting while keeping labels (requires minor tweak)?


## 5. Technical Considerations & Potential Pitfalls

### 5.1 Variable name / scope collisions
* `signalXName` lines MUST exactly match any existing `signalXName` vars in the resurrected script – otherwise TradingView will throw *“identifier redeclared”* errors.
* Avoid short names like `longSignalName` if they already exist; do a quick grep first.

### 5.2 Performance & memory
* Labels are created intrabar – keep **MAX_LABELS** conservative (250) to prevent reaching TV’s hard 50 k label cap in lengthy backtests.
* Use `shouldRenderCritical` / `debugLevel` to skip label rendering during high-speed optimiser runs.
* Re-use the existing ATR reading (`atrVal`) to avoid extra `ta.atr()` calls every bar.

### 5.3 Execution order pitfalls
* The name-construction block must run **after** all `sigXLong/Short` booleans are finalized **and before** arrow / label plotting.
* Plot calls must appear **outside** of any `strategy.*` function scopes; otherwise TV raises *“plots cannot be inside function”*.

### 5.4 BackTester.pine interactions
* BackTester typically runs both the strategy script **and** a companion indicator. Dynamic labels can repaint trade-stat lines; wrap them in `if not backtestMode` guard if necessary.
* Ensure no global variables in the label-pool helpers duplicate those inside `BackTester.pine` (e.g., `MAX_LABELS`). Consider prefixing with `resur_` if clashes arise.
* Because BackTester measures execution time, set `shouldRenderCritical := debugLevel == "Detailed"` so that batch optimisations skip heavy label work.

### 5.5 Chart cleanliness / UX
* ATR-offset calculations (`atrVal*0.5`) may place text off-screen on very illiquid symbols – offer a user-input multiplier.
* Provide optional hide-arrows checkbox to satisfy users who only want labels.
* Keep background colour opacity ≥20 to respect dark/light chart themes.

### 5.6 Intrabar exit integrity
* Dynamic label code must NOT modify or reference exit flags (`trailExitSent`, `maExitSent`, etc.) – only read-only operations are allowed.
* Verify that label rendering does not occur in `calc_on_order_fills=true` scope, else it could unintentionally trigger extra intrabar evaluations.

### 5.7 Debugging & Testing checklist
1. Run compile with *Pine > Debug > More details* to spot any undeclared-identifier errors.
2. Backtest **1,000 bars** in Fast Replay; monitor label count (`array.size(labelPool)`).
3. Switch to scalping config with **intrabar exits**; verify no performance regressions.

---

*Please review.  No code will change until you approve this plan.*
