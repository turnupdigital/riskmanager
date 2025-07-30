# EZ Algo Trader - Code Explanation

This document provides a detailed, line-by-line and section-by-section explanation of the `EZAlgoTrader.pine` script.

### Overview

`EZAlgoTrader.pine` is an advanced, multi-signal, multi-exit risk management system designed for automated trading via webhooks (specifically for TradersPost). Its core strength lies in its ability to combine up to 10 different external indicator signals, apply various directional filters and risk management rules, and manage trade exits through a sophisticated hierarchy of systems.

A standout feature is the **Virtual Signal Architecture**, which backtests each of the 10 signals independently, as if each had its own trading account. This allows for perfect attribution of performance, helping you identify which signals are truly profitable.

---

### 1. Header and Strategy Declaration (Lines 9-22)

```pinescript
//@version=6
// ...
strategy(title = 'EZ Algo Trader (Beta)', overlay = true, default_qty_type = strategy.fixed, default_qty_value = 1, calc_on_order_fills = true, process_orders_on_close = true, calc_on_every_tick = false)
```

*   `//@version=6`: This script is written for Pine Script version 6. This is a significant update from the more common version 5, and it introduces new syntax and features. The script has been migrated to this version.
*   `strategy(...)`: This function configures the script to be a strategy, not just an indicator.
    *   `overlay = true`: The strategy's plots and trades will be shown directly on the main price chart.
    *   `calc_on_order_fills = true`: The strategy performs a recalculation every time an order is filled. This is crucial for accurately managing exits that depend on the entry price.
    *   `process_orders_on_close = true`: The strategy logic is executed on the close of each bar, and orders are processed then. This prevents the strategy from executing multiple times within a single bar.
    *   `calc_on_every_tick = false`: The strategy only calculates at the close of each bar, not on every real-time price tick. This is the standard and most reliable way to backtest, avoiding repaint issues.

---

### 2. Debug System (Lines 11-21)

```pinescript
bool debugEnabled = input.bool(false, '🔍 Enable Debug Labels', group = '🛠️ Debug System', ...)

debugMessage(string type, string message, color bgColor, color txtColor, float yOffset) =>
    if debugEnabled
        label.new(...)
```

This is a simple but effective custom debugging system. When `Enable Debug Labels` is checked in the settings, the `debugMessage` function will plot text labels on the chart. Throughout the script, this function is called to provide real-time information about what the strategy is doing (e.g., "Virtual Long Entry," "Parameters changed," "BB FILTER BLOCKED LONG"). This is incredibly helpful for understanding the script's behavior without having to read through the code during live operation.

---

### 3. Global Variable Declarations (Lines 25-91)

This section declares a large number of variables using the `var` keyword. In Pine Script, `var` ensures that a variable's value is preserved across all historical and real-time bars, instead of being re-initialized on every bar. This is essential for tracking state, like whether a trade is active or what the entry price was.

You'll notice many variables here for different systems:
*   **Exit Systems:** `smartOffset`, `exitReason`, etc.
*   **Backtesting:** `signal_tradeCounts`, `signal_profits`, etc. These are arrays that hold performance metrics for each of the 10 signals.
*   **Advanced Trend Exits:** `inTrendExitMode`, `waitingForReEntry`, etc., which manage the state of the Linear Regression Candle exit system.
*   **Hybrid Exit:** `hybridExitActive`, `hybridUsingSPL`, etc., which manage the state of the hybrid exit system.

The comment `// Missing variable declarations - restored` (line 40) suggests that at some point, crucial variables were accidentally removed and had to be added back. This is a good example of why having a version control system like Git is important.

---

### 4. Virtual Signal Architecture (Lines 92-271)

This is one of the most powerful and unique features of your script. It creates a complete, parallel backtesting environment *inside* the main strategy.

```pinescript
// Virtual Account Structure for Each Signal
type VirtualAccount
    bool inPosition = false
    string direction = "none"
    float entryPrice = na
    // ... many more performance metrics

// Initialize 10 Virtual Accounts
var VirtualAccount virtualAccount1 = VirtualAccount.new()
// ... up to virtualAccount10

// Array of virtual accounts for easy iteration
var array<VirtualAccount> virtualAccounts = array.from(...)
```

*   **`type VirtualAccount`**: This defines a custom data structure (a `type`) that acts as a self-contained trading account. It tracks everything needed to evaluate a signal's performance: P&L, trade counts, win rates, max drawdown, and more, broken down by long and short trades.
*   **Initialization**: Ten of these `VirtualAccount` objects are created, one for each of the 10 potential input signals. They are stored in an array called `virtualAccounts` for easy access in loops.

#### Virtual Trading Functions

```pinescript
processVirtualTrade(VirtualAccount account, bool longSignal, bool shortSignal, ...) =>
    // ... entry logic

processVirtualExit(VirtualAccount account, bool exitSignal, ...) =>
    // ... exit logic
```

*   **`processVirtualTrade`**: This function handles the entry logic for a single virtual account. When a signal is triggered (e.g., `sig1Long`), this function updates the corresponding virtual account to an "inPosition" state, records the entry price, and deducts commission and slippage.
*   **`processVirtualExit`**: This function handles the exit logic. When an opposite signal occurs, it closes the virtual trade, calculates the final P&L for that trade, and updates all the performance statistics (win rate, gross profit/loss, etc.) for that specific account.

**Key Insight:** This system runs completely independently of the main `strategy.*` functions. This means you can see how Signal 1 would have performed *on its own*, with its own P&L, even while the main strategy is trading a combination of signals 1, 3, and 5. This provides **perfect attribution** and is the foundation for the "Individual Signal Backtest" panel.

#### Parameter Change Detection

```pinescript
calculateParameterHash() =>
    // ... creates a unique number based on key settings

resetVirtualAccounts() =>
    // ... resets all virtual accounts to their initial state
```

This is a clever system to ensure backtest integrity. It creates a "hash" (a unique number) based on important settings like commission, slippage, and which signals are enabled. If you change any of these settings, the hash changes. The script detects this change and calls `resetVirtualAccounts()` to wipe all the virtual backtest stats and start fresh. This prevents you from accidentally looking at a backtest result that was calculated with old settings.

---
Continuing with the next sections.
Here is the next part of the explanation for `GUIDES/EZAlgoTrader_Explanation.md`, covering the input systems and how signals are processed.

```markdown
### 5. Multi-Signal Input System (Lines 272-427)

This large section defines the user-configurable inputs for all 10 signals and the supporting trend indicators.

```pinescript
// ─────────────────── SIGNAL SOURCE INPUTS ───────────────────
signal1Enable = input.bool(true, '📊 Signal 1', ...)
signal1LongSrc = input.source(close, 'Long', ...)
signal1ShortSrc = input.source(close, 'Short', ...)
signal1Name = input.string('LuxAlgo', 'Name', ...)
signal1Usage = input.string('Entry All', 'Usage', ...)
signal1OnlyMode = input.bool(false, 'Only', ...)
```

For each of the 10 signals, the user can configure:
*   **`Enable`**: A checkbox to turn the signal on or off completely.
*   **`Long/Short Source`**: This is where you connect the signal from another indicator on your chart. You use the `plot` output of another indicator as the input source here.
*   **`Name`**: A custom name for the signal, which is used in the backtest panels and chart labels.
*   **`Usage`**: This dropdown is intended to define the role of the signal (e.g., 'Entry All', 'Exit Only'). **However, in the current version of the code, this `Usage` input is not actually used in the strategy logic.** The system currently treats all enabled signals as potential entries. This is a key area of divergence between the UI and the backend logic.
*   **`Only Mode`**: A very useful feature for testing. If you check this box for a signal, the script will ignore all other signals, allowing you to isolate and test the performance of just that one signal in the main strategy.

#### Signal Processing

```pinescript
// Process all signals with "Only Mode" logic (FIXED - properly ignore 'close' as signal)
sig1Long = signal1Enable and signal1LongSrc != close and (signal1LongSrc > 0 or bool(signal1LongSrc) == true)
sig1Short = signal1Enable and signal1ShortSrc != close and (signal1ShortSrc > 0 or bool(signal1ShortSrc) == true)
// ... repeated for all 10 signals
```

This is where the raw input sources are converted into `true`/`false` boolean signals.
*   `signal1Enable`: The signal is only processed if it's enabled.
*   `signal1LongSrc != close`: This is a **critical check**. By default, `input.source` is set to the `close` price. This condition ensures that the script only treats an input as a real signal if the user has actually connected it to an indicator. If it's still the default `close`, it's ignored. This prevents the strategy from generating signals on every bar.
*   `(signal1LongSrc > 0 or bool(signal1LongSrc) == true)`: This handles two common types of signals from other indicators: numeric signals (where a value greater than 0 is a signal) and boolean signals (where `true` is a signal).

After processing, the signals are put into arrays (`allLongSignals`, `allShortSignals`) for easier processing in other parts of the script.

---

### 6. Risk Management and Exit Strategy Inputs (Lines 474-785)

This extensive section defines the inputs for all the different exit strategies and risk filters.

#### TradersPost JSON Helpers (Lines 474-485)

```pinescript
var string longEntryMsg = '{"ticker":"' + syminfo.ticker + '","price":{{close}},"time":{{timenow}},"action":"buy","sentiment":"long"}'
// ... and others
```

These lines pre-build the JSON message strings that are sent via webhooks for automated trading with services like TradersPost. Using placeholders like `{{close}}` is highly efficient, as Pine Script substitutes the values at the moment the alert triggers, rather than creating new strings on every bar.

#### ATR, MA Exit, Fixed SL/TP (Lines 487-514)

These are standard, straightforward exit mechanisms:
*   **ATR Settings**: Calculates the Average True Range (ATR), a measure of volatility, which is used by many other exit systems (like the Smart Profit Locker).
*   **MA Exit**: A simple exit where the position is closed if the price crosses a moving average of a specified type and length.
*   **Fixed SL/TP**: A standard fixed stop-loss and take-profit, where the levels are defined either in `Points` or as a multiple of `ATR`.

#### Smart Profit Locker (Lines 516-520)

```pinescript
smartProfitEnable = input.bool(true, '🎯 Enable Smart Profit Locker', ...)
smartProfitOffset = input.float(0.10, 'Pullback %', ...)
```

This is a dynamic trailing stop. It works by setting a `trail_offset`. Once the price moves in your favor by a certain amount (`smartProfitVal`), it activates a trailing stop that is a certain percentage (`smartProfitOffset`) away from the peak price. It's more "aggressive" than a simple trailing stop because it can lock in profits more quickly during a strong move.

#### Trend Change & Hybrid Exit Systems (Lines 521-529)

```pinescript
trendChangeExitEnable = input.bool(false, '📈 Enable Trend Change Exit', ...)
hybridExitEnable = input.bool(false, '🔄 Enable Hybrid Exit', ...)
hybridSwitchThreshold = input.int(2, 'Switch at Contracts', ...)
```

This section introduces two powerful, interconnected exit concepts:
*   **Trend Change Exit**: The idea is to exit a trade if one of the supporting trend indicators gives an opposite signal. For example, if you are in a long trade and the SuperTrend indicator flips to bearish, this system would trigger an exit.
*   **Hybrid Exit System**: This is a meta-system that automatically switches between the **Smart Profit Locker** and the **Trend Change Exit**. The logic is based on position size, which is used as a proxy for confluence.
    *   If your position size is small (e.g., 1 contract, below the `hybridSwitchThreshold`), it assumes only one signal fired. It uses the **Smart Profit Locker** to take profits quickly.
    *   If your position size is large (e.g., 2+ contracts), it assumes multiple signals fired in confluence. It switches to the **Trend Change Exit** to let the winner run, assuming a stronger trend is in play.

#### Advanced Trend Exit System (Lines 530-565)

This is a more sophisticated version of the Trend Change Exit, based on **Linear Regression Candles (LRC)**.
*   It has three phases:
    1.  **Initial Trailing Stop**: Protects the trade with a wide ATR-based trailing stop initially.
    2.  **Profit Mode**: Once the trade is sufficiently in profit, it switches to monitoring the LRC for an exit signal (either a candle color change or an MA cross within the LRC indicator).
    3.  **Re-Entry**: After exiting on an LRC signal, it can be configured to wait for the LRC to flip back in the original direction and re-enter the trade, but only if the supporting trend indicators agree. This is designed to capture continuations after a minor pullback.

#### Directional & Risk Filters (Lines 601-641)

*   **RBW (Relative Bandwidth) Filter**: This is a directional bias filter. It measures the bandwidth of Bollinger Bands, Keltner Channels, or Donchian Channels relative to their own historical average. The idea is that expanding bandwidth might indicate a strong trend (bullish bias) while contracting bandwidth might indicate a range (bearish or neutral bias). It can be configured to only allow long trades when the bias is bullish, and short trades when bearish.
*   **Bollinger Band Entry Filter**: This is a critical **risk management** feature. It prevents the strategy from taking long signals that occur *above* the upper Bollinger Band and short signals that occur *below* the lower Bollinger Band. This is designed to avoid entering trades on extreme price spikes or gaps, which are often followed by reversals.

---
I will now proceed with the logic and execution sections.
I am now writing the final part of the explanation to `GUIDES/EZAlgoTrader_Explanation.md`. This covers the core strategy logic, execution, and the various UI panels.

```markdown
### 7. Core Logic and Strategy Execution (Lines 786-End)

This final part of the script brings all the inputs and filters together to make trading decisions.

#### Bias and Entry Signal Calculation (Lines 806-831)

```pinescript
// SIMPLIFIED BIAS LOGIC - ONLY RBW FILTER
longDirectionalBias = rbwEnable ? (rbwSignal == 2) : true
shortDirectionalBias = rbwEnable ? (rbwSignal == 0) : true

// Define final entry signals with directional bias and Bollinger Band Filter applied
longEntrySignal := primaryLongSig and longDirectionalBias and bbLongFilterOK
shortEntrySignal := primaryShortSig and shortDirectionalBias and bbShortFilterOK
```

This is the heart of the entry decision logic. A final `longEntrySignal` or `shortEntrySignal` is only generated if three conditions are met:
1.  `primaryLongSig` / `primaryShortSig`: At least one of the 10 enabled entry signals has fired.
2.  `longDirectionalBias` / `shortDirectionalBias`: The RBW Filter (if enabled) agrees with the trade direction.
3.  `bbLongFilterOK` / `bbShortFilterOK`: The Bollinger Band risk filter has not blocked the entry.

**Critical Note**: The logic has been simplified to use only the RBW filter for directional bias. Previous complex consensus logic using multiple trend indicators has been removed. The strategy now primarily uses position size (via the Hybrid Exit system) as its method for handling confluence.

#### Virtual Account Processing (Lines 1625-1679)

This block was moved from earlier in the script to its correct logical position. The `processVirtualTrade` and `processVirtualExit` functions are called here for each of the 10 signals.

**Key Improvement**: The virtual trades are now subjected to the *exact same* filters as the main strategy (`longDirectionalBias`, `bbLongFilterOK`, etc.). This is a critical fix that ensures the virtual backtest results accurately reflect how the signals would perform under the main strategy's rules. Previously, the virtual accounts might have taken trades that the main strategy would have filtered out, leading to inaccurate performance metrics.

#### Phantom Position Detector & Auto-Fix (Lines 1703-1724)

```pinescript
if strategy.position_size != 0 and strategy.opentrades == 0
    // ... force close position
```

This is an essential "emergency fix" for a common Pine Script issue. Sometimes, due to complex interactions between `strategy.entry`, `strategy.close`, and `strategy.exit`, the strategy can get into a "phantom" state where `strategy.position_size` is not zero, but there are no open trades (`strategy.opentrades == 0`). This can completely freeze the strategy, as it thinks it's in a trade and won't take new entry signals. This code detects this state and forces a `strategy.close_all` to reset the strategy and allow new trades.

#### Entry and Exit Execution (Lines 1690-End)

This is where the actual `strategy.entry`, `strategy.close`, and `strategy.exit` commands are executed.

*   **`entryAllowed` Flag**: A boolean variable `entryAllowed` is used to control entries. It is set to `true` only when the strategy is flat (`strategy.position_size == 0`) or after an exit has occurred. It's set to `false` immediately after an entry. This is another critical fix to prevent the strategy from firing multiple entry orders on consecutive bars if a signal persists.
*   **Exit Logic**: The various exit systems (MA Exit, Fixed SL/TP, Advanced Trend Exit, etc.) are checked. If their conditions are met, they call `strategy.close()` or `strategy.exit()`. Importantly, after an exit is triggered, they now correctly set `entryAllowed := true` to permit the next trade.

### 8. Visualizations and Panels (Lines 951-1375)

The script uses Pine Script's `table` feature to create several detailed on-chart panels for monitoring the strategy's performance and status in real-time.

*   **Individual Signal Backtest Table (Lines 982-1057)**: This is the visual output of the Virtual Signal Architecture. It creates a detailed table showing the performance of *each enabled signal* separately, with metrics like P&L, win rate (broken down by long/short), max drawdown, and a profit factor. It even provides a status emoji (🟢, 🟡, 🔴) and an "Action" recommendation ('KEEP', 'CUT', 'TEST') based on the signal's performance. This is an incredibly powerful tool for optimizing the strategy by enabling/disabling signals based on their historical performance.

*   **Status/Settings Panel (Lines 1090-1165)**: A smaller panel that shows the real-time status of each enabled exit module (MA Exit, Smart Locker, Hybrid Exit, etc.) and what its current settings are.

*   **Master Panel (Lines 1166-1375)**: A comprehensive dashboard that provides a high-level overview of the strategy's current state. It includes:
    *   **RBW Bias**: The current directional bias from the RBW filter.
    *   **Signal Power**: A meter showing the number of signals currently active.
    *   **Volatility Alert**: Warns if volatility (ATR) is unusually high.
    *   **Exit System**: Shows which exit system is currently managing the trade.
    *   **Position Status**: Shows the current position (LONG/SHORT/FLAT), P&L, and duration.
    *   **Session Stats**: Tracks the win/loss record for the current day.

### Conclusion and Key Observations

This `EZAlgoTrader.pine` script is a highly sophisticated and robust trading strategy. Its key strengths are:

1.  **Perfect Attribution**: The Virtual Signal Architecture is a professional-grade feature that solves the difficult problem of knowing which signals are contributing to profitability.
2.  **Layered Risk Management**: The combination of the BB Entry Filter (preventing bad entries) and multiple, configurable exit systems (Smart Profit Locker, Trend Exits) provides a powerful framework for managing risk.
3.  **Advanced Exit Logic**: The Hybrid and Advanced Trend Exit systems are intelligent ways to adapt the exit strategy based on market conditions (confluence and trend strength).
4.  **Robust Debugging**: The built-in debug labels, status panels, and especially the phantom position detector make the strategy more reliable and easier to troubleshoot.

**Areas for Attention:**
*   The `Usage` input for each signal is not currently implemented in the logic. This could be a future enhancement to allow signals to be designated specifically as exits.
*   The script is very large and complex. While it has been optimized, any future additions should be made carefully to avoid introducing new conflicts or performance issues.

Overall, this is an exceptionally well-designed and feature-rich Pine Script strategy that demonstrates a deep understanding of both trading concepts and advanced Pine Script programming.