It looks like you've provided a Pine Script (version 6) code with a series of compilation errors. The errors are all `Undeclared identifier` errors, meaning that certain variables were used before they were declared. In Pine Script, all variables must be declared with a `var` or `varip` keyword, or implicitly declared as a `const` or `simple` type, before they are used.

To provide a comprehensive solution, I will go through each error, identify the line number, and explain why it's happening. Then, I will provide the corrected code with the necessary variable declarations.

Let's break down the errors you're encountering:

### Analysis of Undeclared Identifier Errors

1.  **`Error at 339:4 Undeclared identifier "hybridExitActive"`**

      * **Line 339:** `if hybridExitActive`
      * **Explanation:** The variable `hybridExitActive` is used here in an `if` condition. While it's later (around line 697) assigned a value using `:=`, it needs to be explicitly declared with `var` or `varip` at the top of the script or within its intended scope to be properly initialized for all bars.

2.  **`Error at 340:21 Undeclared identifier "hybridUsingSPL"`**

      * **Line 340:** `shouldRunSPL := hybridUsingSPL`
      * **Explanation:** Similar to `hybridExitActive`, `hybridUsingSPL` is assigned a value later, but its initial declaration is missing.

3.  **`Error at 477:5 Undeclared identifier "bbLongFilterOK"`**

      * **Line 477:** `bbLongFilterOK := close <= bbUpper`
      * **Explanation:** `bbLongFilterOK` is assigned a value here but is not declared. It needs a `var` or `varip` declaration at the top.

4.  **`Error at 478:5 Undeclared identifier "bbShortFilterOK"`**

      * **Line 478:** `bbShortFilterOK := close >= bbLower`
      * **Explanation:** Same issue as `bbLongFilterOK`.

5.  **`Error at 554:9 Undeclared identifier "hybridUsingTCE"`**

      * **Line 554:** `hybridUsingTCE := true`
      * **Explanation:** `hybridUsingTCE` is used here without prior declaration.

6.  **`Error at 672:43 Undeclared identifier "trendChangeExitActive"`**

      * **Line 672:** `trendChangeMsg += ' | Active=' + (trendChangeExitActive ? 'YES' : 'NO')`
      * **Explanation:** `trendChangeExitActive` is used in a string concatenation without being declared.

7.  **`Error at 673:12 Undeclared identifier "trendChangeDetected"`**

      * **Line 673:** `if trendChangeDetected`
      * **Explanation:** `trendChangeDetected` is used in an `if` statement without declaration.

8.  **`Error at 674:56 Undeclared identifier "trendChangeDetails"`**

      * **Line 674:** `trendChangeMsg += ' | CHANGE DETECTED: ' + trendChangeDetails`
      * **Explanation:** `trendChangeDetails` is used in string concatenation without declaration.

9.  **`Error at 762:36 Undeclared identifier "signal_contributions"`**

      * **Line 762:** `signal_contrib = array.get(signal_contributions, 0)`
      * **Explanation:** `signal_contributions` is used to get an array element, but the array itself is not declared.

10. **`Error at 1047:5 Undeclared identifier "volatilityRatio"`**

      * **Line 1047:** `volatilityRatio := atrCurrent / atrAvg`
      * **Explanation:** `volatilityRatio` is assigned a value without being declared.

11. **`Error at 1185:43 Undeclared identifier "current_trade_entry"`**

      * **Line 1185:** `if strategy.position_size != 0 and not na(current_trade_entry)`
      * **Explanation:** `current_trade_entry` is used in a condition without declaration.

12. **`Error at 1187:40 Undeclared identifier "current_trade_peak"`**

      * **Line 1187:** `current_trade_peak := math.max(current_trade_peak, close)`
      * **Explanation:** `current_trade_peak` is assigned without declaration.

13. **`Error at 1188:42 Undeclared identifier "current_trade_trough"`**

      * **Line 1188:** `current_trade_trough := math.min(current_trade_trough, close)`
      * **Explanation:** `current_trade_trough` is assigned without declaration.

14. **`Error at 1204:22 Undeclared identifier "current_trade_signals"`**

      * **Line 1204:** `if array.get(current_trade_signals, i)`
      * **Explanation:** `current_trade_signals` is used as an array without declaration.

15. **`Error at 1206:40 Undeclared identifier "signal_strategy_trades"`**

      * **Line 1206:** `current_trades = array.get(signal_strategy_trades, i)`
      * **Explanation:** `signal_strategy_trades` is used as an array without declaration.

16. **`Error at 1211:42 Undeclared identifier "signal_strategy_wins"`**

      * **Line 1211:** `current_wins = array.get(signal_strategy_wins, i)`
      * **Explanation:** `signal_strategy_wins` is used as an array without declaration.

You've included a section at the very end of your code (lines 1358-1369) that declares many of these missing variables. The problem is they are declared *after* they are first used. Pine Script executes code sequentially.

### Comprehensive Prompt with Specific Instructions

To fix these `Undeclared identifier` errors, we need to move the variable declarations to a point in the script *before* their first use. The best practice in Pine Script is to declare all `var` and `varip` variables at the beginning of the script, typically within the "GLOBAL VARIABLE DECLARATIONS" section.

Here's the corrected code with detailed instructions on where the missing declarations should be placed.

```pine
// 2025 Andres Garcia — EZ Algo Trader (Beta)
//  ────────────────────────────────────────────────────────────────
//  Enhanced Multi-Signal Risk Management System
//  • Professional risk management with multiple exit strategies
//  • TradersPost webhook integration for automated trading
//  • Configurable position sizing and stop-loss/take-profit levels
//  • Integrated debugging logger for development
//  ────────────────────────────────────────────────────────────────
//@version=6

// DEBUG SYSTEM
// Simple debug system using Pine Script labels - no external libraries

// Debug configuration
bool debugEnabled = input.bool(false, '🔍 Enable Debug Labels', group = '🛠️ Debug System', tooltip = 'Show debug information as chart labels')

// Combined debug function
debugMessage(string type, string message, color bgColor, color txtColor, float yOffset) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * yOffset, type + ": " + message, style=label.style_label_down, color=bgColor, textcolor=txtColor, size=size.small)

strategy(title = 'EZ Algo Trader (Beta)', overlay = true, default_qty_type = strategy.fixed, default_qty_value = 1, calc_on_order_fills = true, process_orders_on_close = true, calc_on_every_tick = false)
// User-controllable quantity

// ═══════════════════ GLOBAL VARIABLE DECLARATIONS ═══════════════════
// Declare all variables early to avoid scope issues

// Trend-riding system variables (removed - replaced by Trend Change Exit & Hybrid Exit)

// Exit system variables
var float smartOffset = na
var string exitComment = na
var float smartDistance = na
var string exitReason = na

// Strategy Performance Tracking Variables (expanded to 10 signals)
var array<float> signal_strategy_profits = array.new<float>(10, 0.0)
var array<float> signal_strategy_drawdowns = array.new<float>(10, 0.0)

// Missing variable declarations - restored
var float strategyEntryPrice = na
var bool trailExitSent = false
var bool maExitSent = false
var bool fixedExitSent = false
var bool inPosition = false
var bool currentPosition = false

// Backtesting variables
var array<int> signal_tradeCounts = array.new<int>(10, 0)
var array<int> signal_winCounts = array.new<int>(10, 0)
var array<float> signal_profits = array.new<float>(10, 0.0)
var array<float> signal_entryPrices = array.new<float>(10, na)
var array<string> signal_positions = array.new<string>(10, "none")
var array<float> signal_largestWins = array.new<float>(10, 0.0)
var array<float> signal_grossProfits = array.new<float>(10, 0.0)
var array<float> signal_grossLosses = array.new<float>(10, 0.0)

// Panel control variables
showBacktestTable = input.bool(true, '📊 Individual Signal Backtest', group = '🔍 Backtesting Panels', tooltip = 'Show individual signal performance (existing table)')
futuresMultiplier = input.int(20, '💰 Futures Multiplier', minval=1, maxval=100, group = '🔍 Backtesting Panels', tooltip = 'Point value multiplier for futures (NQ=20, ES=50, YM=5, RTY=50)')

// Advanced Trend Exit System Variables (moved here from later in script)
var bool inTrendExitMode = false
var bool inProfitMode = false
var float trendExitPrice = na
var bool waitingForReEntry = false
var int exitDirection = 0  // 1 = was long, -1 = was short
var bool reEntrySignal = false
var bool colorChangeExit = false
var bool useAdvancedTrendExit = false

// --- BEGIN INSERTION OF MISSING DECLARATIONS ---
// The following variables were used before they were declared.
// Moving them here ensures they are properly initialized before any operations.
var bool hybridExitActive = false
var bool hybridUsingSPL = false
var bool hybridUsingTCE = false
var bool bbLongFilterOK = true
var bool bbShortFilterOK = true
var bool trendChangeExitActive = false
var bool trendChangeDetected = false
var string trendChangeDetails = ""
var array<float> signal_contributions = array.new<float>(10, 0.0)
var float volatilityRatio = na
var float current_trade_entry = na
var float current_trade_peak = na
var float current_trade_trough = na
var array<bool> current_trade_signals = array.new<bool>(10, false)
var array<int> signal_strategy_trades = array.new<int>(10, 0)
var array<int> signal_strategy_wins = array.new<int>(10, 0)
var bool longEntrySignal = false
var bool shortEntrySignal = false
// --- END INSERTION OF MISSING DECLARATIONS ---

// ═══════════════════ MULTI-SIGNAL INPUT SYSTEM ═══════════════════
// ─────────────────── SIGNAL SOURCE INPUTS ───────────────────
signal1Enable = input.bool(true, '📊 Signal 1', inline = 'sig1', group = '🔄 Multi-Signals', tooltip = 'Primary signal source')
signal1LongSrc = input.source(close, 'Long', inline = 'sig1', group = '🔄 Multi-Signals')
signal1ShortSrc = input.source(close, 'Short', inline = 'sig1', group = '🔄 Multi-Signals')
signal1Name = input.string('LuxAlgo', 'Name', inline = 'sig1name', group = '🔄 Multi-Signals')
signal1Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig1name', group = '🔄 Multi-Signals')
signal1OnlyMode = input.bool(false, 'Only', inline = 'sig1only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal2Enable = input.bool(false, '📊 Signal 2', inline = 'sig2', group = '🔄 Multi-Signals')
signal2LongSrc = input.source(close, 'Long', inline = 'sig2', group = '🔄 Multi-Signals')
signal2ShortSrc = input.source(close, 'Short', inline = 'sig2', group = '🔄 Multi-Signals')
signal2Name = input.string('UTBot', 'Name', inline = 'sig2name', group = '🔄 Multi-Signals')
signal2Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig2name', group = '🔄 Multi-Signals')
signal2OnlyMode = input.bool(false, 'Only', inline = 'sig2only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal3Enable = input.bool(false, '📊 Signal 3', inline = 'sig3', group = '🔄 Multi-Signals')
signal3LongSrc = input.source(close, 'Long', inline = 'sig3', group = '🔄 Multi-Signals')
signal3ShortSrc = input.source(close, 'Short', inline = 'sig3', group = '🔄 Multi-Signals')
signal3Name = input.string('VIDYA', 'Name', inline = 'sig3name', group = '🔄 Multi-Signals')
signal3Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig3name', group = '🔄 Multi-Signals')
signal3OnlyMode = input.bool(false, 'Only', inline = 'sig3only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal4Enable = input.bool(false, '📊 Signal 4', inline = 'sig4', group = '🔄 Multi-Signals')
signal4LongSrc = input.source(close, 'Long', inline = 'sig4', group = '🔄 Multi-Signals')
signal4ShortSrc = input.source(close, 'Short', inline = 'sig4', group = '🔄 Multi-Signals')
signal4Name = input.string('KyleAlgo', 'Name', inline = 'sig4name', group = '🔄 Multi-Signals')
signal4Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig4name', group = '🔄 Multi-Signals')
signal4OnlyMode = input.bool(false, 'Only', inline = 'sig4only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal5Enable = input.bool(false, '📊 Signal 5', inline = 'sig5', group = '🔄 Multi-Signals')
signal5LongSrc = input.source(close, 'Long', inline = 'sig5', group = '🔄 Multi-Signals')
signal5ShortSrc = input.source(close, 'Short', inline = 'sig5', group = '🔄 Multi-Signals')
signal5Name = input.string('Wonder', 'Name', inline = 'sig5name', group = '🔄 Multi-Signals')
signal5Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig5name', group = '🔄 Multi-Signals')
signal5OnlyMode = input.bool(false, 'Only', inline = 'sig5only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal6Enable = input.bool(false, '📊 Signal 6', inline = 'sig6', group = '🔄 Multi-Signals')
signal6LongSrc = input.source(close, 'Long', inline = 'sig6', group = '🔄 Multi-Signals')
signal6ShortSrc = input.source(close, 'Short', inline = 'sig6', group = '🔄 Multi-Signals')
signal6Name = input.string('Signal 6', 'Name', inline = 'sig6name', group = '🔄 Multi-Signals')
signal6Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig6name', group = '🔄 Multi-Signals')
signal6OnlyMode = input.bool(false, 'Only', inline = 'sig6only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal7Enable = input.bool(false, '📊 Signal 7', inline = 'sig7', group = '🔄 Multi-Signals')
signal7LongSrc = input.source(close, 'Long', inline = 'sig7', group = '🔄 Multi-Signals')
signal7ShortSrc = input.source(close, 'Short', inline = 'sig7', group = '🔄 Multi-Signals')
signal7Name = input.string('Signal 7', 'Name', inline = 'sig7name', group = '🔄 Multi-Signals')
signal7Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig7name', group = '🔄 Multi-Signals')
signal7OnlyMode = input.bool(false, 'Only', inline = 'sig7only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal8Enable = input.bool(false, '📊 Signal 8', inline = 'sig8', group = '🔄 Multi-Signals')
signal8LongSrc = input.source(close, 'Long', inline = 'sig8', group = '🔄 Multi-Signals')
signal8ShortSrc = input.source(close, 'Short', inline = 'sig8', group = '🔄 Multi-Signals')
signal8Name = input.string('Signal 8', 'Name', inline = 'sig8name', group = '🔄 Multi-Signals')
signal8Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig8name', group = '🔄 Multi-Signals')
signal8OnlyMode = input.bool(false, 'Only', inline = 'sig8only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal9Enable = input.bool(false, '📊 Signal 9', inline = 'sig9', group = '🔄 Multi-Signals')
signal9LongSrc = input.source(close, 'Long', inline = 'sig9', group = '🔄 Multi-Signals')
signal9ShortSrc = input.source(close, 'Short', inline = 'sig9', group = '🔄 Multi-Signals')
signal9Name = input.string('Signal 9', 'Name', inline = 'sig9name', group = '🔄 Multi-Signals')
signal9Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig9name', group = '🔄 Multi-Signals')
signal9OnlyMode = input.bool(false, 'Only', inline = 'sig9only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

signal10Enable = input.bool(false, '📊 Signal 10', inline = 'sig10', group = '🔄 Multi-Signals')
signal10LongSrc = input.source(close, 'Long', inline = 'sig10', group = '🔄 Multi-Signals')
signal10ShortSrc = input.source(close, 'Short', inline = 'sig10', group = '🔄 Multi-Signals')
signal10Name = input.string('Signal 10', 'Name', inline = 'sig10name', group = '🔄 Multi-Signals')
signal10Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig10name', group = '🔄 Multi-Signals')
signal10OnlyMode = input.bool(false, 'Only', inline = 'sig10only', group = '🔄 Multi-Signals', tooltip = 'Make this the ONLY active signal (for individual testing)')

// ─────────────────── SIGNAL PROCESSING ───────────────────
// Process signals directly - most indicators (UTBot, LuxAlgo) output pulse signals
// Handle both boolean and numeric signal types
// IMPORTANT: Only process real signals, not default 'close' values
// Simple direct signal detection - no complex change detection needed
// Check if any "Only Mode" is active (for individual signal testing)
anyOnlyModeActive = signal1OnlyMode or signal2OnlyMode or signal3OnlyMode or signal4OnlyMode or signal5OnlyMode or signal6OnlyMode or signal7OnlyMode or signal8OnlyMode or signal9OnlyMode or signal10OnlyMode

// Process all signals with "Only Mode" logic (FIXED - properly ignore 'close' as signal)
// Only trigger signals if the source is NOT the default 'close' AND the signal condition is met
sig1Long = signal1Enable and signal1LongSrc != close and (signal1LongSrc > 0 or bool(signal1LongSrc) == true)
sig1Short = signal1Enable and signal1ShortSrc != close and (signal1ShortSrc > 0 or bool(signal1ShortSrc) == true)
sig2Long = signal2Enable and signal2LongSrc != close and (signal2LongSrc > 0 or bool(signal2LongSrc) == true)
sig2Short = signal2Enable and signal2ShortSrc != close and (signal2ShortSrc > 0 or bool(signal2ShortSrc) == true)
sig3Long = signal3Enable and signal3LongSrc != close and (signal3LongSrc > 0 or bool(signal3LongSrc) == true)
sig3Short = signal3Enable and signal3ShortSrc != close and (signal3ShortSrc > 0 or bool(signal3ShortSrc) == true)
sig4Long = signal4Enable and signal4LongSrc != close and (signal4LongSrc > 0 or bool(signal4LongSrc) == true)
sig4Short = signal4Enable and signal4ShortSrc != close and (signal4ShortSrc > 0 or bool(signal4ShortSrc) == true)
sig5Long = signal5Enable and signal5LongSrc != close and (signal5LongSrc > 0 or bool(signal5LongSrc) == true)
sig5Short = signal5Enable and signal5ShortSrc != close and (signal5ShortSrc > 0 or bool(signal5ShortSrc) == true)
sig6Long = signal6Enable and signal6LongSrc != close and (signal6LongSrc > 0 or bool(signal6LongSrc) == true)
sig6Short = signal6Enable and signal6ShortSrc != close and (sig6ShortSrc > 0 or bool(sig6ShortSrc) == true)
sig7Long = signal7Enable and signal7LongSrc != close and (sig7LongSrc > 0 or bool(sig7LongSrc) == true)
sig7Short = signal7Enable and sig7ShortSrc != close and (sig7ShortSrc > 0 or bool(sig7ShortSrc) == true)
sig8Long = signal8Enable and signal8LongSrc != close and (sig8LongSrc > 0 or bool(sig8LongSrc) == true)
sig8Short = signal8Enable and signal8ShortSrc != close and (sig8ShortSrc > 0 or bool(sig8ShortSrc) == true)
sig9Long = signal9Enable and signal9LongSrc != close and (sig9LongSrc > 0 or bool(sig9LongSrc) == true)
sig9Short = signal9Enable and signal9ShortSrc != close and (sig9ShortSrc > 0 or bool(sig9ShortSrc) == true)
sig10Long = signal10Enable and signal10LongSrc != close and (sig10LongSrc > 0 or bool(sig10LongSrc) == true)
sig10Short = signal10Enable and signal10ShortSrc != close and (sig10ShortSrc > 0 or bool(sig10ShortSrc) == true)

// Apply "Only Mode" logic - if any "Only" is active, disable all signals except the one in "Only Mode"
if anyOnlyModeActive
    sig1Long := signal1OnlyMode ? sig1Long : false
    sig1Short := signal1OnlyMode ? sig1Short : false
    sig2Long := signal2OnlyMode ? sig2Long : false
    sig2Short := signal2OnlyMode ? sig2Short : false
    sig3Long := signal3OnlyMode ? sig3Long : false
    sig3Short := signal3OnlyMode ? sig3Short : false
    sig4Long := signal4OnlyMode ? sig4Long : false
    sig4Short := signal4OnlyMode ? sig4Short : false
    sig5Long := signal5OnlyMode ? sig5Long : false
    sig5Short := signal5OnlyMode ? sig5Short : false
    sig6Long := signal6OnlyMode ? sig6Long : false
    sig6Short := signal6OnlyMode ? sig6Short : false
    sig7Long := signal7OnlyMode ? sig7Long : false
    sig7Short := signal7OnlyMode ? sig7Short : false
    sig8Long := signal8OnlyMode ? sig8Long : false
    sig8Short := signal8OnlyMode ? sig8Short : false
    sig9Long := signal9OnlyMode ? sig9Long : false
    sig9Short := signal9OnlyMode ? sig9Short : false
    sig10Long := signal10OnlyMode ? sig10Long : false
    sig10Short := signal10OnlyMode ? sig10Short : false

// ─────────────────── SIGNAL ARRAYS FOR PROCESSING ───────────────────
// Signal arrays for processing and count tracking (expanded to 10 signals)
allLongSignals = array.new<bool>(10)
allShortSignals = array.new<bool>(10)
array.set(allLongSignals, 0, sig1Long)
array.set(allLongSignals, 1, sig2Long)
array.set(allLongSignals, 2, sig3Long)
array.set(allLongSignals, 3, sig4Long)
array.set(allLongSignals, 4, sig5Long)
array.set(allLongSignals, 5, sig6Long)
array.set(allLongSignals, 6, sig7Long)
array.set(allLongSignals, 7, sig8Long)
array.set(allLongSignals, 8, sig9Long)
array.set(allLongSignals, 9, sig10Long)
array.set(allShortSignals, 0, sig1Short)
array.set(allShortSignals, 1, sig2Short)
array.set(allShortSignals, 2, sig3Short)
array.set(allShortSignals, 3, sig4Short)
array.set(allShortSignals, 4, sig5Short)
array.set(allShortSignals, 5, sig6Short)
array.set(allShortSignals, 6, sig7Short)
array.set(allShortSignals, 7, sig8Short)
array.set(allShortSignals, 8, sig9Short)
array.set(allShortSignals, 9, sig10Short)

// Count active signals (all 10 signals)
var int longSignalCount = 0
var int shortSignalCount = 0
longSignalCount := (sig1Long ? 1 : 0) + (sig2Long ? 1 : 0) + (sig3Long ? 1 : 0) + (sig4Long ? 1 : 0) + (sig5Long ? 1 : 0) + (sig6Long ? 1 : 0) + (sig7Long ? 1 : 0) + (sig8Long ? 1 : 0) + (sig9Long ? 1 : 0) + (sig10Long ? 1 : 0)
shortSignalCount := (sig1Short ? 1 : 0) + (sig2Short ? 1 : 0) + (sig3Short ? 1 : 0) + (sig4Short ? 1 : 0) + (sig5Short ? 1 : 0) + (sig6Short ? 1 : 0) + (sig7Short ? 1 : 0) + (sig8Short ? 1 : 0) + (sig9Short ? 1 : 0) + (sig10Short ? 1 : 0)

// ═══════════════════ PRIMARY SIGNAL COMBINATION ═══════════════════
// Note: Primary signals are defined below after imports (line 126-127)

// ═══════════════════ RBW FILTER IMPORT ═══════════════════
// Import enhanced_ta library for existing RBW filter (defined later)
import HeWhoMustNotBeNamed/enhanced_ta/14 as eta

// ═══════════════════ SIGNAL PROCESSING SETUP ═══════════════════
// Legacy compatibility - combine all signals (expanded to 10 signals)
primaryLongSig = sig1Long or sig2Long or sig3Long or sig4Long or sig5Long or sig6Long or sig7Long or sig8Long or sig9Long or sig10Long
primaryShortSig = sig1Short or sig2Short or sig3Short or sig4Short or sig5Short or sig6Short or sig7Short or sig8Short or sig9Short or sig10Short

// ─────────────────── 1 · TRADERSPOST JSON HELPERS ───────────────

// ───── Pre-built JSON messages (compile-time constants) ─────
// Use TradingView alert placeholders so we avoid any per-bar string operations.
// Placeholders {{close}} and {{timenow}} will be expanded at alert trigger time.
var string _jsonBase = '{"ticker":"' + syminfo.ticker + '","price":{{close}},"time":{{timenow}}'

var string longEntryMsg = _jsonBase + ',"action":"buy","sentiment":"long"}'
var string shortEntryMsg = _jsonBase + ',"action":"sell","sentiment":"short"}'
var string flatExitMsg = _jsonBase + ',"action":"exit","sentiment":"flat"}'
var string longExitMsg = _jsonBase + ',"action":"sell","sentiment":"flat"}' // closes long
var string shortExitMsg = _jsonBase + ',"action":"buy","sentiment":"flat"}' // closes short

// ─────────────────── 2 · ATR SETTINGS ───────────────────────────
atrLen = input.int(14, 'ATR Length', minval = 1, group = 'ATR Settings')
atrVal = ta.atr(atrLen)
// Extract ta.sma calculation for volatility alert (fixes compilation warning)
atrAvg = ta.sma(atrVal, 20)

// ─────────────────── 3 · EXIT PARAMETERS (ASCII SAFE) ───────────
maExitOn = input.bool(false, 'Enable MA Exit', group = 'MA Exit')
maLen = input.int(21, 'MA Length', minval = 1, group = 'MA Exit')
maType = input.string('EMA', 'MA Type', options = ['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA'], group = 'MA Exit')
// Intrabar exits removed - exits only trigger once per bar on close

priceMA = maType == 'SMA' ? ta.sma(close, maLen) : maType == 'EMA' ? ta.ema(close, maLen) : maType == 'WMA' ? ta.wma(close, maLen) : maType == 'VWMA' ? ta.vwma(close, maLen) : ta.rma(close, maLen)

fixedEnable = input.bool(false, 'Enable Fixed SL/TP', group = 'Fixed SL/TP')
fixedUnit = input.string('ATR', 'Unit', options = ['ATR', 'Points'], group = 'Fixed SL/TP')
fixedStop = input.float(1.0, 'Stop Size', step = 0.1, minval = 0.0, group = 'Fixed SL/TP')

tpCalc(d) =>
    fixedUnit == 'ATR' ? d * atrVal : d

tp1Enable = input.bool(false, 'TP1', inline = 'tp1', group = 'Fixed SL/TP')
tp1Size = input.float(1.5, '', inline = 'tp1', group = 'Fixed SL/TP')
tp2Enable = input.bool(false, 'TP2', inline = 'tp2', group = 'Fixed SL/TP')
tp2Size = input.float(3.0, '', inline = 'tp2', group = 'Fixed SL/TP')
tp3Enable = input.bool(false, 'TP3', inline = 'tp3', group = 'Fixed SL/TP')
tp3Size = input.float(4.0, '', inline = 'tp3', group = 'Fixed SL/TP')

// Smart Profit Locker (Aggressive Profit Protection)
smartProfitEnable = input.bool(true, '🎯 Enable Smart Profit Locker', group = 'Smart Profit Locker', tooltip = 'Aggressive profit-taking with adjustable pullback sensitivity')
smartProfitType = input.string('ATR', 'Type', options = ['ATR', 'Points', 'Percent'], group = 'Smart Profit Locker')
smartProfitVal = input.float(3.1, 'Value', step = 0.1, group = 'Smart Profit Locker')
smartProfitOffset = input.float(0.10, 'Pullback %', step = 0.05, minval = 0.01, maxval = 1.0, group = 'Smart Profit Locker', tooltip = 'Pullback percentage to trigger exit (0.10 = 10%)')

// TREND CHANGE EXIT SYSTEM
// Simple and effective: exit when any enabled trend indicator gives opposite signal
trendChangeExitEnable = input.bool(false, '📈 Enable Trend Change Exit', group = 'EXIT STRATEGY: Trend Change Exit', tooltip = 'Exit when any enabled trend signal gives opposite direction. Uses the 5 Trend Signals configured below.')

// HYBRID EXIT SYSTEM
// Automatically switch between Smart Profit Locker and Trend Change Exit based on position size
hybridExitEnable = input.bool(false, '🔄 Enable Hybrid Exit', group = '🔄 Hybrid Exit System', tooltip = 'Automatically use Smart Profit Locker for small positions, Trend Change Exit for larger positions (confluence-based)')
hybridSwitchThreshold = input.int(2, 'Switch at Contracts', minval=1, maxval=10, group = '🔄 Hybrid Exit System', tooltip='Number of contracts to switch from Smart Profit Locker to Trend Change Exit (default: 2 = confluence detected)')

// ═══════════════════ ADVANCED TREND EXIT SYSTEM ═══════════════════
// Linear Regression Candle-based exit with trailing stop protection and re-entry logic

// Enable Trend Exit Modes
trendExitEnable = input.bool(false, '🎯 Enable Advanced Trend Exit', group = '🎯 Advanced Trend Exit', tooltip = 'Advanced exit system based on Linear Regression Candles with trailing stop protection')
hybridModeEnable = input.bool(false, '🔄 Activate Hybrid Mode', group = '🎯 Advanced Trend Exit', tooltip = 'Use this exit system in Hybrid mode (switches based on position size)')

// Phase 1: Initial Trailing Stop Protection
initialTrailingStop = input.float(3.5, 'Initial Trailing Stop (ATR)', step=0.1, minval=1.0, maxval=10.0, group = '🎯 Advanced Trend Exit', tooltip = 'ATR multiplier for initial trailing stop protection before switching to candle exit mode')

// Phase 2: Exit Mode Selection  
exitMode = input.string('Color Change', 'Exit Signal Type', options=['Color Change', 'MA Cross'], group = '🎯 Advanced Trend Exit', tooltip = 'Color Change = Exit on Linear Regression candle color change | MA Cross = Exit on Bull/Bear MA crosses')

// Phase 3: Re-Entry System
reEntryEnable = input.bool(false, '🔄 Enable Re-Entry', group = '🎯 Advanced Trend Exit', tooltip = 'After color change exit, re-enter when color changes back (if trend indicators confirm)')
reEntryConfirmation = input.string('Majority', 'Re-Entry Confirmation', options=['All', 'Majority'], group = '🎯 Advanced Trend Exit', tooltip = 'Require All (3/3) or Majority (2/3) trend indicators to confirm re-entry direction')

// Linear Regression Candle Inputs (Primary Exit Signal)
lrcBullColorChange = input.source(close, '🟢 Bull Color Change', group = '📊 Linear Regression Candles', tooltip = 'Connect to Linear Regression Candles Bull Color Change plot')
lrcBearColorChange = input.source(close, '🔴 Bear Color Change', group = '📊 Linear Regression Candles', tooltip = 'Connect to Linear Regression Candles Bear Color Change plot')
lrcBullMAXross = input.source(close, '📈 Bull MA Cross', group = '📊 Linear Regression Candles', tooltip = 'Connect to Linear Regression Candles Bull MA Cross plot')
lrcBearMACross = input.source(close, '📉 Bear MA Cross', group = '📊 Linear Regression Candles', tooltip = 'Connect to Linear Regression Candles Bear MA Cross plot')

// Supporting Trend Indicators (Reduced from 5 to 3)
trend1Enable = input.bool(false, '📈 Trend 1: Adaptive SuperTrend', inline = 'trend1', group = '🎯 Supporting Trend Indicators', tooltip = 'Adaptive SuperTrend for trend confirmation')
trend1LongSrc = input.source(close, 'Long', inline = 'trend1', group = '🎯 Supporting Trend Indicators')
trend1ShortSrc = input.source(close, 'Short', inline = 'trend1', group = '🎯 Supporting Trend Indicators')

trend2Enable = input.bool(false, '📈 Trend 2: Custom', inline = 'trend2', group = '🎯 Supporting Trend Indicators')
trend2LongSrc = input.source(close, 'Long', inline = 'trend2', group = '🎯 Supporting Trend Indicators')
trend2ShortSrc = input.source(close, 'Short', inline = 'trend2', group = '🎯 Supporting Trend Indicators')

trend3Enable = input.bool(false, '📈 Trend 3: Custom', inline = 'trend3', group = '🎯 Supporting Trend Indicators')
trend3LongSrc = input.source(close, 'Long', inline = 'trend3', group = '🎯 Supporting Trend Indicators')
trend3ShortSrc = input.source(close, 'Short', inline = 'trend3', group = '🎯 Supporting Trend Indicators')

// ─────────────────── 4. SMART PROFIT LOCKER (Aggressive Profit Protection) ────────────
// HYBRID EXIT INTEGRATION: Switch between Smart Profit Locker and Trend Change Exit

// SMART PROFIT LOCKER - SIMPLIFIED  
// Smart Profit Locker: Original logic + Hybrid Exit integration
shouldRunSPL = smartProfitEnable and strategy.position_size != 0
if hybridExitActive
    shouldRunSPL := hybridUsingSPL  // Hybrid system overrides SPL decision
    
if shouldRunSPL
    // Calculate Smart Profit Locker distance and offset
    smartDistance := smartProfitType == 'ATR' ? smartProfitVal * atrVal : smartProfitType == 'Points' ? smartProfitVal : strategyEntryPrice * smartProfitVal / 100.0
    smartOffset := smartDistance * math.max(smartProfitOffset, 0.01)
    
    // Ensure distances are valid
    if na(smartDistance) or smartDistance <= 0
        smartDistance := 50.0  // Safe default value in points
    if na(smartOffset) or smartOffset <= 0
        smartOffset := 5.0  // Safe default offset
    
    if strategy.position_size > 0  // Long position
        strategy.exit('Smart-Long', from_entry='Long', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment='Smart Profit Locker')
        if not trailExitSent
            trailExitSent := true
            debugMessage("INFO", "🎯 Smart Profit Locker activated - Distance: " + str.tostring(smartDistance, "#.##") + " pts", color.green, color.white, 0.05)
    else if strategy.position_size < 0  // Short position
        strategy.exit('Smart-Short', from_entry='Short', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment='Smart Profit Locker')
        if not trailExitSent
            trailExitSent := true
            debugMessage("INFO", "🎯 Smart Profit Locker activated - Distance: " + str.tostring(smartDistance, "#.##") + " pts", color.green, color.white, 0.05)

// OLD TREND CHANGE EXIT SYSTEM - REMOVED
// This old system was interfering with the new Advanced Trend Exit System
// All trend change exit logic is now handled by the Advanced Trend Exit System below

// RBW DIRECTIONAL FILTER
// Compact multi-column panel for volatility-based directional bias
rbwEnable = input.bool(false, '📊 Enable RBW Filter', group='📊 Relative Bandwidth Filter', inline='rbw0')

// Band Type Selection (Row 1)
rbwBandType = input.string("KC", "Band Type", group='📊 Relative Bandwidth Filter', options=["BB", "KC", "DC"], inline='rbw1')
rbwSource = input.source(close, "Source", group='📊 Relative Bandwidth Filter', inline='rbw1')

// Band Parameters (Row 2) 
rbwLength = input.int(100, "Length", group='📊 Relative Bandwidth Filter', inline='rbw2')
rbwMultiplier = input.float(2.0, "Mult", step=0.5, group='📊 Relative Bandwidth Filter', inline='rbw2')

// Additional Options (Row 3)
rbwUseTR = input.bool(true, "Use TR", group='📊 Relative Bandwidth Filter', inline='rbw3')
rbwUseAltSrc = input.bool(false, "Alt Src", group='📊 Relative Bandwidth Filter', inline='rbw3')

// Signal Configuration (Row 4)
rbwDesiredCond = input.string("Higher Bandwidth", "Condition", group='📊 Relative Bandwidth Filter', options=["Higher Bandwidth", "Lower Bandwidth"], inline='rbw4')
rbwRefBand = input.string("Middle", "Reference", group='📊 Relative Bandwidth Filter', options=["Upper", "Lower", "Middle"], inline='rbw4')

// ATR and Filter Settings (Row 5)
rbwATRLength = input.int(20, "ATR Len", group='📊 Relative Bandwidth Filter', inline='rbw5')
rbwBBType = input.string("sma", "BB Type", group='📊 Relative Bandwidth Filter', options=["sma", "ema", "hma", "rma", "wma", "vwma", "linreg", "median"], inline='rbw5')

// OLD TREND SIGNAL SYSTEM - REPLACED BY ADVANCED TREND EXIT SYSTEM
// This section is kept for compatibility with existing exit logic but will be phased out

// Trend logic simplified: ALL enabled filters must agree (no more voting system)

// BOLLINGER BAND RISK MANAGEMENT
// Critical risk management: Never take signals outside Bollinger Bands

// Bollinger Band Entry Filter (Row 1) - RISK MANAGEMENT
bbEntryFilterEnable = input.bool(false, '🚫 BB Entry Filter', group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='Ignore buy signals above upper BB and sell signals below lower BB. Prevents gap-open disasters.')
bbLength = input.int(20, 'BB Length', minval=5, maxval=50, group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='Bollinger Band period for entry filtering. Standard is 20.')
bbMultiplier = input.float(3.0, 'BB Mult', minval=1.0, maxval=5.0, step=0.1, group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='Bollinger Band standard deviation multiplier. Default is 3.0.')

// Bollinger Band Exit Logic (Row 2) - OPTIONAL TESTING FEATURE
bbExitEnable = input.bool(false, '⚡ BB Exit Logic', group='🎯 Bollinger Band Risk Management', inline='bb2', tooltip='OPTIONAL: When price hits BB extreme during trade, flip to tight Smart Profit Locker. OFF by default - for testing only.')
bbExitTightness = input.float(0.5, 'Tight Multiplier', minval=0.1, maxval=1.0, step=0.1, group='🎯 Bollinger Band Risk Management', inline='bb2', tooltip='Smart Profit Locker multiplier when BB exit triggers. 0.5 = half normal distance (tighter). Lower = more aggressive profit taking.')

// RBW Calculation Logic - Variable Declarations
var float rbwUpper = na
var float rbwLower = na
var float rbwMiddle = na
var float rbwRelativeBandwidth = na
var int rbwSignal = 0
var float rbwBBMiddle = na
var float rbwBBUpper = na
var float rbwBBLower = na
var float rbwRef = na
var float rbwStdDev = na
var float rbwATR = na

if rbwEnable
    // Bollinger Bands
    if rbwBandType == "BB"
        rbwMiddle := ta.sma(rbwSource, rbwLength)
        rbwStdDev := ta.stdev(rbwSource, rbwLength)
        rbwUpper := rbwMiddle + rbwStdDev * rbwMultiplier
        rbwLower := rbwMiddle - rbwStdDev * rbwMultiplier
    
    // Keltner Channels  
    else if rbwBandType == "KC"
        rbwMiddle := ta.sma(rbwSource, rbwLength)
        rbwATR := rbwUseTR ? ta.atr(rbwLength) : ta.rma(high - low, rbwLength)
        rbwUpper := rbwMiddle + rbwATR * rbwMultiplier
        rbwLower := rbwMiddle - rbwATR * rbwMultiplier
    
    // Donchian Channels
    else if rbwBandType == "DC"
        rbwUpper := ta.highest(rbwUseAltSrc ? high : rbwSource, rbwLength)
        rbwLower := ta.lowest(rbwUseAltSrc ? low : rbwSource, rbwLength)
        rbwMiddle := (rbwUpper + rbwLower) / 2
        rbwATR := ta.atr(rbwATRLength)

    // Relative Bandwidth calculation
    if not na(rbwUpper) and not na(rbwLower)
        // Use pre-calculated ATR
        if not na(rbwATR) and rbwATR > 0
            rbwRelativeBandwidth := (rbwUpper - rbwLower) / rbwATR
            
            // Calculate reference bands for signal (use extracted TA functions)
            rbwBBMiddle := ta.sma(rbwRelativeBandwidth, 100)
            rbwBBUpper := rbwBBMiddle + ta.stdev(rbwRelativeBandwidth, 100) * 1.0
            rbwBBLower := rbwBBMiddle - ta.stdev(rbwRelativeBandwidth, 100) * 1.0
            
            rbwRef := rbwRefBand == "Middle" ? rbwBBMiddle : rbwRefBand == "Upper" ? rbwBBUpper : rbwBBLower
            rbwSignal := rbwRelativeBandwidth > rbwRef ? 2 : 0
            rbwSignal := rbwDesiredCond == "Lower Bandwidth" ? math.abs(rbwSignal-2) : rbwSignal

// BOLLINGER BAND RISK MANAGEMENT CALCULATION
// Critical entry filtering: Never take signals outside Bollinger Bands

// Bollinger Band Variables
var float bbUpper = na
var float bbLower = na
var float bbMiddle = na
// bbLongFilterOK and bbShortFilterOK are now declared at the top.
var bool bbExitTriggered = false

if bbEntryFilterEnable
    // Calculate standard Bollinger Bands for entry filtering
    bbMiddle := ta.sma(close, bbLength)
    bbStdDev = ta.stdev(close, bbLength)
    bbUpper := bbMiddle + bbStdDev * bbMultiplier
    bbLower := bbMiddle - bbStdDev * bbMultiplier
    
    // Entry filter logic: Block signals outside bands
    bbLongFilterOK := close <= bbUpper  // Allow long entries only when price is NOT above upper band
    bbShortFilterOK := close >= bbLower  // Allow short entries only when price is NOT below lower band
    
    // Debug output for BB entry filter
    if debugEnabled
        if not bbLongFilterOK
            debugMessage("WARN", "🚫 BB ENTRY FILTER: Long signal blocked - Price $" + str.tostring(close, "#.##") + " above upper BB $" + str.tostring(bbUpper, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)
        if not bbShortFilterOK
            debugMessage("WARN", "🚫 BB ENTRY FILTER: Short signal blocked - Price $" + str.tostring(close, "#.##") + " below lower BB $" + str.tostring(bbLower, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)
else
    // When disabled, allow all entries
    bbLongFilterOK := true
    bbShortFilterOK := true

// Optional BB Exit Logic (for testing)
if bbExitEnable and strategy.position_size != 0
    // Check if price hits BB extreme during trade
    longAtUpperBB = strategy.position_size > 0 and close >= bbUpper
    shortAtLowerBB = strategy.position_size < 0 and close <= bbLower
    
    if longAtUpperBB or shortAtLowerBB
        bbExitTriggered := true
        if debugEnabled
            exitType = longAtUpperBB ? "Long at Upper BB" : "Short at Lower BB"
            debugMessage("INFO", "⚡ BB EXIT TRIGGER: " + exitType + " - Switching to tight Smart Profit Locker", color.green, color.white, 0.05)
else
    bbExitTriggered := false

// OLD TREND SIGNAL PROCESSING - REPLACED BY ADVANCED TREND EXIT SYSTEM
// Keeping minimal compatibility variables
var bool trend1Long = false
var bool trend1Short = false
var bool trend2Long = false  
var bool trend2Short = false
var bool trend3Long = false
var bool trend3Short = false
var int trend1Signal = 0
var int trend2Signal = 0
var int trend3Signal = 0

// ═══════════════════ LINEAR REGRESSION CANDLE PROCESSING ═══════════════════
// Process Linear Regression Candle signals for advanced trend exit

// Process LRC signals (ignore default 'close' values)
lrcBullColor = lrcBullColorChange != close and (lrcBullColorChange > 0 or bool(lrcBullColorChange) == true)
lrcBearColor = lrcBearColorChange != close and (lrcBearColorChange > 0 or bool(lrcBearColorChange) == true)
lrcBullMA = lrcBullMAXross != close and (lrcBullMAXross > 0 or bool(lrcBullMAXross) == true)
lrcBearMA = lrcBearMACross != close and (lrcBearMACross > 0 or bool(lrcBearMACross) == true)

// Supporting trend indicators (reduced to 3)
supportTrend1Long = trend1Enable and trend1Long
supportTrend1Short = trend1Enable and trend1Short
supportTrend2Long = trend2Enable and trend2Long  
supportTrend2Short = trend2Enable and trend2Short
supportTrend3Long = trend3Enable and trend3Long
supportTrend3Short = trend3Enable and trend3Short

// Count supporting trend indicators for re-entry confirmation
enabledTrendCount = (trend1Enable ? 1 : 0) + (trend2Enable ? 1 : 0) + (trend3Enable ? 1 : 0)
bullishTrendCount = (supportTrend1Long ? 1 : 0) + (supportTrend2Long ? 1 : 0) + (supportTrend3Long ? 1 : 0)
bearishTrendCount = (supportTrend1Short ? 1 : 0) + (supportTrend2Short ? 1 : 0) + (supportTrend3Short ? 1 : 0)

// Determine trend confirmation for re-entry
trendConfirmsLong = enabledTrendCount == 0 ? true : reEntryConfirmation == 'All' ? bullishTrendCount == enabledTrendCount : bullishTrendCount >= math.ceil(enabledTrendCount / 2.0)

trendConfirmsShort = enabledTrendCount == 0 ? true : reEntryConfirmation == 'All' ? bearishTrendCount == enabledTrendCount : bearishTrendCount >= math.ceil(enabledTrendCount / 2.0)

// HYBRID EXIT SYSTEM LOGIC
// Automatically choose exit strategy based on position size (confluence indicator)
hybridExitActive := hybridExitEnable and strategy.position_size != 0
currentPositionSize = math.abs(strategy.position_size)

if hybridExitActive
    // Determine which exit strategy to use based on position size
    if currentPositionSize >= hybridSwitchThreshold
        // Large position (confluence detected) = Trend Change Exit
        hybridUsingSPL := false
        hybridUsingTCE := true
    else
        // Small position (single signal) = Smart Profit Locker  
        hybridUsingSPL := true
        hybridUsingTCE := false
else
    // Reset hybrid flags when not active
    hybridUsingSPL := false
    hybridUsingTCE := false

// SIMPLIFIED BIAS LOGIC - ONLY RBW FILTER
// Removed complex trend signal consensus - using position size for confluence instead
// Only RBW (Relative Bandwidth) provides directional bias filtering

longDirectionalBias = rbwEnable ? (rbwSignal == 2) : true    // RBW bullish or disabled (permissive)  
shortDirectionalBias = rbwEnable ? (rbwSignal == 0) : true   // RBW bearish or disabled (permissive)

// TEMPORARY TEST OVERRIDE (recommended by AI analysis) - Set to false after confirming signals work
testModeOverride = input.bool(true, "🧪 Test Mode Override", group="🛠️ Debug System", tooltip="Temporarily override all filters to allow trades for testing")
if testModeOverride
    longDirectionalBias := true
    shortDirectionalBias := true

// TREND-RIDING OVERLAY LOGIC
// Advanced exit system to "let winners run" in strong trending conditions

// Define final entry signals with directional bias and Bollinger Band Filter applied
longEntrySignal := primaryLongSig and longDirectionalBias and bbLongFilterOK
shortEntrySignal := primaryShortSig and shortDirectionalBias and bbShortFilterOK

// Add re-entry signals to entry logic
if reEntrySignal and exitDirection == 1  // Re-entering long
    longEntrySignal := true
else if reEntrySignal and exitDirection == -1  // Re-entering short  
    shortEntrySignal := true

// Visual indicators for Advanced Trend Exit System
plotchar(inTrendExitMode, title='🎯 Trend Exit Mode', char='🎯', location=location.top, color=color.new(color.blue, 0), size=size.small)
plotchar(inProfitMode, title='💰 Profit Mode', char='💰', location=location.top, color=color.new(color.green, 0), size=size.small)
plotchar(waitingForReEntry, title='⏳ Waiting Re-Entry', char='⏳', location=location.top, color=color.new(color.orange, 0), size=size.small)
plotchar(colorChangeExit, title='🔄 Color Change Exit', char='🔄', location=location.top, color=color.new(color.red, 0), size=size.small)
plotchar(reEntrySignal, title='🚀 Re-Entry Signal', char='🚀', location=location.top, color=color.new(color.purple, 0), size=size.small)

// Plot Linear Regression Candle signals for visibility
plotchar(lrcBullColor, title='🟢 LRC Bull Color', char='🟢', location=location.bottom, color=color.new(color.lime, 0), size=size.tiny)
plotchar(lrcBearColor, title='🔴 LRC Bear Color', char='🔴', location=location.bottom, color=color.new(color.red, 0), size=size.tiny)
plotchar(lrcBullMA, title='📈 LRC Bull MA', char='📈', location=location.bottom, color=color.new(color.blue, 0), size=size.tiny)
plotchar(lrcBearMA, title='📉 LRC Bear MA', char='📉', location=location.bottom, color=color.new(color.maroon, 0), size=size.tiny)

// TEMPORARY DEBUG PLOTS (recommended by AI analysis) - Remove after confirming signals work
plotchar(sig1Long, title="Sig1Long", char='1', location=location.bottom, color=color.yellow, size=size.tiny)
plotchar(primaryLongSig, title="PrimaryLong", char='●', location=location.bottom, color=color.orange, size=size.tiny)
plotchar(longDirectionalBias, title="LongBias", char='B', location=location.bottom, color=color.blue, size=size.tiny)
plotchar(longEntrySignal, title="LongFinal", char='↑', location=location.bottom, color=color.lime, size=size.small)

// Debug warnings when Bollinger Band filter blocks trades (critical risk management)
if debugEnabled and bbEntryFilterEnable
    if primaryLongSig and longDirectionalBias and not bbLongFilterOK
        debugMessage("WARN", "🚫 BB FILTER BLOCKED LONG: Price " + str.tostring(close, "#.##") + " above upper BB " + str.tostring(bbUpper, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)
    if primaryShortSig and shortDirectionalBias and not bbShortFilterOK
        debugMessage("WARN", "🚫 BB FILTER BLOCKED SHORT: Price " + str.tostring(close, "#.##") + " below lower BB " + str.tostring(bbLower, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)

// Enhanced debug logging for all directional bias filters and systems  
if debugEnabled
    // Debug entry signals
    if primaryLongSig or primaryShortSig
        entryMsg = 'ENTRY SIGNALS: Long=' + str.tostring(primaryLongSig) + ' Short=' + str.tostring(primaryShortSig)
        entryMsg += ' | LongBias=' + str.tostring(longDirectionalBias) + ' ShortBias=' + str.tostring(shortDirectionalBias)
        entryMsg += ' | BBFilter: L=' + str.tostring(bbLongFilterOK) + ' S=' + str.tostring(bbShortFilterOK)
        entryMsg += ' | Final: L=' + str.tostring(longEntrySignal) + ' S=' + str.tostring(shortEntrySignal)
        debugMessage("INFO", entryMsg, color.blue, color.white, 0.05)
    
    // Debug Advanced Trend Exit System
    if useAdvancedTrendExit
        trendExitMsg = 'ADVANCED TREND EXIT:'
        trendExitMsg += ' Mode=' + (inTrendExitMode ? 'ACTIVE' : 'OFF')
        trendExitMsg += ' Phase=' + (not inTrendExitMode ? 'NONE' : inProfitMode ? 'PROFIT' : 'PROTECTION')
        trendExitMsg += ' Exit=' + exitMode
        if waitingForReEntry
            trendExitMsg += ' | WAITING_RE-ENTRY (Dir=' + str.tostring(exitDirection) + ')'
        debugMessage("INFO", trendExitMsg, color.purple, color.white, 0.05)
        
        // Debug LRC signals
        if lrcBullColor or lrcBearColor or lrcBullMA or lrcBearMA
            lrcMsg = 'LRC SIGNALS:'
            lrcMsg += ' BullColor=' + str.tostring(lrcBullColor)
            lrcMsg += ' BearColor=' + str.tostring(lrcBearColor) 
            lrcMsg += ' BullMA=' + str.tostring(lrcBullMA)
            lrcMsg += ' BearMA=' + str.tostring(lrcBearMA)
            debugMessage("INFO", lrcMsg, color.yellow, color.black, 0.05)
        
        // Debug trend confirmation
        if enabledTrendCount > 0
            confirmMsg = 'TREND CONFIRMATION:'
            confirmMsg += ' Enabled=' + str.tostring(enabledTrendCount)
            confirmMsg += ' Bull=' + str.tostring(bullishTrendCount) + '/' + str.tostring(enabledTrendCount)
            confirmMsg += ' Bear=' + str.tostring(bearishTrendCount) + '/' + str.tostring(enabledTrendCount)
            confirmMsg += ' ConfirmLong=' + str.tostring(trendConfirmsLong)
            confirmMsg += ' ConfirmShort=' + str.tostring(trendConfirmsShort)
            debugMessage("INFO", confirmMsg, color.orange, color.white, 0.05)
    
    // Show new trend signal system status
    trendStatusMsg = 'TREND SIGNALS:'
    trendStatusMsg += ' T1=' + (trend1Enable ? (trend1Signal == 1 ? 'BULL' : trend1Signal == -1 ? 'BEAR' : 'NEUT') : 'OFF')
    trendStatusMsg += ' T2=' + (trend2Enable ? (trend2Signal == 1 ? 'BULL' : trend2Signal == -1 ? 'BEAR' : 'NEUT') : 'OFF')  
    trendStatusMsg += ' T3=' + (trend3Enable ? (trend3Signal == 1 ? 'BULL' : trend3Signal == -1 ? 'BEAR' : 'NEUT') : 'OFF')
    trendStatusMsg += ' RBW=' + (rbwEnable ? (rbwSignal == 2 ? 'BULL' : rbwSignal == 0 ? 'BEAR' : 'NEUT') : 'OFF')
    trendStatusMsg += ' BB=' + (bbEntryFilterEnable ? ('L:' + (bbLongFilterOK ? 'OK' : 'BLOCK') + ' S:' + (bbShortFilterOK ? 'OK' : 'BLOCK')) : 'OFF')
    debugMessage("INFO", trendStatusMsg, color.green, color.white, 0.05)
    
    // Show simplified bias status (RBW only now)
    consensusMsg = 'BIAS STATUS: RBW-Only Filter | RBW=' + (rbwEnable ? (rbwSignal == 2 ? 'BULL' : rbwSignal == 0 ? 'BEAR' : 'NEUT') : 'OFF')
    consensusMsg += ' | Final: Long=' + (longDirectionalBias ? 'ALLOW' : 'BLOCK') + ' Short=' + (shortDirectionalBias ? 'ALLOW' : 'BLOCK')
    debugMessage("INFO", consensusMsg, color.green, color.white, 0.05)
    
    // Show trend change exit system status
    if trendChangeExitEnable
        trendChangeMsg = 'TREND CHANGE EXIT: Status=' + (trendChangeExitEnable ? 'ENABLED' : 'DISABLED')
        trendChangeMsg += ' | Active=' + (trendChangeExitActive ? 'YES' : 'NO')  
        if trendChangeDetected
            trendChangeMsg += ' | CHANGE DETECTED: ' + trendChangeDetails
        debugMessage("INFO", trendChangeMsg, color.purple, color.white, 0.05)
    
    // Show hybrid exit system status
    if hybridExitEnable
        hybridMsg = 'HYBRID EXIT: Active=' + (hybridExitActive ? 'YES' : 'NO')
        hybridMsg += ' | Position=' + str.tostring(currentPositionSize) + '/' + str.tostring(hybridSwitchThreshold)
        if hybridExitActive
            hybridMsg += ' | Mode=' + (hybridUsingSPL ? 'SMART_PROFIT_LOCKER' : hybridUsingTCE ? 'TREND_CHANGE_EXIT' : 'NONE')
        hybridMsg += ' | shouldRunSPL=' + str.tostring(shouldRunSPL)
        debugMessage("INFO", hybridMsg, color.yellow, color.black, 0.05)

// Debug logging for exit systems (trend-riding system removed)
if debugEnabled and strategy.position_size != 0
    systemMsg = 'EXIT SYSTEMS:'
    systemMsg += ' SPL=' + (shouldRunSPL ? 'ON' : 'OFF')
    systemMsg += ' TCE=' + (trendChangeExitActive ? 'ON' : 'OFF')
    systemMsg += ' HYB=' + (hybridExitActive ? 'ON' : 'OFF')
    debugMessage("INFO", systemMsg, color.blue, color.white, 0.25)

// Enhanced visual indicators for exit systems  
plotchar(shouldRunSPL, title='Smart Profit Locker', char='🔒', location=location.top, color=color.blue, size=size.small)
plotchar(trendChangeExitEnable and strategy.position_size != 0, title='Trend Change Exit Active', char='📈', location=location.top, color=color.green, size=size.small)




// ──────── REMOVED: CUSTOM EXIT SYSTEM ────────────
// This old custom exit logic was interfering with the trend/hybrid exit system
// All custom exit functionality has been removed to prevent conflicts

// Visual aids for active levels
plot(strategy.position_size > 0 and fixedEnable ? strategyEntryPrice - tpCalc(fixedStop) : na, 'Fixed SL', color.red, style=plot.style_linebr)
plot(strategy.position_size > 0 and fixedEnable and tp1Enable ? strategyEntryPrice + tpCalc(tp1Size) : na, 'Fixed TP', color.green, style=plot.style_linebr)

// ─────────────────── 8 · ENHANCED CHART VISUALS WITH BACKTESTING INTEGRATION ─────────────────────────
// Entry signals - simple visualization
// Removed: Static BUY/SELL arrows - replaced with dynamic signal naming above

// Signal count indicators (for visual reference only - position size determines strategy)
// Removed confluence plots - using position size for trade management instead

// ──────── OPTIMIZED SIGNAL PLOTTING (Consolidated) ────────────
// Removed consolidated signal markers - redundant with main BUY/SELL triangles
// This reduces visual clutter and eliminates duplicate signal indicators

// ═══════════════════ COMPREHENSIVE INDICATOR VISUALIZATION ═══════════════════
// Beautiful, human-readable plotting of all strategy components
// ──────── CORE STRATEGY INDICATORS ────────────
// Moving Average Exit Line (Thick, Color-Coded)
maExitMA = ta.ema(close, 21)  // Using EMA-21 as the primary MA exit
plot(maExitMA, title="📈 MA Exit Line", color=color.new(color.blue, 0), linewidth=3, display=display.all)

// Stop Loss and Profit Lines (Dynamic based on position)
stopLossLine = strategy.position_size > 0 ? strategyEntryPrice - (atrVal * 3.1) : 
               strategy.position_size < 0 ? strategyEntryPrice + (atrVal * 3.1) : na
profitLockerLine = strategy.position_size > 0 ? high - (atrVal * smartProfitVal * smartProfitOffset) :
                   strategy.position_size < 0 ? low + (atrVal * smartProfitVal * smartProfitOffset) : na

plot(stopLossLine, title="🛑 Stop Loss", color=color.new(color.red, 20), linewidth=2, style=plot.style_linebr)
plot(profitLockerLine, title="💰 Profit Locker", color=color.new(color.green, 20), linewidth=2, style=plot.style_linebr)

// ═══════════════════ PROFESSIONAL STRATEGY ANALYTICS PANEL ═══════════════════
// Strategy-based performance analytics with professional UI design
if showBacktestTable and barstate.isconfirmed
    // Create compact analytics table with responsive design (expanded for 10 signals)
    var table strategyAnalytics = table.new(position.bottom_left, 5, 12, bgcolor = color.new(color.black, 5), border_width = 1)

    // Compact headers with better sizing
    table.cell(strategyAnalytics, 0, 0, '📊 SIGNALS', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 15))
    table.cell(strategyAnalytics, 1, 0, 'Win%', text_color = color.white, text_size = size.small, bgcolor = color.new(color.green, 15))
    table.cell(strategyAnalytics, 2, 0, 'P&L', text_color = color.white, text_size = size.small, bgcolor = color.new(color.green, 15))
    table.cell(strategyAnalytics, 3, 0, 'DD', text_color = color.white, text_size = size.small, bgcolor = color.new(color.red, 15))
    table.cell(strategyAnalytics, 4, 0, '#', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 15))

    // Global analytics panel variables (declared to avoid scope issues)
    var string health_emoji = '⚪'
    var color health_color = color.gray
    var string action_text = 'WAIT'
    var color action_color = color.gray
    
    // Signal 1 - Individual Signal Analytics (Fixed)
    if signal1Enable
        // Use individual signal tracking instead of combined strategy stats
        signal_trades = array.get(signal_tradeCounts, 0)
        signal_wins = array.get(signal_winCounts, 0)
        signal_pnl = array.get(signal_profits, 0)  // Individual signal P&L
        signal_max_dd = array.get(signal_strategy_drawdowns, 0)  // Individual signal drawdown
        signal_contrib = array.get(signal_contributions, 0)
        
        // Calculate individual signal metrics
        signal_winrate = signal_trades > 0 ? signal_wins / signal_trades * 100 : 0
        
        // Convert to dollar amounts using configurable futures multiplier
        signal_pnl_dollars = signal_pnl * futuresMultiplier
        signal_max_dd_dollars = signal_max_dd * futuresMultiplier
        
        // Determine health status (traffic light system) - using dollar amounts for futures
        health_emoji := '🟢'  // Green = Healthy
        health_color := color.lime
        if signal_pnl_dollars < 0 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 50
            health_emoji := '🔴'  // Red = Unhealthy
            health_color := color.red
        else if signal_pnl_dollars < 200 or math.abs(signal_max_dd_dollars) > 300 or signal_winrate < 65
            health_emoji := '🟡'  // Yellow = Caution
            health_color := color.yellow
        
        // Determine Keep/Cut recommendation - using dollar amounts for futures
        action_text := 'KEEP ✅'
        action_color := color.lime
        if signal_pnl_dollars < -100 or math.abs(signal_max_dd_dollars) > 800 or (signal_trades > 5 and signal_winrate < 40)
            action_text := 'CUT ❌'
            action_color := color.red
        else if signal_pnl_dollars < 100 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 55
            action_text := 'TEST ⚠️'
            action_color := color.orange
        
        // Compact table cells with dollar amounts displayed
        table.cell(strategyAnalytics, 0, 1, str.substring(signal1Name, 0, 6), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 70))
        table.cell(strategyAnalytics, 1, 1, str.tostring(signal_winrate, '#') + '%', text_color = signal_winrate >= 65 ? color.lime : signal_winrate >= 50 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 2, 1, '$' + str.tostring(signal_pnl_dollars, '#'), text_color = signal_pnl_dollars > 0 ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 3, 1, '$' + str.tostring(math.abs(signal_max_dd_dollars), '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.red, 70))
        table.cell(strategyAnalytics, 4, 1, str.tostring(signal_trades, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))

    // Signal 2 - Individual Signal Analytics (Fixed)
    if signal2Enable
        signal_trades = array.get(signal_tradeCounts, 1)
        signal_wins = array.get(signal_winCounts, 1)
        signal_pnl = array.get(signal_profits, 1)
        signal_max_dd = array.get(signal_strategy_drawdowns, 1)
        signal_winrate = signal_trades > 0 ? signal_wins / signal_trades * 100 : 0
        
        // Convert to dollar amounts using configurable futures multiplier
        signal_pnl_dollars = signal_pnl * futuresMultiplier
        signal_max_dd_dollars = signal_max_dd * futuresMultiplier
        
        health_emoji := signal_pnl_dollars < 0 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 50 ? '🔴' : signal_pnl_dollars < 200 or math.abs(signal_max_dd_dollars) > 300 or signal_winrate < 65 ? '🟡' : '🟢'
        health_color := signal_pnl_dollars < 0 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 50 ? color.red : signal_pnl_dollars < 200 or math.abs(signal_max_dd_dollars) > 300 or signal_winrate < 65 ? color.yellow : color.lime
        
        action_text := signal_pnl_dollars < -100 or math.abs(signal_max_dd_dollars) > 800 or (signal_trades > 5 and signal_winrate < 40) ? 'CUT ❌' : signal_pnl_dollars < 100 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 55 ? 'TEST ⚠️' : 'KEEP ✅'
        action_color := signal_pnl_dollars < -100 or math.abs(signal_max_dd_dollars) > 800 or (signal_trades > 5 and signal_winrate < 40) ? color.red : signal_pnl_dollars < 100 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 55 ? color.orange : color.lime
        
        table.cell(strategyAnalytics, 0, 2, str.substring(signal2Name, 0, 6), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 70))
        table.cell(strategyAnalytics, 1, 2, str.tostring(signal_winrate, '#') + '%', text_color = signal_winrate >= 65 ? color.lime : signal_winrate >= 50 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 2, 2, '$' + str.tostring(signal_pnl_dollars, '#'), text_color = signal_pnl_dollars > 0 ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 3, 2, '$' + str.tostring(math.abs(signal_max_dd_dollars), '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.red, 70))
        table.cell(strategyAnalytics, 4, 2, str.tostring(signal_trades, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))

    // Signals 3-10 - Individual Signal Analytics (Fixed and Expanded)
    signal_names = array.from(signal3Name, signal4Name, signal5Name, signal6Name, signal7Name, signal8Name, signal9Name, signal10Name)
    signal_enables = array.from(signal3Enable, signal4Enable, signal5Enable, signal6Enable, signal7Enable, signal8Enable, signal9Enable, signal10Enable)
    
    for i = 2 to 9
        if array.get(signal_enables, i - 2)
            signal_trades = array.get(signal_tradeCounts, i)
            signal_wins = array.get(signal_winCounts, i)
            signal_pnl = array.get(signal_profits, i)
            signal_max_dd = array.get(signal_strategy_drawdowns, i)
            signal_winrate = signal_trades > 0 ? signal_wins / signal_trades * 100 : 0
            
            // Convert to dollar amounts using configurable futures multiplier
            signal_pnl_dollars = signal_pnl * futuresMultiplier
            signal_max_dd_dollars = signal_max_dd * futuresMultiplier
            
            health_emoji := signal_pnl_dollars < 0 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 50 ? '🔴' : signal_pnl_dollars < 200 or math.abs(signal_max_dd_dollars) > 300 or signal_winrate < 65 ? '🟡' : '🟢'
            health_color := signal_pnl_dollars < 0 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 50 ? color.red : signal_pnl_dollars < 200 or math.abs(signal_max_dd_dollars) > 300 or signal_winrate < 65 ? color.yellow : color.lime
            
            action_text := signal_pnl_dollars < -100 or math.abs(signal_max_dd_dollars) > 800 or (signal_trades > 5 and signal_winrate < 40) ? 'CUT ❌' : signal_pnl_dollars < 100 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 55 ? 'TEST ⚠️' : 'KEEP ✅'
            action_color := signal_pnl_dollars < -100 or math.abs(signal_max_dd_dollars) > 800 or (signal_trades > 5 and signal_winrate < 40) ? color.red : signal_pnl_dollars < 100 or math.abs(signal_max_dd_dollars) > 500 or signal_winrate < 55 ? color.orange : color.lime
            
            table.cell(strategyAnalytics, 0, i + 1, str.substring(array.get(signal_names, i - 2), 0, 6), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 70))
            table.cell(strategyAnalytics, 1, i + 1, str.tostring(signal_winrate, '#') + '%', text_color = signal_winrate >= 65 ? color.lime : signal_winrate >= 50 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
            table.cell(strategyAnalytics, 2, i + 1, '$' + str.tostring(signal_pnl_dollars, '#'), text_color = signal_pnl_dollars > 0 ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.gray, 80))
            table.cell(strategyAnalytics, 3, i + 1, '$' + str.tostring(math.abs(signal_max_dd_dollars), '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.red, 70))
            table.cell(strategyAnalytics, 4, i + 1, str.tostring(signal_trades, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))

    // Compact Strategy Features Summary (moved to row 11 for 10 signals)
    table.cell(strategyAnalytics, 0, 11, '🚀 FEATURES', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 25))
    table.cell(strategyAnalytics, 1, 11, 'BB:' + (bbEntryFilterEnable ? 'ON' : 'OFF'), text_color = bbEntryFilterEnable ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.blue, 35))
    hybridFeatureDisplay = hybridExitEnable ? 'HYB:ON' : trendChangeExitEnable ? 'TCE:ON' : 'TCE:OFF'
    hybridFeatureColor = hybridExitEnable ? color.yellow : trendChangeExitEnable ? color.lime : color.red
    table.cell(strategyAnalytics, 2, 11, hybridFeatureDisplay, text_color = hybridFeatureColor, text_size = size.small, bgcolor = color.new(color.blue, 35))
    rbwStatusDisplay = rbwEnable ? 'RBW:ON' : 'RBW:OFF'
    rbwStatusColor = rbwEnable ? color.lime : color.red
    table.cell(strategyAnalytics, 3, 11, rbwStatusDisplay, text_color = rbwStatusColor, text_size = size.small, bgcolor = color.new(color.blue, 35))
    exitModeDisplay = hybridExitActive ? (hybridUsingSPL ? 'H-SPL' : hybridUsingTCE ? 'H-TCE' : 'HYB') : trendChangeExitActive ? 'TCE' : 'NOR'
    exitModeColor = hybridExitActive ? (hybridUsingSPL ? color.blue : hybridUsingTCE ? color.green : color.yellow) : trendChangeExitActive ? color.green : color.white
    table.cell(strategyAnalytics, 4, 11, exitModeDisplay, text_color = exitModeColor, text_size = size.small, bgcolor = color.new(color.blue, 35))

// ═══════════════════ INTRABAR EXIT DEBUG SYSTEM ═══════════════════
// Visual monitoring and validation system for robust exit logic

debugOn = input.bool(false, '🔍 Enable Exit Debug Labels', group = '🛠️ Debug System', tooltip = 'Show visual labels when exits trigger to validate anti-spam logic')

if debugOn and barstate.isconfirmed
    // Determine which exit method was triggered (if any)
    exitType = 
      maExitSent ? 'MA' :
      fixedExitSent ? 'Fixed' :
      trailExitSent ? 'Trail' :
      // Removed: customExitSent ? 'Custom' : - old custom exit system
      'None'
    
    // Color coding for different exit types
    labelColor = 
      maExitSent ? color.red : 
      fixedExitSent ? color.orange : 
      trailExitSent ? color.blue : 
      // Removed: customExitSent ? color.teal : - old custom exit system
      color.gray
    
    // Show debug label when an exit is triggered
    if exitType != 'None'
        label.new(bar_index, high * 1.02, 'EXIT: ' + exitType + '\nBar: ' + str.tostring(bar_index) + '\nPrice: ' + str.tostring(close, '#.####') + '\nPos: ' + (strategy.position_size > 0 ? 'Long' : strategy.position_size < 0 ? 'Short' : 'Flat'), color=labelColor, textcolor=color.white, style=label.style_label_down, yloc=yloc.abovebar, size=size.small)
    
    // Show position state changes
    if currentPosition != currentPosition[1]
        stateColor = currentPosition ? color.green : color.red
        stateText = currentPosition ? 'ENTRY' : 'EXIT'
        label.new(bar_index, low * 0.98, stateText + '\nFlags Reset: ' + (currentPosition ? 'YES' : 'NO'), color=stateColor, textcolor=color.white, style=label.style_label_up, yloc=yloc.belowbar, size=size.tiny)

// ──────── STATUS/SETTINGS PANEL (Optional) ────────────
showStatusPanel = input.bool(false, '⚙️ Status/Settings Panel', group = '🛠️ Debug System', tooltip = 'Show comprehensive status and settings for all enabled features')

if showStatusPanel and barstate.isconfirmed
    // Create fixed-size table (3 columns, 8 rows max)  
    var table statusTable = table.new(position.bottom_right, 3, 8, bgcolor = color.new(color.black, 20), border_width = 1)
    
    // Headers
    table.cell(statusTable, 0, 0, '⚙️ FEATURE', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    table.cell(statusTable, 1, 0, 'STATUS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    table.cell(statusTable, 2, 0, 'SETTINGS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    
    // Dynamic row counter
    var int currentRow = 1
    currentRow := 1
    
    // MA Exit
    if maExitOn
        maStatus = inPosition and (strategy.position_size > 0 and close < priceMA) or (strategy.position_size < 0 and close > priceMA) ? '🔴 TRIGGERED' : inPosition ? '🟡 MONITORING' : '⚪ STANDBY'
        maStatusColor = inPosition and ((strategy.position_size > 0 and close < priceMA) or (strategy.position_size < 0 and close > priceMA)) ? color.red : inPosition ? color.yellow : color.gray
        maSettings = maType + '-' + str.tostring(maLen)
        
        table.cell(statusTable, 0, currentRow, '📈 MA Exit', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, maStatus, text_color = maStatusColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, maSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Fixed SL/TP
    if fixedEnable
        fixedStatus = inPosition ? '🟡 ACTIVE' : '⚪ STANDBY'
        fixedStatusColor = inPosition ? color.yellow : color.gray
        fixedSettings = 'SL:' + str.tostring(fixedStop, '#.1') + ' ' + fixedUnit + ' TP:' + (tp1Enable ? str.tostring(tp1Size, '#.1') : 'OFF')
        
        table.cell(statusTable, 0, currentRow, '🎯 Fixed SL/TP', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, fixedStatus, text_color = fixedStatusColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, fixedSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Smart Profit Locker
    if smartProfitEnable
        smartStatus = inPosition ? '🟢 ACTIVE' : '⚪ STANDBY'
        smartStatusColor = inPosition ? color.lime : color.gray
        smartSettings = str.tostring(smartProfitVal, '#.1') + ' ' + smartProfitType + ', ' + str.tostring(smartProfitOffset * 100, '#.1') + '% PB'
        
        table.cell(statusTable, 0, currentRow, '🔒 Smart Locker', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, smartStatus, text_color = smartStatusColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, smartSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Trend Change Exit
    if trendChangeExitEnable
        trendChangeStatus = trendChangeExitActive ? '🟢 ACTIVE' : inPosition ? '🟡 MONITORING' : '⚪ STANDBY'
        trendChangeColor = trendChangeExitActive ? color.lime : inPosition ? color.yellow : color.gray
        trendChangeSettings = 'Any trend signal direction change'
        
        table.cell(statusTable, 0, currentRow, '📈 Trend Exit', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, trendChangeStatus, text_color = trendChangeColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, trendChangeSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Hybrid Exit System
    if hybridExitEnable
        hybridStatus = hybridExitActive ? (hybridUsingSPL ? '🔵 SPL MODE' : hybridUsingTCE ? '🟢 TCE MODE' : '🟡 ACTIVE') : '⚪ STANDBY'
        hybridColor = hybridExitActive ? (hybridUsingSPL ? color.blue : hybridUsingTCE ? color.green : color.yellow) : color.gray
        hybridSettings = 'Switch at ' + str.tostring(hybridSwitchThreshold) + ' contracts'
        
        table.cell(statusTable, 0, currentRow, '⚡ Hybrid Exit', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, hybridStatus, text_color = hybridColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, hybridSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // System Status Row  
    table.cell(statusTable, 0, currentRow, '📊 Position', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(statusTable, 1, currentRow, inPosition ? '🟢 IN TRADE' : '⚪ NO POSITION', text_color = inPosition ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.purple, 70))
    table.cell(statusTable, 2, currentRow, 'Size: ' + str.tostring(strategy.position_size), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 70))

// ═══════════════════ MASTER PANEL SYSTEM ═══════════════════
// Real-time strategy status panel for enhanced transparency

// Master Panel Enable Toggle
masterPanelEnable = input.bool(true, '📊 Master Panel', group='🎛️ Master Panel', tooltip='Show real-time strategy status panel with key metrics')
bigPositionDisplay = input.bool(true, '💯 Big Position Display', group='🎛️ Master Panel', tooltip='Show large, bold position indicator for instant recognition')

// Big Bold Position Display (Optimized for immediate loading)
if bigPositionDisplay and barstate.isconfirmed
    var label bigPositionLabel = na
    
    // Delete previous label
    if not na(bigPositionLabel)
        label.delete(bigPositionLabel)
    
    // Create new position label
    if strategy.position_size != 0
        positionText = strategy.position_size > 0 ? 'LONG' : 'SHORT'
        positionColor = strategy.position_size > 0 ? color.new(color.lime, 20) : color.new(color.red, 20)
        textColor = strategy.position_size > 0 ? color.lime : color.red
        
        bigPositionLabel := label.new(x = bar_index + 5, y = high + (high - low) * 0.3, text = positionText, style = label.style_label_left, color = positionColor, textcolor = textColor, size = size.huge)

// Session Stats Tracking
var float sessionStartEquity = na
var int sessionTrades = 0
var int sessionWins = 0

// Initialize session stats at start of day
if na(sessionStartEquity) or dayofweek != dayofweek[1]
    sessionStartEquity := strategy.equity
    sessionTrades := 0
    sessionWins := 0

// Track session trades
if strategy.closedtrades > strategy.closedtrades[1]
    sessionTrades := sessionTrades + 1
    if strategy.wintrades > strategy.wintrades[1]
        sessionWins := sessionWins + 1

// Simplified bias calculation (RBW only)
// Removed complex trend consensus logic - using position size for strategy control

// Master Panel Display (Optimized for immediate loading)
if masterPanelEnable and barstate.isconfirmed
    var table masterPanel = table.new(position.top_right, 2, 15, bgcolor = color.new(color.black, 15), border_width = 1)
    
    // Header
    table.cell(masterPanel, 0, 0, '🎛️ MASTER PANEL', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 30))
    table.cell(masterPanel, 1, 0, 'STATUS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 30))
    

    
    // Bias Filter Status (Simplified - RBW Only)
    biasText = rbwEnable ? 
      (longDirectionalBias ? 'BULLISH' : shortDirectionalBias ? 'BEARISH' : 'NEUTRAL') : 'DISABLED'
    biasColor = longDirectionalBias ? color.lime : shortDirectionalBias ? color.red : color.gray
    table.cell(masterPanel, 0, 2, '📈 RBW Bias', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(masterPanel, 1, 2, biasText, text_color = biasColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Signal Strength Meter Row (expanded to 10 signals)
    activeSignals = (signal1Enable ? 1 : 0) + (signal2Enable ? 1 : 0) + (signal3Enable ? 1 : 0) + (signal4Enable ? 1 : 0) + (signal5Enable ? 1 : 0) + (signal6Enable ? 1 : 0) + (signal7Enable ? 1 : 0) + (signal8Enable ? 1 : 0) + (signal9Enable ? 1 : 0) + (signal10Enable ? 1 : 0)
    longSignalCount := (signal1Enable and sig1Long ? 1 : 0) + (signal2Enable and sig2Long ? 1 : 0) + (signal3Enable and sig3Long ? 1 : 0) + (signal4Enable and sig4Long ? 1 : 0) + (signal5Enable and sig5Long ? 1 : 0) + (signal6Enable and sig6Long ? 1 : 0) + (signal7Enable and sig7Long ? 1 : 0) + (signal8Enable and sig8Long ? 1 : 0) + (signal9Enable and sig9Long ? 1 : 0) + (signal10Enable and sig10Long ? 1 : 0)
    shortSignalCount := (signal1Enable and sig1Short ? 1 : 0) + (signal2Enable and sig2Short ? 1 : 0) + (signal3Enable and sig3Short ? 1 : 0) + (signal4Enable and sig4Short ? 1 : 0) + (signal5Enable and sig5Short ? 1 : 0) + (signal6Enable and sig6Short ? 1 : 0) + (signal7Enable and sig7Short ? 1 : 0) + (signal8Enable and sig8Short ? 1 : 0) + (signal9Enable and sig9Short ? 1 : 0) + (signal10Enable and sig10Short ? 1 : 0)
    
    if activeSignals > 0
        maxSignals = math.max(longSignalCount, shortSignalCount)
        signalStrength = maxSignals / activeSignals * 100
        strengthText = str.tostring(signalStrength, '#') + '% (' + str.tostring(maxSignals) + '/' + str.tostring(activeSignals) + ')'
        strengthColor = signalStrength >= 80 ? color.lime : signalStrength >= 60 ? color.yellow : signalStrength >= 40 ? color.orange : color.red
        table.cell(masterPanel, 0, 3, '📊 Signal Power', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 3, strengthText, text_color = strengthColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Volatility Alert Row
    atrCurrent = atrVal
    // Use pre-calculated atrAvg instead of inline ta.sma calculation
    volatilityRatio := atrCurrent / atrAvg
    
    if volatilityRatio > 1.5  // Show alert when ATR is 50% above average
        volText = 'HIGH (' + str.tostring(volatilityRatio, '#.##') + 'x)'
        volColor = volatilityRatio > 2.0 ? color.red : color.orange
        table.cell(masterPanel, 0, 4, '⚡ Volatility', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 4, volText, text_color = volColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Mode Status Row
    if hybridExitEnable
        modeText = ''
        modeColor = color.gray
        
        if hybridExitEnable and hybridExitActive
            modeText := 'HYBRID'
            modeColor := color.orange
        else
            modeText := 'NORMAL'
            modeColor := color.gray
            
        table.cell(masterPanel, 0, 5, '🚀 Mode', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 5, modeText, text_color = modeColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    

    
    // Exit System Status Row - NEW COMPREHENSIVE TRACKING
    if strategy.position_size != 0
        exitSystemText = ''
        exitSystemColor = color.gray
        
        // Determine which exit system is currently active
        if bbExitTriggered
            // BB EXIT MODE: Tight profit locker from BB extreme
            exitSystemText := 'BB TIGHT'
            exitSystemColor := color.yellow
        else if smartProfitEnable and shouldRunSPL
            // NORMAL MODE: Smart profit locker active
            exitSystemText := 'SMART LOCKER'
            exitSystemColor := color.lime
        else if maExitOn
            // MA EXIT MODE: Moving average exit active
            exitSystemText := 'MA EXIT'
            exitSystemColor := color.purple
        else if fixedEnable
            // FIXED MODE: Fixed SL/TP active
            exitSystemText := 'FIXED SL/TP'
            exitSystemColor := color.blue
        else
            // NO EXIT SYSTEM: This shouldn't happen but safety check
            exitSystemText := 'NO EXIT!'
            exitSystemColor := color.red
        
        table.cell(masterPanel, 0, 7, '🎯 Exit System', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 7, exitSystemText, text_color = exitSystemColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Position Status Row
    positionText = strategy.position_size == 0 ? 'FLAT' : strategy.position_size > 0 ? 'LONG' : 'SHORT'
    positionColor = strategy.position_size == 0 ? color.gray : strategy.position_size > 0 ? color.lime : color.red
    table.cell(masterPanel, 0, 8, '📈 Position', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(masterPanel, 1, 8, positionText, text_color = positionColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // P&L Row (only when in position)
    if strategy.position_size != 0
        unrealizedPnL = strategy.openprofit
        pnlText = (unrealizedPnL >= 0 ? '+$' : '-$') + str.tostring(math.abs(unrealizedPnL), '#.##')
        pnlColor = unrealizedPnL > 0 ? color.lime : unrealizedPnL < 0 ? color.red : color.gray
        table.cell(masterPanel, 0, 9, '💰 P&L', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 9, pnlText, text_color = pnlColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Trade Duration Row (only when in position)
    if strategy.position_size != 0
        barsInTrade = bar_index - strategy.opentrades.entry_bar_index(strategy.opentrades - 1)
        durationText = str.tostring(barsInTrade) + ' bars'
        durationColor = barsInTrade > 50 ? color.orange : color.white
        table.cell(masterPanel, 0, 10, '⏱️ Duration', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 10, durationText, text_color = durationColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Entry Price Row (only when in position)
    if strategy.position_size != 0
        float entryPrice = strategy.opentrades.entry_price(strategy.opentrades - 1)
        priceText = '$' + str.tostring(entryPrice, '#.##')
        priceDiff = close - entryPrice
        diffText = (priceDiff >= 0 ? '+' : '') + str.tostring(priceDiff, '#.##')
        table.cell(masterPanel, 0, 11, '🎯 Entry Price', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 11, priceText + ' (' + diffText + ')', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Risk/Reward Calculator Row (only when in position)
    if strategy.position_size != 0 and smartProfitEnable
        float entryPrice = strategy.opentrades.entry_price(strategy.opentrades - 1)
        stopDistance = smartProfitVal * atrVal  // Assuming ATR-based stop
        currentDistance = math.abs(close - entryPrice)
        riskRewardRatio = currentDistance / stopDistance
        rrText = str.tostring(riskRewardRatio, '#.##') + ':1'
        rrColor = riskRewardRatio >= 2.0 ? color.lime : riskRewardRatio >= 1.0 ? color.yellow : color.red
        table.cell(masterPanel, 0, 12, '🎲 R:R Ratio', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 12, rrText, text_color = rrColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Session Stats Row
    if sessionTrades > 0
        sessionPnL = strategy.equity - sessionStartEquity
        sessionWinRate = sessionWins / sessionTrades * 100
        sessionText = str.tostring(sessionWinRate, '#') + '% (' + str.tostring(sessionWins) + '/' + str.tostring(sessionTrades) + ')'
        sessionColor = sessionWinRate >= 70 ? color.lime : sessionWinRate >= 50 ? color.yellow : color.red
        table.cell(masterPanel, 0, 12, '🌅 Today', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 12, sessionText, text_color = sessionColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Next Signal Countdown Row (only when flat)
    if strategy.position_size == 0
        // Simple countdown based on bars since last signal
        barsWithoutSignal = 0
        if not (sig1Long or sig1Short or sig2Long or sig2Short or sig3Long or sig3Short or sig4Long or sig4Short or sig5Long or sig5Short)
            barsWithoutSignal := 1
        
        countdownText = ''
        countdownColor = color.gray
        
        if barsWithoutSignal > 0
            countdownText := 'Waiting...'
            countdownColor := color.gray
        else
            countdownText := 'Signal Active!'
            countdownColor := color.yellow
        
        table.cell(masterPanel, 0, 13, '⏳ Next Signal', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 13, countdownText, text_color = countdownColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Win Rate Row
    if strategy.closedtrades > 0
        winRate = strategy.wintrades / strategy.closedtrades * 100
        winText = str.tostring(winRate, '#.1') + '% (' + str.tostring(strategy.wintrades) + '/' + str.tostring(strategy.closedtrades) + ')'
        winColor = winRate >= 70 ? color.lime : winRate >= 50 ? color.yellow : color.red
        table.cell(masterPanel, 0, 14, '🏆 Win Rate', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 14, winText, text_color = winColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))

// ═══════════════════ STRATEGY PERFORMANCE TRACKING UPDATES ═══════════════════
// Update signal performance arrays when trades close

// Track peak/trough during open trades for drawdown calculation
if strategy.position_size != 0 and not na(current_trade_entry)
    if strategy.position_size > 0  // Long position
        current_trade_peak := math.max(current_trade_peak, close)
        current_trade_trough := math.min(current_trade_trough, close)
    else  // Short position
        current_trade_peak := math.max(current_trade_peak, current_trade_entry - close)
        current_trade_trough := math.min(current_trade_trough, current_trade_entry - close)

// Update performance arrays when trades close
if strategy.closedtrades > strategy.closedtrades[1]
    // Calculate trade P&L
    trade_pnl = strategy.position_size[1] > 0 ? (close - current_trade_entry) / current_trade_entry * 100 : (current_trade_entry - close) / current_trade_entry * 100
    trade_won = trade_pnl > 0
    
    // Calculate drawdown during this trade
    trade_drawdown = strategy.position_size[1] > 0 ? (current_trade_peak - current_trade_trough) / current_trade_entry * 100 : current_trade_peak
    
    // Update performance for each signal that contributed to this trade (expanded to 10 signals)
    for i = 0 to 9
        if array.get(current_trade_signals, i)
            // Update trade count
            current_trades = array.get(signal_strategy_trades, i)
            array.set(signal_strategy_trades, i, current_trades + 1)
            
            // Update win count
            if trade_won
                current_wins = array.get(signal_strategy_wins, i)
                array.set(signal_strategy_wins, i, current_wins + 1)
            
            // Update cumulative P&L
            current_profit = array.get(signal_strategy_profits, i)
            array.set(signal_strategy_profits, i, current_profit + trade_pnl)
            
            // Update max drawdown (track worst drawdown per signal)
            current_dd = array.get(signal_strategy_drawdowns, i)
            array.set(signal_strategy_drawdowns, i, math.max(current_dd, trade_drawdown))
            
            // Update contribution score (weighted by trade P&L)
            current_contrib = array.get(signal_contributions, i)
            array.set(signal_contributions, i, current_contrib + (trade_pnl * 0.1))  // Weighted contribution
    
    // Reset tracking variables (expanded to 10 signals)
    current_trade_entry := na
    current_trade_peak := na
    current_trade_trough := na
    for i = 0 to 9
        array.set(current_trade_signals, i, false)

// TREND SIGNAL-BASED EXIT DETECTION
// Check for opposite signals from the 5 Trend Signals (correct approach)

// Reset trend change detection each bar
trendChangeDetected := false
trendChangeDetails := ""

// OLD TREND CHANGE EXIT LOGIC - KEPT FOR BASIC COMPATIBILITY
// This is overridden by the Advanced Trend Exit System when enabled
if trendChangeExitEnable and strategy.position_size != 0 and not useAdvancedTrendExit
    // Basic fallback logic (simplified)
    if strategy.position_size > 0 and (trend1Short or trend2Short or trend3Short)
        trendChangeDetected := true
        trendChangeDetails := "Basic Trend Exit: Opposite signal detected"
    else if strategy.position_size < 0 and (trend1Long or trend2Long or trend3Long)
        trendChangeDetected := true
        trendChangeDetails := "Basic Trend Exit: Opposite signal detected"

// ═══════════════════ ADVANCED TREND EXIT LOGIC ═══════════════════
// 3-Phase Exit System: Trailing Stop → Color Change Exit → Re-Entry

// State variables for advanced trend exit (declared in global section)


// Reset state when no position
if strategy.position_size == 0
    inTrendExitMode := false
    inProfitMode := false
    trendExitPrice := na
    if not reEntryEnable
        waitingForReEntry := false
        exitDirection := 0

// Initialize trend exit mode on new position
if strategy.position_size != 0 and not inTrendExitMode and useAdvancedTrendExit
    inTrendExitMode := true
    inProfitMode := false
    trendExitPrice := strategy.position_avg_price
    waitingForReEntry := false
    exitDirection := 0

// Phase 1: Check if we should switch from trailing stop to profit mode
if inTrendExitMode and not inProfitMode and strategy.position_size != 0
    trailingStopDistance = initialTrailingStop * atrVal
    
    if strategy.position_size > 0
        // Long position: switch to profit mode when price is above entry + trailing stop distance
        if close >= trendExitPrice + trailingStopDistance
            inProfitMode := true
    else
        // Short position: switch to profit mode when price is below entry - trailing stop distance  
        if close <= trendExitPrice - trailingStopDistance
            inProfitMode := true

// Phase 2: Color Change Exit (when in profit mode)
colorChangeExit := false

if inTrendExitMode and inProfitMode and strategy.position_size != 0
    if exitMode == 'Color Change'
        if strategy.position_size > 0 and lrcBearColor  // Long position exits on bear color
            colorChangeExit := true
            exitDirection := 1
        else if strategy.position_size < 0 and lrcBullColor  // Short position exits on bull color
            colorChangeExit := true
            exitDirection := -1
    else if exitMode == 'MA Cross'
        if strategy.position_size > 0 and lrcBearMA  // Long position exits on bear MA cross
            colorChangeExit := true
            exitDirection := 1
        else if strategy.position_size < 0 and lrcBullMA  // Short position exits on bull MA cross
            colorChangeExit := true
            exitDirection := -1

// Phase 3: Re-Entry Logic
reEntrySignal := false

if reEntryEnable and waitingForReEntry and strategy.position_size == 0
    if exitDirection == 1  // Was long, look for bull signal to re-enter long
        if exitMode == 'Color Change' and lrcBullColor and trendConfirmsLong
            reEntrySignal := true
            waitingForReEntry := false
            exitDirection := 0
        else if exitMode == 'MA Cross' and lrcBullMA and trendConfirmsLong
            reEntrySignal := true
            waitingForReEntry := false
            exitDirection := 0
    else if exitDirection == -1  // Was short, look for bear signal to re-enter short
        if exitMode == 'Color Change' and lrcBearColor and trendConfirmsShort
            reEntrySignal := true
            waitingForReEntry := false
            exitDirection := 0
        else if exitMode == 'MA Cross' and lrcBearMA and trendConfirmsShort
            reEntrySignal := true
            waitingForReEntry := false
            exitDirection := 0

// Set waiting for re-entry when color change exit occurs
if colorChangeExit and reEntryEnable
    waitingForReEntry := true

// Set final trend change detection for compatibility with existing exit logic
if colorChangeExit
    trendChangeDetected := true
    trendChangeDetails := "LRC " + exitMode + " Exit"

// ═══════════════════ DYNAMIC SIGNAL NAMING FOR ARROWS ═══════════════════
// Create meaningful arrow labels showing which signal(s) triggered the entry

// Build dynamic signal names for long entries
longSignalName = ""
var int dynamicLongCount = 0
dynamicLongCount := 0
if longEntrySignal
    if sig1Long and signal1Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal1Name
        dynamicLongCount := dynamicLongCount + 1
    if sig2Long and signal2Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal2Name
        dynamicLongCount := dynamicLongCount + 1
    if sig3Long and signal3Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal3Name
        dynamicLongCount := dynamicLongCount + 1
    if sig4Long and signal4Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal4Name
        dynamicLongCount := dynamicLongCount + 1
    if sig5Long and signal5Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal5Name
        dynamicLongCount := dynamicLongCount + 1
    if sig6Long and signal6Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal6Name
        dynamicLongCount := dynamicLongCount + 1
    if sig7Long and signal7Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal7Name
        dynamicLongCount := dynamicLongCount + 1
    if sig8Long and signal8Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal8Name
        dynamicLongCount := dynamicLongCount + 1
    if sig9Long and signal9Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal9Name
        dynamicLongCount := dynamicLongCount + 1
    if sig10Long and signal10Enable
        longSignalName += (dynamicLongCount > 0 ? "+" : "") + signal10Name
        dynamicLongCount := dynamicLongCount + 1

// Build dynamic signal names for short entries
shortSignalName = ""
var int dynamicShortCount = 0
dynamicShortCount := 0
if shortEntrySignal
    if sig1Short and signal1Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal1Name
        dynamicShortCount := dynamicShortCount + 1
    if sig2Short and signal2Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal2Name
        dynamicShortCount := dynamicShortCount + 1
    if sig3Short and signal3Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal3Name
        dynamicShortCount := dynamicShortCount + 1
    if sig4Short and signal4Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal4Name
        dynamicShortCount := dynamicShortCount + 1
    if sig5Short and signal5Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal5Name
        dynamicShortCount := dynamicShortCount + 1
    if sig6Short and signal6Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal6Name
        dynamicShortCount := dynamicShortCount + 1
    if sig7Short and signal7Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal7Name
        dynamicShortCount := dynamicShortCount + 1
    if sig8Short and signal8Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal8Name
        dynamicShortCount := dynamicLongCount + 1
    if sig9Short and signal9Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal9Name
        dynamicShortCount := dynamicShortCount + 1
    if sig10Short and signal10Enable
        shortSignalName += (dynamicShortCount > 0 ? "+" : "") + signal10Name
        dynamicShortCount := dynamicShortCount + 1

// Create final arrow titles with signal names
dynamicLongText = longSignalName != "" ? longSignalName : "MULTI"
dynamicShortText = shortSignalName != "" ? shortSignalName : "MULTI"

// Plot arrows with static titles and use labels for dynamic signal names
plotshape(longEntrySignal, title = "BUY Signal", style = shape.triangleup, location = location.belowbar, color = color.lime, size = size.small)
plotshape(shortEntrySignal, title = "SELL Signal", style = shape.triangledown, location = location.abovebar, color = color.red, size = size.small)

// Add dynamic labels showing which signal(s) triggered the entry
if longEntrySignal
    label.new(bar_index, low - (atrVal * 0.5), dynamicLongText, color = color.new(color.lime, 20), style = label.style_label_up, textcolor = color.white, size = size.small)

if shortEntrySignal
    label.new(bar_index, high + (atrVal * 0.5), dynamicShortText, color = color.new(color.red, 20), style = label.style_label_down, textcolor = color.white, size = size.small)

// ─────────────────── 0 · POSITION SIZE & PRIMARY SIGNALS ───────────────────
// Position Size Control
positionQty = input.int(1, 'Number of Contracts', minval = 1, maxval = 1000, group = 'Position Size', tooltip = 'Set the number of contracts/shares to trade per signal')
```