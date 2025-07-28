// 2025 Andres Garcia — WARNING WARNING WARNING
//  ────────────────────────────────────────────────────────────────
//  CORRUPTED Enhanced Multi-Signal Risk Management System
//  • Professional risk management with multiple exit strategies
//  • TradersPost webhook integration for automated trading
//  • Configurable position sizing and stop-loss/take-profit levels
//  • Integrated debugging logger for development
//      NOTES FROM LEAD: THIS VERSION IS CORRUPTED BUT HAS SOME ADVANCED FEATURES DO NOT TRUST THE TRADING LOGIC OR IMPLEMENTATION OF THE STRATEGY. 
//  ────────────────────────────────────────────────────────────────
//@version=6

// DEBUG SYSTEM
// Simple debug system using Pine Script labels - no external libraries

// Debug configuration
bool debugEnabled = input.bool(false, '🔍 Enable Debug Labels', group = '🛠️ Debug System', tooltip = 'Show debug information as chart labels')

// Combined debug function
debugMessage(string type, string message, color bgColor, color txtColor, float yOffset) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * yOffset, type + ": " + message, style=label.style_label_down, color=bgColor, textcolor=txtColor, size=size.small)

strategy(title = 'HIS VERSION IS CORRUPTED BUT HAS SOME ADVANCED FEATURES DO NOT TRUST THE TRADING LOGIC OR IMPLEMENTATION OF THE STRATEGY.)', overlay = true, default_qty_type = strategy.fixed, default_qty_value = 1, calc_on_order_fills = true, process_orders_on_close = true, calc_on_every_tick = false)
// User-controllable quantity

// ═══════════════════ GLOBAL VARIABLE DECLARATIONS ═══════════════════
// Declare all variables early to avoid scope issues

// Trend-riding system variables
var int trendRidingBarsActive = 0
var bool inTrendRidingMode = false
var int trendRidingStartBar = 0

// Exit system variables
var float smartOffset = na
var string exitComment = na
var float smartDistance = na
var string exitReason = na

// Hybrid Exit Mode Variables
var bool inHybridExitMode = false
var int hybridModeStartBar = 0

// Strategy Performance Tracking Variables
var array<float> signal_strategy_profits = array.new<float>(5, 0.0)
var array<float> signal_strategy_drawdowns = array.new<float>(5, 0.0)
var array<int> signal_strategy_trades = array.new<int>(5, 0)
var array<int> signal_strategy_wins = array.new<int>(5, 0)
var array<float> signal_contributions = array.new<float>(5, 0.0)
var array<bool> current_trade_signals = array.new<bool>(5, false)
var float current_trade_entry = na
var float current_trade_peak = na
var float current_trade_trough = na

// Bollinger Band Filter Variables
var bool bbLongFilterOK = true
var bool bbShortFilterOK = true

// Volatility and technical variables
var float volatilityRatio = na

// Entry signals
var bool longEntrySignal = false
var bool shortEntrySignal = false

// ATR and other technical variables
// ATR is calculated using user-configurable atrLen in the ATR Settings section

// ─────────────────── 0 · POSITION SIZE & PRIMARY SIGNALS ───────────────────
// Position Size Control
positionQty = input.int(1, 'Number of Contracts', minval = 1, maxval = 1000, group = 'Position Size', tooltip = 'Set the number of contracts/shares to trade per signal')

// ═══════════════════ MULTI-SIGNAL INPUT SYSTEM ═══════════════════


// ─────────────────── SIGNAL SOURCE INPUTS ───────────────────
signal1Enable = input.bool(true, '📊 Signal 1', inline = 'sig1', group = '🔄 Multi-Signals', tooltip = 'Primary signal source')
signal1LongSrc = input.source(close, 'Long', inline = 'sig1', group = '🔄 Multi-Signals')
signal1ShortSrc = input.source(close, 'Short', inline = 'sig1', group = '🔄 Multi-Signals')
signal1Name = input.string('LuxAlgo', 'Name', inline = 'sig1name', group = '🔄 Multi-Signals')
signal1Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig1name', group = '🔄 Multi-Signals')

signal2Enable = input.bool(false, '📊 Signal 2', inline = 'sig2', group = '🔄 Multi-Signals')
signal2LongSrc = input.source(close, 'Long', inline = 'sig2', group = '🔄 Multi-Signals')
signal2ShortSrc = input.source(close, 'Short', inline = 'sig2', group = '🔄 Multi-Signals')
signal2Name = input.string('UTBot', 'Name', inline = 'sig2name', group = '🔄 Multi-Signals')
signal2Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig2name', group = '🔄 Multi-Signals')

signal3Enable = input.bool(false, '📊 Signal 3', inline = 'sig3', group = '🔄 Multi-Signals')
signal3LongSrc = input.source(close, 'Long', inline = 'sig3', group = '🔄 Multi-Signals')
signal3ShortSrc = input.source(close, 'Short', inline = 'sig3', group = '🔄 Multi-Signals')
signal3Name = input.string('VIDYA', 'Name', inline = 'sig3name', group = '🔄 Multi-Signals')
signal3Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig3name', group = '🔄 Multi-Signals')

signal4Enable = input.bool(false, '📊 Signal 4', inline = 'sig4', group = '🔄 Multi-Signals')
signal4LongSrc = input.source(close, 'Long', inline = 'sig4', group = '🔄 Multi-Signals')
signal4ShortSrc = input.source(close, 'Short', inline = 'sig4', group = '🔄 Multi-Signals')
signal4Name = input.string('KyleAlgo', 'Name', inline = 'sig4name', group = '🔄 Multi-Signals')
signal4Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig4name', group = '🔄 Multi-Signals')

signal5Enable = input.bool(false, '📊 Signal 5', inline = 'sig5', group = '🔄 Multi-Signals')
signal5LongSrc = input.source(close, 'Long', inline = 'sig5', group = '🔄 Multi-Signals')
signal5ShortSrc = input.source(close, 'Short', inline = 'sig5', group = '🔄 Multi-Signals')
signal5Name = input.string('Wonder', 'Name', inline = 'sig5name', group = '🔄 Multi-Signals')
signal5Usage = input.string('Entry All', 'Usage', options = ['Entry All', 'Entry Long Only', 'Entry Short Only', 'Exit All', 'Exit Long Only', 'Exit Short Only', 'Observe'], inline = 'sig5name', group = '🔄 Multi-Signals')

// ─────────────────── SIGNAL PROCESSING ───────────────────
// Process signals directly - most indicators (UTBot, LuxAlgo) output pulse signals
// Handle both boolean and numeric signal types
// IMPORTANT: Only process real signals, not default 'close' values
// Simple direct signal detection - no complex change detection needed
sig1Long = signal1Enable and signal1LongSrc != close ? (signal1LongSrc > 0 or bool(signal1LongSrc) == true) : false
sig1Short = signal1Enable and signal1ShortSrc != close ? (signal1ShortSrc > 0 or bool(signal1ShortSrc) == true) : false
sig2Long = signal2Enable and signal2LongSrc != close ? (signal2LongSrc > 0 or bool(signal2LongSrc) == true) : false
sig2Short = signal2Enable and signal2ShortSrc != close ? (signal2ShortSrc > 0 or bool(signal2ShortSrc) == true) : false
sig3Long = signal3Enable and signal3LongSrc != close ? (signal3LongSrc > 0 or bool(signal3LongSrc) == true) : false
sig3Short = signal3Enable and signal3ShortSrc != close ? (signal3ShortSrc > 0 or bool(signal3ShortSrc) == true) : false
sig4Long = signal4Enable and signal4LongSrc != close ? (signal4LongSrc > 0 or bool(signal4LongSrc) == true) : false
sig4Short = signal4Enable and signal4ShortSrc != close ? (signal4ShortSrc > 0 or bool(signal4ShortSrc) == true) : false
sig5Long = signal5Enable and signal5LongSrc != close ? (signal5LongSrc > 0 or bool(signal5LongSrc) == true) : false
sig5Short = signal5Enable and signal5ShortSrc != close ? (signal5ShortSrc > 0 or bool(signal5ShortSrc) == true) : false

// ─────────────────── SIGNAL ARRAYS FOR PROCESSING ───────────────────
// Combine all signals for confluence analysis
allLongSignals = array.new<bool>(5)
allShortSignals = array.new<bool>(5)
array.set(allLongSignals, 0, sig1Long)
array.set(allLongSignals, 1, sig2Long)
array.set(allLongSignals, 2, sig3Long)
array.set(allLongSignals, 3, sig4Long)
array.set(allLongSignals, 4, sig5Long)
array.set(allShortSignals, 0, sig1Short)
array.set(allShortSignals, 1, sig2Short)
array.set(allShortSignals, 2, sig3Short)
array.set(allShortSignals, 3, sig4Short)
array.set(allShortSignals, 4, sig5Short)

// Count active signals
longSignalCount = (sig1Long ? 1 : 0) + (sig2Long ? 1 : 0) + (sig3Long ? 1 : 0) + (sig4Long ? 1 : 0) + (sig5Long ? 1 : 0)
shortSignalCount = (sig1Short ? 1 : 0) + (sig2Short ? 1 : 0) + (sig3Short ? 1 : 0) + (sig4Short ? 1 : 0) + (sig5Short ? 1 : 0)

// ═══════════════════ PRIMARY SIGNAL COMBINATION ═══════════════════
// Note: Primary signals are defined below after imports (line 126-127)

// ═══════════════════ RBW FILTER IMPORT ═══════════════════
// Import enhanced_ta library for existing RBW filter (defined later)
import HeWhoMustNotBeNamed/enhanced_ta/14 as eta

// ═══════════════════ SIGNAL PROCESSING SETUP ═══════════════════
// Legacy compatibility - combine all signals
primaryLongSig = sig1Long or sig2Long or sig3Long or sig4Long or sig5Long
primaryShortSig = sig1Short or sig2Short or sig3Short or sig4Short or sig5Short

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

// Tight Profit Locker (Post-Trend & BB Exit)
tightProfitType = input.string('ATR', 'Tight Type', options = ['ATR', 'Points', 'Percent'], group = 'Tight Profit Locker', tooltip = 'Type for tight profit locker used after trend exits and BB extremes')
tightProfitVal = input.float(1.55, 'Tight Value', step = 0.1, group = 'Tight Profit Locker', tooltip = 'Tight profit locker distance (1.55 ATR = half of normal 3.1 ATR)')
tightProfitOffset = input.float(0.05, 'Tight Pullback %', step = 0.01, minval = 0.01, maxval = 0.20, group = 'Tight Profit Locker', tooltip = 'Tight pullback percentage (0.05 = 5% for aggressive profit taking)')

// ─────────────────── BACKTESTING PANEL CONTROLS ─────────────────────
showBacktestTable = input.bool(true, '📊 Individual Signal Backtest', group = '🔍 Backtesting Panels', tooltip = 'Show individual signal performance (existing table)')


// HELPER FUNCTIONS
// Format stacked cell with Win Rate, PNL, and Drawdown
formatStackedCell(winRate, profit, trades, drawdown, isEnabled) =>
    if not isEnabled or trades == 0
        'No Data'
    else
        winRateStr = str.tostring(winRate * 100, '#.1') + '%'
        profitStr = str.tostring(profit, '#.##')
        drawdownStr = str.tostring(drawdown, '#.##')
        winRateStr + '\n' + profitStr + '\n-' + drawdownStr

// ─────────────────── 3 · BACKTESTING LOOKBACK PERIODS ─────────────────────
// Day-based lookback periods for backtesting analysis
backtestLookback = input.string('7 Days', 'Backtest Lookback Period', options = ['3 Days', '7 Days', '30 Days', '90 Days'], group = '🔍 Backtesting Panels', tooltip = 'How far back to analyze performance')

// BACKTESTING FUNCTIONS

// Convert day-based lookback to bars (dynamically adjusts to current timeframe)
getLookbackBars() =>
    // Determine the number of days for the lookback period
    days = switch backtestLookback
        '3 Days'  => 3
        '7 Days'  => 7
        '30 Days' => 30
        '90 Days' => 90
        => 7 // Default to 7 days

    // Get the current timeframe in minutes
    timeframeMinutes = timeframe.multiplier

    // Calculate the total minutes in the lookback period (assuming 24/7 market for bar calculation)
    totalMinutesInLookback = days * 24 * 60

    // Calculate the number of bars required for the lookback period on the current timeframe
    // If timeframe is less than 1 minute (i.e., seconds), default to 1 to avoid division by zero or skewed results
    barsToLookback = totalMinutesInLookback / (timeframeMinutes > 0 ? timeframeMinutes : 1)

    math.max(math.round(barsToLookback), 20) // Ensure a minimum of 20 bars for calculations

// GLOBAL BACKTESTING ARRAYS
// Global state arrays for each signal (like Multi-MA Strategy Analyzer)
var array<float> signal_profits = array.new<float>(5, 0.0)      // Total profit for each signal
var array<float> signal_entryPrices = array.new<float>(5, na)   // Current entry price for each signal
var array<string> signal_positions = array.new<string>(5, "none") // Position state: "long", "short", "none"
var array<int> signal_tradeCounts = array.new<int>(5, 0)        // Total completed trades
var array<int> signal_winCounts = array.new<int>(5, 0)          // Total winning trades
var array<float> signal_largestWins = array.new<float>(5, 0.0)  // Largest winning trade
var array<float> signal_grossProfits = array.new<float>(5, 0.0) // Total gross profit
var array<float> signal_grossLosses = array.new<float>(5, 0.0)  // Total gross loss





// Process each signal's backtesting (Multi-MA style approach)
processSignalBacktest(signalIndex, longCondition, shortCondition) =>
    if signalIndex >= 0 and signalIndex < 5
        // Get current state from arrays
        profit = array.get(signal_profits, signalIndex)
        entryPrice = array.get(signal_entryPrices, signalIndex)
        position = array.get(signal_positions, signalIndex)
        trades = array.get(signal_tradeCounts, signalIndex)
        wins = array.get(signal_winCounts, signalIndex)
        largestWin = array.get(signal_largestWins, signalIndex)
        grossProfit = array.get(signal_grossProfits, signalIndex)
        grossLoss = array.get(signal_grossLosses, signalIndex)
        
        // Long signal processing
        if longCondition
            if position == "short"
                // Exit short position
                tradeProfit = entryPrice - close
                profit += tradeProfit
                trades += 1
                if tradeProfit > 0
                    wins += 1
                    grossProfit += tradeProfit
                    largestWin := math.max(largestWin, tradeProfit)
                else
                    grossLoss += math.abs(tradeProfit)
                position := "none"
            if position == "none"
                // Enter long position
                entryPrice := close
                position := "long"
        
        // Short signal processing
        if shortCondition
            if position == "long"
                // Exit long position
                tradeProfit = close - entryPrice
                profit += tradeProfit
                trades += 1
                if tradeProfit > 0
                    wins += 1
                    grossProfit += tradeProfit
                    largestWin := math.max(largestWin, tradeProfit)
                else
                    grossLoss += math.abs(tradeProfit)
                position := "none"
            if position == "none"
                // Enter short position
                entryPrice := close
                position := "short"
        
        // Save updated state back to arrays
        array.set(signal_profits, signalIndex, profit)
        array.set(signal_entryPrices, signalIndex, entryPrice)
        array.set(signal_positions, signalIndex, position)
        array.set(signal_tradeCounts, signalIndex, trades)
        array.set(signal_winCounts, signalIndex, wins)
        array.set(signal_largestWins, signalIndex, largestWin)
        array.set(signal_grossProfits, signalIndex, grossProfit)
        array.set(signal_grossLosses, signalIndex, grossLoss)

// Get final backtesting results for a signal
getSignalResults(signalIndex) =>
    if signalIndex >= 0 and signalIndex < 5
        profit = array.get(signal_profits, signalIndex)
        trades = array.get(signal_tradeCounts, signalIndex)
        wins = array.get(signal_winCounts, signalIndex)
        largestWin = array.get(signal_largestWins, signalIndex)
        grossProfit = array.get(signal_grossProfits, signalIndex)
        grossLoss = array.get(signal_grossLosses, signalIndex)
        
        winRate = trades > 0 ? wins / trades : 0.0
        profitFactor = grossLoss > 0 ? grossProfit / grossLoss : (grossProfit > 0 ? 2.0 : 1.0)
        
        [profit, largestWin, profitFactor, float(trades), winRate]
    else
        [0.0, 0.0, 1.0, 0.0, 0.0]

// ───── Individual-signal back-test pass (event-gated) ─────
if sig1Long or sig1Short or array.get(signal_positions, 0) != "none"
    processSignalBacktest(0, sig1Long, sig1Short)

if sig2Long or sig2Short or array.get(signal_positions, 1) != "none"
    processSignalBacktest(1, sig2Long, sig2Short)

if sig3Long or sig3Short or array.get(signal_positions, 2) != "none"
    processSignalBacktest(2, sig3Long, sig3Short)

if sig4Long or sig4Short or array.get(signal_positions, 3) != "none"
    processSignalBacktest(3, sig4Long, sig4Short)

if sig5Long or sig5Short or array.get(signal_positions, 4) != "none"
    processSignalBacktest(4, sig5Long, sig5Short)




// ENTRY LOGIC
// Simplified entry logic without Smart Filter

// Initialize debug logging on first bar
if barstate.isfirst and debugEnabled
    debugMessage("INFO", 'EZAlgoTrader initialized with debug logging', color.green, color.white, 0.05)

// Long Entry - direct signal
if longEntrySignal
    entryComment = 'Long' + (longSignalCount > 1 ? ' C:' + str.tostring(longSignalCount) : '')
    strategy.entry('Long', strategy.long, qty = positionQty, comment = entryComment, alert_message = longEntryMsg)
    if debugEnabled
        debugMessage("INFO", 'Long entry triggered: ' + entryComment + ' at price: ' + str.tostring(close), color.green, color.white, 0.05)
    
    // Track which signals contributed to this strategy entry
    current_trade_entry := close
    current_trade_peak := close
    current_trade_trough := close
    array.set(current_trade_signals, 0, signal1Enable and sig1Long)
    array.set(current_trade_signals, 1, signal2Enable and sig2Long)
    array.set(current_trade_signals, 2, signal3Enable and sig3Long)
    array.set(current_trade_signals, 3, signal4Enable and sig4Long)
    array.set(current_trade_signals, 4, signal5Enable and sig5Long)

// Short Entry - direct signal
if shortEntrySignal
    entryComment = 'Short' + (shortSignalCount > 1 ? ' C:' + str.tostring(shortSignalCount) : '')
    strategy.entry('Short', strategy.short, qty = positionQty, comment = entryComment, alert_message = shortEntryMsg)
    if debugEnabled
        debugMessage("INFO", 'Short entry triggered: ' + entryComment + ' at price: ' + str.tostring(close), color.green, color.white, 0.05)
    
    // Track which signals contributed to this strategy entry
    current_trade_entry := close
    current_trade_peak := close
    current_trade_trough := close
    array.set(current_trade_signals, 0, signal1Enable and sig1Short)
    array.set(current_trade_signals, 1, signal2Enable and sig2Short)
    array.set(current_trade_signals, 2, signal3Enable and sig3Short)
    array.set(current_trade_signals, 3, signal4Enable and sig4Short)
    array.set(current_trade_signals, 4, signal5Enable and sig5Short)

// STRATEGY EXIT LOGIC
// This section bridges the gap between combo backtesting and actual strategy execution
// All exit methods now control the REAL strategy, not just the display panel

// Track entry price for distance-based exits
var float strategyEntryPrice = na

// STRATEGY PERFORMANCE TRACKING
// Track which signals contribute to actual strategy trades for professional analytics
// (Arrays declared in global variable section above)

// EXIT CONTROL FLAGS
// Per-method flags to prevent duplicate alerts and enable prioritization
var bool maExitSent = false
var bool fixedExitSent = false
var bool fibExitSent = false
var bool trailExitSent = false
var bool customExitSent = false
var bool inPosition = false
var bool exitInProgress = false

// Position tracking and flag reset logic
if strategy.position_size == 0
    strategyEntryPrice := na
else if strategy.position_size != 0 and na(strategyEntryPrice)
    strategyEntryPrice := strategy.position_avg_price

// Reset all exit flags on new position entry
currentPosition = strategy.position_size != 0
if currentPosition and not inPosition
    // New trade detected - reset all flags
    maExitSent := false
    fixedExitSent := false
    fibExitSent := false
    trailExitSent := false
    customExitSent := false
    exitInProgress := false
    inPosition := true
else if not currentPosition and inPosition
    // Trade closed - update state
    inPosition := false

// INTRABAR EXIT SYSTEM
// Exit logic that works properly while preventing alert spam
// Key insight: strategy.exit() calls must run every bar, only alerts should be limited

// ──────── 1. MA EXIT (Intrabar with Anti-Spam) ────────────
if maExitOn and strategy.position_size != 0
    longMaExit = strategy.position_size > 0 and close < priceMA
    shortMaExit = strategy.position_size < 0 and close > priceMA
    
    if longMaExit and not maExitSent
        strategy.close('Long', comment='MA Exit ', alert_message=longExitMsg)
        maExitSent := true
    else if shortMaExit and not maExitSent
        strategy.close('Short', comment='MA Exit ', alert_message=shortExitMsg)
        maExitSent := true

// ──────── 2. FIXED SL/TP (Always Active) ────────────
if fixedEnable and not na(strategyEntryPrice) and strategy.position_size != 0
    stopDistance = tpCalc(fixedStop)
    profitDistance = tp1Enable ? tpCalc(tp1Size) : na
    
    // Ensure distances are valid
    if na(stopDistance) or stopDistance <= 0
        stopDistance := 0.01  // Safe default
    
    if strategy.position_size > 0  // Long position
        stopLevel = math.max(strategyEntryPrice - stopDistance, close * 0.99)
        profitLevel = not na(profitDistance) ? strategyEntryPrice + profitDistance : na
        strategy.exit('Fixed-Long', from_entry='Long', stop=stopLevel, limit=profitLevel, comment='Fixed SL/TP')
        if not fixedExitSent
            fixedExitSent := true
    
    else if strategy.position_size < 0  // Short position
        stopLevel = math.min(strategyEntryPrice + stopDistance, close * 1.01)
        profitLevel = not na(profitDistance) ? strategyEntryPrice - profitDistance : na
        strategy.exit('Fixed-Short', from_entry='Short', stop=stopLevel, limit=profitLevel, comment='Fixed SL/TP')
        if not fixedExitSent
            fixedExitSent := true



// ──────── 4. SMART PROFIT LOCKER (Aggressive Profit Protection) ────────────
// TREND RIDING INTEGRATION: Disable during pure trend mode, use tight settings in hybrid mode
// Three modes: Normal Mode, Trend Mode (disabled), Hybrid Mode (tight settings)

// Determine which mode we're in and if Smart Profit Locker should run
runSmartProfitLocker = false
useNormalSettings = true

if smartProfitEnable and strategy.position_size != 0
    if inTrendRidingMode and not inHybridExitMode
        // PURE TREND MODE: Completely disable Smart Profit Locker
        runSmartProfitLocker := false
        if debugEnabled
            debugMessage("INFO", "🚀 Trend Mode: Smart Profit Locker DISABLED", color.green, color.white, 0.05)
    else if inHybridExitMode
        // HYBRID MODE: Use tight profit locker settings
        runSmartProfitLocker := true
        useNormalSettings := false
        if debugEnabled
            debugMessage("INFO", "⚡ Hybrid Mode: Using TIGHT Profit Locker", color.green, color.white, 0.05)
    else
        // NORMAL MODE: Use regular Smart Profit Locker settings
        runSmartProfitLocker := true
        useNormalSettings := true

if runSmartProfitLocker
    // Assign values to existing variables
    smartDistance := 0.0
    smartOffset := 0.0
    exitComment := ''
    
    // Calculate distance and offset based on mode
    if useNormalSettings
        // NORMAL MODE: Use regular Smart Profit Locker settings
        smartDistance := smartProfitType == 'ATR' ? smartProfitVal * atrVal : smartProfitType == 'Points' ? smartProfitVal : strategyEntryPrice * smartProfitVal / 100.0
        smartOffset := smartDistance * math.max(smartProfitOffset, 0.01)
        exitComment := 'Smart Profit Locker'
    else
        // HYBRID MODE: Use tight profit locker settings
        smartDistance := tightProfitType == 'ATR' ? tightProfitVal * atrVal : tightProfitType == 'Points' ? tightProfitVal : strategyEntryPrice * tightProfitVal / 100.0
        smartOffset := smartDistance * math.max(tightProfitOffset, 0.01)
        exitComment := 'Tight Profit Locker'
    
    // Ensure distances are valid
    if na(smartDistance) or smartDistance <= 0
        smartDistance := 50.0  // Safe default value in points
    if na(smartOffset) or smartOffset <= 0
        smartOffset := 5.0  // Safe default offset
    
    if strategy.position_size > 0  // Long position
        strategy.exit('Smart-Long', from_entry='Long', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment=exitComment)
        if not trailExitSent
            trailExitSent := true
    else if strategy.position_size < 0  // Short position
        strategy.exit('Smart-Short', from_entry='Short', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment=exitComment)
        if not trailExitSent
            trailExitSent := true

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

// ADDITIONAL BIAS FILTERS
// Extended trend-based directional bias filters with confluence logic

// Filter Enable Toggles (Row 1)
hullEnable = input.bool(false, '📈 Hull Suite', group='🎯 Extended Bias Filters', inline='bias1')
supertrendEnable = input.bool(false, '🔥 SuperTrend', group='🎯 Extended Bias Filters', inline='bias1')
quadrantEnable = input.bool(false, '📊 Quadrant NW', group='🎯 Extended Bias Filters', inline='bias1')

// Confluence Logic (Row 2)
biasConfluence = input.string('Any', 'Confluence Mode', options=['Any', 'Majority', 'All'], group='🎯 Extended Bias Filters', inline='bias2', tooltip='Any: At least one filter agrees | Majority: Most filters agree | All: All enabled filters agree')

// Hull Suite Parameters (Row 3)
hullLength = input.int(55, 'Hull Length', group='🎯 Extended Bias Filters', inline='bias3')
hullMode = input.string('Hma', 'Hull Type', options=['Hma', 'Ehma', 'Thma'], group='🎯 Extended Bias Filters', inline='bias3')

// SuperTrend Parameters (Row 4)
supertrendFactor = input.float(3.0, 'ST Factor', step=0.1, group='🎯 Extended Bias Filters', inline='bias4')
supertrendATR = input.int(10, 'ST ATR', group='🎯 Extended Bias Filters', inline='bias4')

// Quadrant Parameters (Row 5)
quadrantLookback = input.float(8.0, 'Quad Lookback', minval=3.0, group='🎯 Extended Bias Filters', inline='bias5')
quadrantWeight = input.float(8.0, 'Quad Weight', step=0.25, group='🎯 Extended Bias Filters', inline='bias5')

// MTF ZIGZAG FILTER
// Advanced trend analysis using higher timeframe ZigZag pivots

// MTF ZigZag Enable Toggle (Row 1)
mtfZigzagEnable = input.bool(false, '📈 MTF ZigZag', group='🎯 Advanced Bias Filters', inline='mtf1', tooltip='Enable Multi-Timeframe ZigZag trend analysis. Uses higher timeframe pivot analysis to detect strong trending conditions. Only uses confirmed bars (no repainting).')

// MTF ZigZag Parameters (Row 2)
mtfTimeframe = input.timeframe('60', 'MTF Timeframe', group='🎯 Advanced Bias Filters', inline='mtf2', tooltip='Higher timeframe for trend analysis. Common choices: 15 for 5m charts, 60 for 15m charts, 240 for 1H charts. Higher timeframes give stronger trend signals but slower response.')
mtfZigzagLength = input.int(21, 'ZZ Length', minval=5, group='🎯 Advanced Bias Filters', inline='mtf2', tooltip='ZigZag sensitivity. Lower values (5-10) = more sensitive, more signals. Higher values (20-50) = less sensitive, stronger trends only.')

// MTF ZigZag Advanced (Row 3)
mtfDepth = input.int(200, 'ZZ Depth', minval=50, maxval=500, step=50, group='🎯 Advanced Bias Filters', inline='mtf3', tooltip='Number of pivots to analyze. Higher values give more historical context but use more memory. 200 is good balance for most timeframes.')
mtfMinPivotAge = input.int(2, 'Min Pivot Age', minval=1, maxval=5, group='🎯 Advanced Bias Filters', inline='mtf3', tooltip='Minimum bars a pivot must be old to be considered confirmed. 2+ bars ensures no repainting.')

// REVERSAL PROBABILITY FILTER
// Statistical reversal probability analysis for intelligent trend continuation
// Uses AlgoAlpha + LuxAlgo probability indicators for bias decisions

// Reversal Probability Enable Toggle (Row 1)
reversalProbEnable = input.bool(false, '📊 Reversal Probability', group='🎯 Probability Bias Filter', inline='prob1', tooltip='Enable reversal probability bias filter. Uses statistical analysis to determine trend continuation likelihood. LOW probability = continue trend, HIGH probability = neutral/exit bias.')
reversalProbPrimary = input.bool(false, '⭐ Primary Mode', group='🎯 Probability Bias Filter', inline='prob1', tooltip='Make reversal probability the primary bias filter. When enabled, this filter can work alone when other filters are disabled.')

// Probability Thresholds (Row 2)
reversalProbThreshold = input.float(30.0, 'Bias Threshold %', minval=10.0, maxval=90.0, step=5.0, group='🎯 Probability Bias Filter', inline='prob2', tooltip='Reversal probability threshold for bias decisions. Below this % = continue trend bias. Default 30% = conservative. Lower = more aggressive trend continuation.')
reversalExitThreshold = input.float(70.0, 'Exit Threshold %', minval=30.0, maxval=95.0, step=5.0, group='🎯 Probability Bias Filter', inline='prob2', tooltip='Reversal probability threshold for exit decisions. Above this % = consider exiting. Separate from bias threshold for independent testing.')

// Trend-Riding Integration (Row 3)
reversalProbRequiredForTrend = input.bool(false, '🚀 Required for Trend-Riding', group='🎯 Probability Bias Filter', inline='prob3', tooltip='Require LOW reversal probability for trend-riding activation. When enabled, trend-riding mode will only activate if reversal probability is below threshold.')
reversalProbWeight = input.float(1.0, 'Filter Weight', minval=0.1, maxval=3.0, step=0.1, group='🎯 Probability Bias Filter', inline='prob3', tooltip='Weight of this filter in confluence voting. 1.0 = equal weight. Higher values give this filter more influence in bias decisions.')

// Entry Probability Filter (Row 4) - NEW FEATURE
entryProbFilterEnable = input.bool(false, '🚫 Entry Probability Filter', group='🎯 Probability Bias Filter', inline='prob4', tooltip='OPTIONAL: Block entries when reversal probability is too high (e.g., gap opens at extremes). Prevents entering at 84%+ reversal probability zones. OFF by default.')
entryProbFilterThreshold = input.float(84.0, 'Block Above %', minval=70.0, maxval=95.0, step=1.0, group='🎯 Probability Bias Filter', inline='prob4', tooltip='Block entries when reversal probability exceeds this threshold. 84% = AlgoAlpha extreme level. 90% = LuxAlgo 90th percentile. Protects against gap-open losses.')

// BOLLINGER BAND RISK MANAGEMENT
// Critical risk management: Never take signals outside Bollinger Bands

// Bollinger Band Entry Filter (Row 1) - CRITICAL RISK MANAGEMENT
bbEntryFilterEnable = input.bool(true, '🚫 BB Entry Filter', group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='CRITICAL: Ignore buy signals above upper BB and sell signals below lower BB. Prevents gap-open disasters. ON by default - should NEVER be turned off.')
bbLength = input.int(20, 'BB Length', minval=5, maxval=50, group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='Bollinger Band period for entry filtering. Standard is 20.')
bbMultiplier = input.float(2.0, 'BB Mult', minval=1.0, maxval=3.0, step=0.1, group='🎯 Bollinger Band Risk Management', inline='bb1', tooltip='Bollinger Band standard deviation multiplier. Standard is 2.0.')

// Bollinger Band Exit Logic (Row 2) - OPTIONAL TESTING FEATURE
bbExitEnable = input.bool(false, '⚡ BB Exit Logic', group='🎯 Bollinger Band Risk Management', inline='bb2', tooltip='OPTIONAL: When price hits BB extreme during trade, flip to tight Smart Profit Locker. OFF by default - for testing only.')
bbExitTightness = input.float(0.5, 'Tight Multiplier', minval=0.1, maxval=1.0, step=0.1, group='🎯 Bollinger Band Risk Management', inline='bb2', tooltip='Smart Profit Locker multiplier when BB exit triggers. 0.5 = half normal distance (tighter). Lower = more aggressive profit taking.')

// TREND-RIDING OVERLAY SYSTEM
// Advanced exit system to "let winners run" in strong trending conditions

// Trend-Riding Enable Toggle (Row 1)
trendRidingEnable = input.bool(false, '🚀 Trend-Riding Mode', group='🎯 Trend-Riding System', inline='tr1', tooltip='Enable trend-riding overlay that overrides Smart Profit Locker when all filters strongly agree. Designed to "let winners run" in obvious trending conditions while preserving high win rate in other scenarios.')

// Activation Requirements (Row 2)
trendActivationMode = input.string('All Filters', 'Activation', options=['Majority Filters', 'All Filters', 'Custom Count'], group='🎯 Trend-Riding System', inline='tr2', tooltip='How many directional bias filters must agree to activate trend-riding mode. "All Filters" = strongest requirement, "Majority" = more frequent activation.')
trendMinFilters = input.int(4, 'Min Count', minval=2, maxval=6, group='🎯 Trend-Riding System', inline='tr2', tooltip='Minimum number of filters that must agree (only used with "Custom Count" mode). Higher numbers = stronger trends required.')

// Trend Strength Requirements (Row 3)
trendMinBars = input.int(3, 'Min Trend Bars', minval=1, maxval=10, group='🎯 Trend-Riding System', inline='tr3', tooltip='Minimum consecutive bars all filters must agree before activating trend-riding. Prevents activation on brief/false trend signals.')
trendMaxHold = input.int(50, 'Max Hold Bars', minval=10, maxval=200, group='🎯 Trend-Riding System', inline='tr3', tooltip='Maximum bars to stay in trend-riding mode regardless of trend strength. Prevents overnight risk and ensures eventual exit.')

// SIGNAL-DRIVEN TREND RIDER EXIT SYSTEM
// Revolutionary exit system: Instead of price-based exits, use opposite signals from primary indicators

// Exit Signal Selection Header (Row 4)
trendRiderExitHeader = input.string('--- Exit Signal Source ---', 'Signal Exit Config', group='🎯 Trend-Riding System', inline='tr4', tooltip='Select which primary signals can trigger exits during trend-riding mode. When trend-riding is active, normal exits are ignored and the system waits for opposite signals from selected indicators.')

// Signal Selection Checkboxes (Row 5)
useExitSignal1 = input.bool(true, 'Exit: Sig 1', group='🎯 Trend-Riding System', inline='tr5', tooltip='Use opposite signal from Signal 1 (LuxAlgo) to exit trend-riding mode. If in long trade, waits for LuxAlgo sell signal.')
useExitSignal2 = input.bool(false, 'Exit: Sig 2', group='🎯 Trend-Riding System', inline='tr5', tooltip='Use opposite signal from Signal 2 (UTBot) to exit trend-riding mode.')
useExitSignal3 = input.bool(false, 'Exit: Sig 3', group='🎯 Trend-Riding System', inline='tr5', tooltip='Use opposite signal from Signal 3 (VIDYA) to exit trend-riding mode.')

// Additional Signal Selection (Row 6)
useExitSignal4 = input.bool(false, 'Exit: Sig 4', group='🎯 Trend-Riding System', inline='tr6', tooltip='Use opposite signal from Signal 4 (KyleAlgo) to exit trend-riding mode.')
useExitSignal5 = input.bool(false, 'Exit: Sig 5', group='🎯 Trend-Riding System', inline='tr6', tooltip='Use opposite signal from Signal 5 (Wonder) to exit trend-riding mode.')

// Confluence and Safety Settings (Row 7)
minOppositeSignals = input.int(1, 'Min Exit Signals', minval=1, maxval=5, group='🎯 Trend-Riding System', inline='tr7', tooltip='Minimum number of selected opposite signals required to trigger exit. 1 = exit on first available signal. 2+ = wait for confluence of multiple opposite signals.')
enableTrendBreakSafety = input.bool(true, 'Trend Break Safety', group='🎯 Trend-Riding System', inline='tr7', tooltip='Exit immediately if trend consensus breaks (filters no longer agree). Critical safety net to prevent being stuck in sideways markets.')

// Catastrophic Stop Safety (Row 8)
enableCatastrophicStop = input.bool(true, 'Catastrophic Stop', group='🎯 Trend-Riding System', inline='tr8', tooltip='Respect catastrophic stop-loss even during trend-riding. Uses 3x normal stop distance as final safety net.')
catastrophicStopMultiplier = input.float(3.0, 'Cat Stop Mult', minval=2.0, maxval=5.0, step=0.5, group='🎯 Trend-Riding System', inline='tr8', tooltip='Multiplier for catastrophic stop distance. 3.0 = 3x normal trailing stop distance as absolute maximum loss.')


// Exit Mode Selection (Row 4)
adaptiveExitMode = input.string('Adaptive', 'Exit Mode', options=['Confirmed Only', 'Adaptive', 'Aggressive'], group='🎯 Adaptive Exit System', inline='ie4', tooltip='Exit timing mode. Confirmed Only = wait for bar close. Adaptive = real-time exits in high volatility. Aggressive = always use real-time exits.')
volatilityThreshold = input.float(1.2, 'Vol Threshold', minval=1.0, maxval=2.0, step=0.1, group='🎯 Adaptive Exit System', inline='ie4', tooltip='Volatility ratio threshold for adaptive mode. 1.2 = use real-time exits when current ATR is 1.2x average ATR.')

// EXIT INDICATOR INTEGRATION SYSTEM
// Advanced exit signal detection using multiple reversal indicators
// Implements hybrid exit mode to reduce profit giveback in trend-riding

// Exit Indicator Enable Toggles (Row 1)
exitIndicator1Enable = input.bool(true, '🔄 AlgoAlpha Reversal', group='🚨 Exit Indicators', inline='exit1', tooltip='Enable AlgoAlpha Reversal Signal detection. Extremely accurate reversal signals for trend exit confirmation.')
exitIndicator2Enable = input.bool(false, '🔄 LuxAlgo RevProb', group='🚨 Exit Indicators', inline='exit1', tooltip='Enable LuxAlgo Reversal Probability zones for exit signal detection.')
exitIndicator3Enable = input.bool(false, '🔄 Reversal Candles', group='🚨 Exit Indicators', inline='exit1', tooltip='Enable simple reversal candlestick pattern detection.')

// External Plot Integration (Row 2) - 3 Plot Points for LuxAlgo Integration
exitPlot1Enable = input.bool(false, '📊 Exit Plot 1', group='🚨 Exit Indicators', inline='exit2', tooltip='Enable external exit plot 1. Connect your LuxAlgo Long Exit signal here.')
exitPlot1Source = input.source(close, 'Plot 1 Src', group='🚨 Exit Indicators', inline='exit2', tooltip='Source for external exit plot 1 (LuxAlgo Long Exit)')
exitPlot2Enable = input.bool(false, '📊 Exit Plot 2', group='🚨 Exit Indicators', inline='exit2', tooltip='Enable external exit plot 2. Connect your LuxAlgo Short Exit signal here.')
exitPlot2Source = input.source(close, 'Plot 2 Src', group='🚨 Exit Indicators', inline='exit2', tooltip='Source for external exit plot 2 (LuxAlgo Short Exit)')

// Additional Plot Point (Row 3)
exitPlot3Enable = input.bool(false, '📊 Exit Plot 3', group='🚨 Exit Indicators', inline='exit3', tooltip='Enable external exit plot 3. Additional exit signal input for custom indicators.')
exitPlot3Source = input.source(close, 'Plot 3 Src', group='🚨 Exit Indicators', inline='exit3', tooltip='Source for external exit plot 3 (Custom Exit Signal)')

// Hybrid Exit Mode Configuration (Row 4)
hybridExitEnable = input.bool(true, '🔄 Hybrid Exit Mode', group='🚨 Exit Indicators', inline='exit4', tooltip='Enable hybrid exit mode. When exit indicators reach threshold, switch from signal-based to Smart Profit Locker mode.')
hybridExitThreshold = input.int(2, 'Exit Threshold', minval=1, maxval=5, group='🚨 Exit Indicators', inline='exit4', tooltip='Number of exit indicators required to trigger hybrid mode switch. 2 = switch to Smart Profit Locker when 2+ exit indicators trigger.')

// AlgoAlpha Reversal Signal Parameters (Row 5)
algoAlphaLookback = input.int(12, 'AA Lookback', minval=5, maxval=20, group='🚨 Exit Indicators', inline='exit5', tooltip='AlgoAlpha lookback period for reversal detection. Lower = more sensitive.')
algoAlphaConfirmation = input.int(3, 'AA Confirm', minval=1, maxval=5, group='🚨 Exit Indicators', inline='exit5', tooltip='AlgoAlpha confirmation period. Number of bars to confirm reversal signal.')

// ALGOA ALPHA REVERSAL SIGNAL CALCULATION
// Core logic ported from AlgoAlphaReversalSignal.pine for exit detection

// AlgoAlpha Variables
var int aaBullCandleScore = 0
var int aaBearCandleScore = 0
var bool aaBullReversalCandidate = false
var bool aaBearReversalCandidate = false
var float aaBullReversalLow = 0.0
var float aaBullReversalHigh = 0.0
var float aaBearReversalLow = 0.0
var float aaBearReversalHigh = 0.0
var bool aaBullSignalConfirmed = false
var bool aaBearSignalConfirmed = false
var int aaBullCandleCounter = 0
var int aaBearCandleCounter = 0

// AlgoAlpha Signal Detection
aaVolumeIsHigh = volume > ta.sma(volume, 20)
aaBullSignal = false
aaBearSignal = false

if exitIndicator1Enable
    // Reset candle scores
    aaBullCandleScore := 0
    aaBearCandleScore := 0
    
    // Loop through the lookback period
    for i = 0 to algoAlphaLookback - 1 by 1
        if close < low[i]
            aaBullCandleScore := aaBullCandleScore + 1
        if close > high[i]
            aaBearCandleScore := aaBearCandleScore + 1
    
    // Bearish reversal detection (exit signal for long positions)
    if aaBearCandleScore == algoAlphaLookback - 1
        aaBearReversalCandidate := true
        aaBearReversalLow := low
        aaBearReversalHigh := high
        aaBearSignalConfirmed := false
        aaBearCandleCounter := 0
    
    if aaBearReversalCandidate
        aaBearCandleCounter := aaBearCandleCounter + 1
        if close > aaBearReversalHigh
            aaBearReversalCandidate := false
    
    aaBearCondition = false
    if aaBearReversalCandidate and close < aaBearReversalLow and not aaBearSignalConfirmed and aaBearCandleCounter <= algoAlphaConfirmation + 1
        aaBearSignalConfirmed := true
        aaBearCondition := true
    
    if aaBearCondition
        if aaVolumeIsHigh  // Volume confirmation
            aaBearSignal := true
    
    // Bullish reversal detection (exit signal for short positions)
    if aaBullCandleScore == algoAlphaLookback - 1
        aaBullReversalCandidate := true
        aaBullReversalLow := low
        aaBullReversalHigh := high
        aaBullSignalConfirmed := false
        aaBullCandleCounter := 0
    
    if aaBullReversalCandidate
        aaBullCandleCounter := aaBullCandleCounter + 1
        if close < aaBullReversalLow
            aaBullReversalCandidate := false
    
    aaBullCondition = false
    if aaBullReversalCandidate and close > aaBullReversalHigh and not aaBullSignalConfirmed and aaBullCandleCounter <= algoAlphaConfirmation + 1
        aaBullSignalConfirmed := true
        aaBullCondition := true
    
    if aaBullCondition
        if aaVolumeIsHigh  // Volume confirmation
            aaBullSignal := true

// REVERSAL PROBABILITY CALCULATION
// Composite probability analysis using AlgoAlpha + LuxAlgo indicators
// Provides statistical likelihood of trend reversal for intelligent bias decisions

// AlgoAlpha Probability Calculation (ported from AlgoAlphaReversalProbability.pine)
var durations = array.new_int()
cut = ta.barssince(ta.cross(close, ta.ema(close, 21)))  // Simplified trend cross detection

if cut == 0 and cut != cut[1]
    durations.unshift(cut[1])

basis = durations.avg()
stdDev = durations.stdev()

// Statistical probability calculation using cumulative distribution function
f_cdf(z) =>
    a1 = 0.254829592
    a2 = -0.284496736
    a3 = 1.421413741
    a4 = -1.453152027
    a5 = 1.061405429
    p = 0.3275911
    
    sign = z < 0 ? -1 : 1
    x = math.abs(z) / math.sqrt(2)
    t = 1 / (1 + p * x)
    erf_approx = 1 - (((((a5 * t + a4) * t) + a3) * t + a2) * t + a1) * t * math.exp(-x * x)
    
    0.5 * (1 + sign * erf_approx)

z = na(stdDev) or stdDev == 0 ? 0 : (cut - basis) / stdDev
algoAlphaProbability = f_cdf(z) * 100  // Convert to percentage (0-100)

// LuxAlgo Probability Zone Estimation (simplified)
// Based on current price position relative to recent pivots
pivotHigh = ta.pivothigh(high, 10, 10)
pivotLow = ta.pivotlow(low, 10, 10)
var float lastPivotHigh = na
var float lastPivotLow = na

if not na(pivotHigh)
    lastPivotHigh := pivotHigh
if not na(pivotLow)
    lastPivotLow := pivotLow

// Estimate probability zone based on price position
pricePosition = na(lastPivotHigh) or na(lastPivotLow) ? 50 : 
  ((close - lastPivotLow) / (lastPivotHigh - lastPivotLow)) * 100

// Convert to probability zones (25%, 50%, 75%, 90%)
luxAlgoProbabilityZone = 
  pricePosition > 90 ? 90 :
  pricePosition > 75 ? 75 :
  pricePosition > 50 ? 50 : 25

// Composite Reversal Probability (weighted average)
compositeProbability = reversalProbEnable ? 
  (algoAlphaProbability * 0.6 + luxAlgoProbabilityZone * 0.4) : 50

// ENTRY PROBABILITY FILTER
// NEW FEATURE: Block entries at extreme reversal probability levels
// Prevents entering trades at gap opens or other extreme reversal zones

// Entry Probability Filter Logic
entryProbabilityOK = entryProbFilterEnable ? 
  (compositeProbability <= entryProbFilterThreshold) : true  // Block entries above threshold

// Debug output for Entry Probability Filter
if entryProbFilterEnable and not entryProbabilityOK and debugEnabled
    debugMessage("INFO", "🚫 ENTRY BLOCKED: Reversal probability " + str.tostring(compositeProbability, "#.#") + "% exceeds threshold " + str.tostring(entryProbFilterThreshold, "#.#") + "%", color.green, color.white, 0.05)

// Reversal Probability Bias Logic
reversalProbLongBias = reversalProbEnable ? 
  (compositeProbability < reversalProbThreshold) : true  // Low probability = continue long trend
reversalProbShortBias = reversalProbEnable ? 
  (compositeProbability < reversalProbThreshold) : true  // Low probability = continue short trend

// EXIT INDICATOR VOTING SYSTEM
// Hybrid exit mode: count exit indicators and switch to Smart Profit Locker

// External Plot Signal Detection (for LuxAlgo integration)
exitPlot1LongSignal = exitPlot1Enable and exitPlot1Source > exitPlot1Source[1]  // Rising signal = long exit
exitPlot1ShortSignal = exitPlot1Enable and exitPlot1Source < exitPlot1Source[1]  // Falling signal = short exit
exitPlot2LongSignal = exitPlot2Enable and exitPlot2Source > exitPlot2Source[1]  // Rising signal = long exit  
exitPlot2ShortSignal = exitPlot2Enable and exitPlot2Source < exitPlot2Source[1]  // Falling signal = short exit
exitPlot3LongSignal = exitPlot3Enable and exitPlot3Source > exitPlot3Source[1]  // Rising signal = long exit
exitPlot3ShortSignal = exitPlot3Enable and exitPlot3Source < exitPlot3Source[1]  // Falling signal = short exit

// Simple Reversal Candle Detection (basic implementation)
reversalCandleBull = exitIndicator3Enable and (close > open and close[1] < open[1] and close > high[1])  // Bullish engulfing
reversalCandleBear = exitIndicator3Enable and (close < open and close[1] > open[1] and close < low[1])   // Bearish engulfing

// Count Exit Indicators for Long Positions (bearish exit signals)
longExitSignalCount = 0
longExitSignalCount := longExitSignalCount + (aaBearSignal ? 1 : 0)  // AlgoAlpha bearish reversal
longExitSignalCount := longExitSignalCount + (exitPlot1LongSignal ? 1 : 0)  // External plot 1
longExitSignalCount := longExitSignalCount + (exitPlot2LongSignal ? 1 : 0)  // External plot 2  
longExitSignalCount := longExitSignalCount + (exitPlot3LongSignal ? 1 : 0)  // External plot 3
longExitSignalCount := longExitSignalCount + (reversalCandleBear ? 1 : 0)  // Bearish reversal candle

// Count Exit Indicators for Short Positions (bullish exit signals)
shortExitSignalCount = 0
shortExitSignalCount := shortExitSignalCount + (aaBullSignal ? 1 : 0)  // AlgoAlpha bullish reversal
shortExitSignalCount := shortExitSignalCount + (exitPlot1ShortSignal ? 1 : 0)  // External plot 1
shortExitSignalCount := shortExitSignalCount + (exitPlot2ShortSignal ? 1 : 0)  // External plot 2
shortExitSignalCount := shortExitSignalCount + (exitPlot3ShortSignal ? 1 : 0)  // External plot 3
shortExitSignalCount := shortExitSignalCount + (reversalCandleBull ? 1 : 0)  // Bullish reversal candle

// Enhanced Hybrid Exit Mode Trigger Detection
// Combines binary signal counting with reversal probability analysis
probabilityExitTriggered = reversalProbEnable and compositeProbability >= reversalExitThreshold

// Multiple trigger modes:
// 1. Binary signals only (original logic)
// 2. Probability only (new statistical approach) 
// 3. Combined mode (signals + probability confirmation)
binaryExitTriggeredLong = hybridExitEnable and longExitSignalCount >= hybridExitThreshold
binaryExitTriggeredShort = hybridExitEnable and shortExitSignalCount >= hybridExitThreshold

// Final hybrid exit decision (enhanced logic)
hybridExitTriggeredLong = binaryExitTriggeredLong or (reversalProbEnable and probabilityExitTriggered)
hybridExitTriggeredShort = binaryExitTriggeredShort or (reversalProbEnable and probabilityExitTriggered)

// Activate Hybrid Exit Mode
if not inHybridExitMode and strategy.position_size != 0
    if (strategy.position_size > 0 and hybridExitTriggeredLong) or (strategy.position_size < 0 and hybridExitTriggeredShort)
        inHybridExitMode := true
        hybridModeStartBar := bar_index
        if debugEnabled
            exitSignalType = strategy.position_size > 0 ? "Long" : "Short"
            signalCount = strategy.position_size > 0 ? longExitSignalCount : shortExitSignalCount
            binaryTriggered = strategy.position_size > 0 ? binaryExitTriggeredLong : binaryExitTriggeredShort
            probTriggered = probabilityExitTriggered
            triggerReason = binaryTriggered and probTriggered ? "Signals+Probability" : 
                           binaryTriggered ? "Binary Signals" : "Probability Only"
            probInfo = reversalProbEnable ? (" | RevProb: " + str.tostring(compositeProbability, "#.#") + "% (" + str.tostring(reversalExitThreshold, "#.#") + "% threshold)") : ""
            debugMessage("INFO", "🔄 HYBRID EXIT: Activated for " + exitSignalType + " position. Trigger: " + triggerReason + " | Signals: " + str.tostring(signalCount) + "/" + str.tostring(hybridExitThreshold) + probInfo, color.green, color.white, 0.05)

// Deactivate Hybrid Exit Mode (when position closes)
if inHybridExitMode and strategy.position_size == 0
    inHybridExitMode := false
    if debugEnabled
        barsInHybrid = bar_index - hybridModeStartBar
        debugMessage("INFO", "🔄 HYBRID EXIT: Deactivated. Duration: " + str.tostring(barsInHybrid) + " bars", color.green, color.white, 0.05)

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

    // Relative Bandwidth calculation
    if not na(rbwUpper) and not na(rbwLower)
        rbwATR := ta.atr(rbwATRLength)
        if not na(rbwATR) and rbwATR > 0
            rbwRelativeBandwidth := (rbwUpper - rbwLower) / rbwATR
            
            // Calculate reference bands for signal
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
var bool bbEntryFilterOK = true
var bool bbExitTriggered = false

if bbEntryFilterEnable
    // Calculate standard Bollinger Bands for entry filtering
    bbMiddle := ta.sma(close, bbLength)
    bbStdDev = ta.stdev(close, bbLength)
    bbUpper := bbMiddle + bbStdDev * bbMultiplier
    bbLower := bbMiddle - bbStdDev * bbMultiplier
    
    // Entry filter logic: Block signals outside bands
    bbLongFilterOK := close <= bbUpper  // Allow long entries only when price is NOT above upper band
    bbShortFilterOK := close >= bbLower  // Allow short entries only when price is NOT below lower band
    
    // Debug output for BB entry filter (already properly wrapped)
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

// BB EXIT INTEGRATION WITH TIGHT PROFIT LOCKER
// When BB exit is triggered, switch to tight profit locker mode using user settings
if bbExitTriggered and strategy.position_size != 0
    // Calculate tight profit locker distance using user-defined settings
    tightDistance = tightProfitType == 'ATR' ? tightProfitVal * atrVal : tightProfitType == 'Points' ? tightProfitVal : strategyEntryPrice * tightProfitVal / 100.0
    tightOffset = tightDistance * math.max(tightProfitOffset, 0.01)
    
    // Ensure valid values
    if na(tightDistance) or tightDistance <= 0
        tightDistance := atrVal * 1.55  // Fallback to 1.55 ATR
    if na(tightOffset) or tightOffset <= 0
        tightOffset := tightDistance * 0.05  // Fallback to 5% offset
    
    if strategy.position_size > 0  // Long position
        strategy.exit('BB-Tight-Long', from_entry='Long', trail_points=math.max(tightDistance, 0.01), trail_offset=math.max(tightOffset, 0.01), comment='BB Tight Exit')
        if debugEnabled
            debugMessage("INFO", "🎯 BB TIGHT EXIT: Long trailing stop - Distance: " + str.tostring(tightDistance, "#.##") + " pts, Offset: " + str.tostring(tightOffset, "#.##") + " pts", color.green, color.white, 0.05)
    
    else if strategy.position_size < 0  // Short position
        strategy.exit('BB-Tight-Short', from_entry='Short', trail_points=math.max(tightDistance, 0.01), trail_offset=math.max(tightOffset, 0.01), comment='BB Tight Exit')
        if debugEnabled
            debugMessage("INFO", "🎯 BB TIGHT EXIT: Short trailing stop - Distance: " + str.tostring(tightDistance, "#.##") + " pts, Offset: " + str.tostring(tightOffset, "#.##") + " pts", color.green, color.white, 0.05)

// HULL SUITE CALCULATION
// Hull Moving Average trend detection

// Hull MA calculation functions (must be defined outside conditional blocks)
HMA(src, length) =>
    ta.wma(2 * ta.wma(src, length / 2) - ta.wma(src, length), math.round(math.sqrt(length)))

EHMA(src, length) =>
    ta.ema(2 * ta.ema(src, length / 2) - ta.ema(src, length), math.round(math.sqrt(length)))

THMA(src, length) =>
    ta.wma(ta.wma(src, length / 3) * 3 - ta.wma(src, length / 2) - ta.wma(src, length), length)

var float hullMA = na
var bool hullBullish = false

if hullEnable
    // Calculate Hull MA based on selected mode
    hullMA := hullMode == 'Hma' ? HMA(close, hullLength) : hullMode == 'Ehma' ? EHMA(close, hullLength) : hullMode == 'Thma' ? THMA(close, hullLength / 2) : na
    
    // Determine trend direction (current > 2 bars ago = bullish)
    hullBullish := not na(hullMA) and hullMA > hullMA[2]
else
    // When disabled, set neutral (allow all trades)
    hullBullish := false

// SUPERTREND CALCULATION
// SuperTrend directional bias
var float supertrend = na
var float supertrendDirection = na
var bool supertrendBullish = false

if supertrendEnable
    [st, dir] = ta.supertrend(supertrendFactor, supertrendATR)
    supertrend := st
    supertrendDirection := dir
    supertrendBullish := dir < 0  // Uptrend when direction < 0
else
    // When disabled, set neutral (allow all trades)
    supertrendBullish := false

// QUADRANT (NADARAYA-WATSON) CALCULATION
// Rational Quadratic Kernel regression for trend detection

// Kernel regression function (must be defined outside conditional blocks)
kernel_regression(src, h, r) =>
    float currentWeight = 0.0
    float cumulativeWeight = 0.0
    int size = math.min(25, bar_index)  // Limit lookback for performance
    
    for i = 0 to size
        y = src[i]
        w = math.pow(1 + math.pow(i, 2) / (math.pow(h, 2) * 2 * r), -r)
        currentWeight := currentWeight + y * w
        cumulativeWeight := cumulativeWeight + w
    
    cumulativeWeight > 0 ? currentWeight / cumulativeWeight : src

var float quadrantEstimate = na
var bool quadrantBullish = false

if quadrantEnable
    // Calculate kernel estimate
    quadrantEstimate := kernel_regression(close, quadrantLookback, quadrantWeight)
    
    // Determine trend direction
    quadrantBullish := not na(quadrantEstimate) and quadrantEstimate > quadrantEstimate[1]
else
    // When disabled, set neutral (allow all trades)
    quadrantBullish := false

// MULTI-TIMEFRAME ZIGZAG CALCULATION
// Simplified MTF ZigZag for directional bias (confirmed bars only)

// Simplified ZigZag calculation for directional bias
simple_zigzag(src, length) =>
    var float zz_high = na
    var float zz_low = na
    var int trend = 0
    var float last_pivot = na
    
    // Calculate pivot highs and lows (confirmed only)
    pivot_high = ta.pivothigh(src, length, length)
    pivot_low = ta.pivotlow(src, length, length)
    
    // Update ZigZag points and trend direction
    if not na(pivot_high) and (na(last_pivot) or pivot_high != last_pivot)
        zz_high := pivot_high
        last_pivot := pivot_high
        trend := 1  // Uptrend (higher high)
    
    if not na(pivot_low) and (na(last_pivot) or pivot_low != last_pivot)
        zz_low := pivot_low
        last_pivot := pivot_low
        trend := -1  // Downtrend (lower low)
    
    [zz_high, zz_low, trend]

var float mtfZigzagHigh = na
var float mtfZigzagLow = na
var int mtfZigzagTrend = 0
var bool mtfZigzagBullish = false

if mtfZigzagEnable
    // Get higher timeframe data (confirmed bars only)
    [htf_high, htf_low, htf_close] = request.security(syminfo.tickerid, mtfTimeframe, [high[1], low[1], close[1]], lookahead=barmerge.lookahead_off)
    
    // Calculate ZigZag on higher timeframe using high/low instead of close
    [zz_h, zz_l, zz_trend] = simple_zigzag(htf_high, mtfZigzagLength)
    
    // Update ZigZag values when new pivots are detected
    if not na(zz_h)
        mtfZigzagHigh := zz_h
        mtfZigzagTrend := zz_trend
    
    if not na(zz_l)
        mtfZigzagLow := zz_l
        mtfZigzagTrend := zz_trend
    
    // Determine trend direction - simplified logic
    // Bullish if trend is up (1), bearish if trend is down (-1)
    mtfZigzagBullish := mtfZigzagTrend == 1
else
    // When disabled, set neutral values
    mtfZigzagBullish := false

// DIRECTIONAL BIAS INTEGRATION
// Apply directional bias filters to entry signals with confluence logic

// Individual Filter Bias Logic (Fixed: disabled filters don't vote)
rbwLongBias = rbwEnable ? (rbwSignal == 2) : true  // If enabled: bullish signal required. If disabled: allow all
rbwShortBias = rbwEnable ? (rbwSignal == 0) : true  // If enabled: bearish signal required. If disabled: allow all

hullLongBias = hullEnable ? hullBullish : true  // If enabled: bullish trend required. If disabled: allow all
hullShortBias = hullEnable ? (not hullBullish) : true  // If enabled: bearish trend required. If disabled: allow all

supertrendLongBias = supertrendEnable ? supertrendBullish : true  // If enabled: bullish trend required. If disabled: allow all
supertrendShortBias = supertrendEnable ? (not supertrendBullish) : true  // If enabled: bearish trend required. If disabled: allow all

quadrantLongBias = quadrantEnable ? quadrantBullish : true  // If enabled: bullish trend required. If disabled: allow all
quadrantShortBias = quadrantEnable ? (not quadrantBullish) : true  // If enabled: bearish trend required. If disabled: allow all

mtfZigzagLongBias = mtfZigzagEnable ? mtfZigzagBullish : true  // If enabled: bullish trend required. If disabled: allow all
mtfZigzagShortBias = mtfZigzagEnable ? (not mtfZigzagBullish) : true  // If enabled: bearish trend required. If disabled: allow all

// Reversal Probability Bias Logic (6th Filter)
// Note: reversalProbLongBias/reversalProbShortBias already calculated above

// Confluence Logic Implementation - UPDATED WITH 6TH FILTER
// Count only enabled filters and their actual signals (now includes reversal probability)
enabledFilters = (rbwEnable ? 1 : 0) + (hullEnable ? 1 : 0) + (supertrendEnable ? 1 : 0) + (quadrantEnable ? 1 : 0) + (mtfZigzagEnable ? 1 : 0) + (reversalProbEnable ? math.round(reversalProbWeight) : 0)

// Count bullish votes from enabled filters only (weighted for reversal probability)
bullishVotes = (rbwEnable and rbwSignal == 2 ? 1 : 0) + 
              (hullEnable and hullBullish ? 1 : 0) + 
              (supertrendEnable and supertrendBullish ? 1 : 0) + 
              (quadrantEnable and quadrantBullish ? 1 : 0) + 
              (mtfZigzagEnable and mtfZigzagBullish ? 1 : 0) +
              (reversalProbEnable and reversalProbLongBias ? math.round(reversalProbWeight) : 0)

// Count bearish votes from enabled filters only (weighted for reversal probability)
bearishVotes = (rbwEnable and rbwSignal == 0 ? 1 : 0) + 
              (hullEnable and not hullBullish ? 1 : 0) + 
              (supertrendEnable and not supertrendBullish ? 1 : 0) + 
              (quadrantEnable and not quadrantBullish ? 1 : 0) + 
              (mtfZigzagEnable and not mtfZigzagBullish ? 1 : 0) +
              (reversalProbEnable and reversalProbShortBias ? math.round(reversalProbWeight) : 0)

// Apply confluence mode - SIMPLIFIED LOGIC
longDirectionalBias = enabledFilters == 0 ? true :  // No filters = allow all
  biasConfluence == 'Any' ? bullishVotes > 0 :  // At least one bullish vote
  biasConfluence == 'Majority' ? bullishVotes >= math.ceil(enabledFilters / 2.0) :  // Majority bullish
  biasConfluence == 'All' ? bullishVotes == enabledFilters : false  // All filters bullish

shortDirectionalBias = enabledFilters == 0 ? true :  // No filters = allow all
  biasConfluence == 'Any' ? bearishVotes > 0 :  // At least one bearish vote
  biasConfluence == 'Majority' ? bearishVotes >= math.ceil(enabledFilters / 2.0) :  // Majority bearish
  biasConfluence == 'All' ? bearishVotes == enabledFilters : false  // All filters bearish

// TREND-RIDING OVERLAY LOGIC
// Advanced exit system to "let winners run" in strong trending conditions

// Variables are now declared at the top of the script to avoid scope issues

// Calculate trend strength for activation
trendStrengthScore = math.max(bullishVotes, bearishVotes)  // Score based on number of agreeing filters

// Determine if trend-riding should activate
trendActivationThreshold = trendActivationMode == 'All Filters' ? enabledFilters : 
  trendActivationMode == 'Majority Filters' ? math.ceil(enabledFilters / 2.0) : trendMinFilters

// Check for trend-riding activation conditions
// Enhanced with optional reversal probability requirement
reversalProbOK = reversalProbRequiredForTrend ? (compositeProbability < reversalProbThreshold) : true

strongTrendDetected = enabledFilters > 0 and 
  (bullishVotes >= trendActivationThreshold or bearishVotes >= trendActivationThreshold) and
  reversalProbOK  // Optional: require low reversal probability for trend activation

// Track consecutive bars of strong trend
if strongTrendDetected
    trendRidingBarsActive := trendRidingBarsActive + 1
else
    trendRidingBarsActive := 0

// Activate trend-riding mode
if trendRidingEnable and not inTrendRidingMode and trendRidingBarsActive >= trendMinBars and strategy.position_size != 0
    inTrendRidingMode := true
    trendRidingStartBar := bar_index
    probInfo = reversalProbRequiredForTrend ? (" | RevProb: " + str.tostring(compositeProbability, "#.#") + "%") : ""
    debugMessage("INFO", "🚀 Trend-Riding Mode ACTIVATED - Bar: " + str.tostring(bar_index) + ", Filters: " + str.tostring(trendStrengthScore) + probInfo, color.green, color.white, 0.05)

// SIGNAL-DRIVEN TREND RIDER EXIT LOGIC
// Revolutionary exit system: Replace price-based exits with signal-based exits

// Count available opposite signals for exit
oppositeSignalsCount = 0
trendRiderLongExit = false
trendRiderShortExit = false

// Check for opposite signals (only count enabled exit signals)
if useExitSignal1 and signal1Enable
    if strategy.position_size > 0 and sig1Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig1Long
        oppositeSignalsCount := oppositeSignalsCount + 1

if useExitSignal2 and signal2Enable
    if strategy.position_size > 0 and sig2Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig2Long
        oppositeSignalsCount := oppositeSignalsCount + 1

if useExitSignal3 and signal3Enable
    if strategy.position_size > 0 and sig3Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig3Long
        oppositeSignalsCount := oppositeSignalsCount + 1

if useExitSignal4 and signal4Enable
    if strategy.position_size > 0 and sig4Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig4Long
        oppositeSignalsCount := oppositeSignalsCount + 1

if useExitSignal5 and signal5Enable
    if strategy.position_size > 0 and sig5Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig5Long
        oppositeSignalsCount := oppositeSignalsCount + 1

// Determine if enough opposite signals are present
signalBasedExitTriggered = oppositeSignalsCount >= minOppositeSignals

// Set specific exit flags for long/short
if strategy.position_size > 0
    trendRiderLongExit := signalBasedExitTriggered
if strategy.position_size < 0
    trendRiderShortExit := signalBasedExitTriggered

// TREND RIDER SAFETY NETS
// Critical safety mechanisms to prevent being stuck in bad trades

// Safety Net 1: Trend Break Detection
trendBreakSafetyTriggered = false
if enableTrendBreakSafety and inTrendRidingMode
    // Check if trend consensus has broken
    trendConsensusBroken = not strongTrendDetected
    if trendConsensusBroken
        trendBreakSafetyTriggered := true
        debugMessage("WARN", "⚠️ Trend Break Safety: Consensus lost, exiting trend-riding mode", color.orange, color.white, 0.15)

// Safety Net 2: Maximum Hold Duration
maxHoldSafetyTriggered = false
if inTrendRidingMode and bar_index - trendRidingStartBar >= trendMaxHold
    maxHoldSafetyTriggered := true
    debugMessage("WARN", "⚠️ Max Hold Safety: " + str.tostring(trendMaxHold) + " bars reached, forcing exit", color.orange, color.white, 0.15)

// Safety Net 3: Catastrophic Stop Loss
catastrophicStopTriggered = false
if enableCatastrophicStop and inTrendRidingMode and strategy.position_size != 0
    // Calculate catastrophic stop distance
    normalStopDistance = atrVal * 3.1  // Normal Smart Profit Locker distance
    catastrophicStopDistance = normalStopDistance * catastrophicStopMultiplier
    
    if strategy.position_size > 0
        catastrophicStopPrice = strategy.position_avg_price - catastrophicStopDistance
        if low <= catastrophicStopPrice
            catastrophicStopTriggered := true
            debugMessage("ERROR", "🛑 Catastrophic Stop: Long position hit " + str.tostring(catastrophicStopMultiplier) + "x stop at " + str.tostring(catastrophicStopPrice), color.red, color.white, 0.25)
    
    if strategy.position_size < 0
        catastrophicStopPrice = strategy.position_avg_price + catastrophicStopDistance
        if high >= catastrophicStopPrice
            catastrophicStopTriggered := true
            debugMessage("ERROR", "🛑 Catastrophic Stop: Short position hit " + str.tostring(catastrophicStopMultiplier) + "x stop at " + str.tostring(catastrophicStopPrice), color.red, color.white, 0.25)

// TREND RIDER EXIT EXECUTION
// Execute exits based on signals or safety nets

// Execute signal-based exits
if inTrendRidingMode and signalBasedExitTriggered
    if strategy.position_size > 0
        strategy.close("Long", comment="Signal Exit", alert_message=longExitMsg)
        debugMessage("INFO", "✅ Signal-Based Exit (Long): " + str.tostring(oppositeSignalsCount) + " opposite signals detected", color.green, color.white, 0.05)
    if strategy.position_size < 0
        strategy.close("Short", comment="Signal Exit", alert_message=shortExitMsg)
        debugMessage("INFO", "✅ Signal-Based Exit (Short): " + str.tostring(oppositeSignalsCount) + " opposite signals detected", color.green, color.white, 0.05)
    inTrendRidingMode := false

// Execute safety net exits
if inTrendRidingMode and (trendBreakSafetyTriggered or maxHoldSafetyTriggered or catastrophicStopTriggered)
    exitReason := trendBreakSafetyTriggered ? "Trend Break" : maxHoldSafetyTriggered ? "Max Hold" : "Catastrophic Stop"
    
    if strategy.position_size > 0
        strategy.close("Long", comment=exitReason, alert_message=longExitMsg)
        debugMessage("WARN", "⚠️ Safety Exit (Long): " + exitReason, color.orange, color.white, 0.15)
    if strategy.position_size < 0
        strategy.close("Short", comment=exitReason, alert_message=shortExitMsg)
        debugMessage("WARN", "⚠️ Safety Exit (Short): " + exitReason, color.orange, color.white, 0.15)
    inTrendRidingMode := false

// Deactivate trend-riding mode when position closes
if inTrendRidingMode and strategy.position_size == 0
    inTrendRidingMode := false
    trendRidingBarsActive := 0
    debugMessage("INFO", "🏁 Trend-Riding Mode DEACTIVATED - Position closed", color.green, color.white, 0.05)



// Define final entry signals with directional bias, Entry Probability Filter, and Bollinger Band Filter applied
longEntrySignal := primaryLongSig and longDirectionalBias and entryProbabilityOK and bbLongFilterOK
shortEntrySignal := primaryShortSig and shortDirectionalBias and entryProbabilityOK and bbShortFilterOK

// Debug warnings when Bollinger Band filter blocks trades (critical risk management)
if debugEnabled and bbEntryFilterEnable
    if primaryLongSig and longDirectionalBias and entryProbabilityOK and not bbLongFilterOK
        debugMessage("WARN", "🚫 BB FILTER BLOCKED LONG: Price " + str.tostring(close, "#.##") + " above upper BB " + str.tostring(bbUpper, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)
    if primaryShortSig and shortDirectionalBias and entryProbabilityOK and not bbShortFilterOK
        debugMessage("WARN", "🚫 BB FILTER BLOCKED SHORT: Price " + str.tostring(close, "#.##") + " below lower BB " + str.tostring(bbLower, "#.##") + " - Gap/spike protection active", color.orange, color.white, 0.15)

// Enhanced debug logging for all directional bias filters and systems
if debugEnabled
    // Always show filter status when debug is enabled
    filterStatusMsg = 'FILTER STATUS:'
    filterStatusMsg += ' RBW=' + (rbwEnable ? (rbwSignal == 2 ? 'BULL' : rbwSignal == 0 ? 'BEAR' : 'NEUTRAL(' + str.tostring(rbwSignal) + ')') : 'OFF')
    filterStatusMsg += ' Hull=' + (hullEnable ? (hullBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(hullMA, '#.##') + ')' : 'OFF')
    filterStatusMsg += ' ST=' + (supertrendEnable ? (supertrendBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(supertrendDirection, '#.#') + ')' : 'OFF')
    filterStatusMsg += ' Quad=' + (quadrantEnable ? (quadrantBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(quadrantEstimate, '#.##') + ')' : 'OFF')
    filterStatusMsg += ' MTF-ZZ=' + (mtfZigzagEnable ? (mtfZigzagBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(mtfZigzagTrend) + ')' : 'OFF')
    filterStatusMsg += ' BB=' + (bbEntryFilterEnable ? ('L:' + (bbLongFilterOK ? 'OK' : 'BLOCK') + ' S:' + (bbShortFilterOK ? 'OK' : 'BLOCK')) : 'OFF')
    debugMessage("INFO", filterStatusMsg, color.green, color.white, 0.05)
    
    // Show confluence calculation with new voting system
    confluenceMsg = 'CONFLUENCE: Mode=' + biasConfluence + ' | Enabled=' + str.tostring(enabledFilters) + ' | BullVotes=' + str.tostring(bullishVotes) + ' | BearVotes=' + str.tostring(bearishVotes)
    confluenceMsg += ' | LongBias=' + (longDirectionalBias ? 'ALLOW' : 'BLOCK') + ' | ShortBias=' + (shortDirectionalBias ? 'ALLOW' : 'BLOCK')
    debugMessage("INFO", confluenceMsg, color.green, color.white, 0.05)
    
    // Show individual filter votes (only when enabled)
    if enabledFilters > 0
        voteDetailMsg = 'FILTER VOTES:'
        if rbwEnable
            voteDetailMsg += ' RBW=' + (rbwSignal == 2 ? 'BULL' : rbwSignal == 0 ? 'BEAR' : 'NEUTRAL')
        if hullEnable
            voteDetailMsg += ' Hull=' + (hullBullish ? 'BULL' : 'BEAR')
        if supertrendEnable
            voteDetailMsg += ' ST=' + (supertrendBullish ? 'BULL' : 'BEAR')
        if quadrantEnable
            voteDetailMsg += ' Quad=' + (quadrantBullish ? 'BULL' : 'BEAR')
        if mtfZigzagEnable
            voteDetailMsg += ' MTF=' + (mtfZigzagBullish ? 'BULL' : 'BEAR')
        debugMessage("INFO", voteDetailMsg, color.green, color.white, 0.05)

// Debug logging for trend-riding system
if debugEnabled and strategy.position_size != 0
    systemMsg = 'Exit Systems:'
    systemMsg += ' TrendRiding=' + (inTrendRidingMode ? 'ACTIVE(' + str.tostring(bar_index - trendRidingStartBar) + 'bars)' : 'OFF')
    debugMessage("INFO", systemMsg, color.blue, color.white, 0.25)

// ═══════════════════ SIGNAL-DRIVEN TREND RIDER: EXIT INTERCEPTION ═══════════════════
// REVOLUTIONARY FEATURE: Intercept and ignore standard exits during trend-riding mode
// This is the core of the Signal-Driven Trend Rider system

// ──────── EXIT INTERCEPTION LOGIC ────────────
// When trend-riding mode is active, we intercept standard exits and ignore them
// Only signal-based exits and safety nets are allowed to close positions

var bool standardExitBlocked = false
standardExitBlocked := inTrendRidingMode



// Trend-riding mode modifications (modify existing exit behavior)
var bool exitOverridden = false
if inTrendRidingMode and strategy.position_size != 0
    // In trend-riding mode, we modify the normal exit logic
    // This is now integrated with Smart Profit Locker above
    exitOverridden := true
    
    if debugEnabled
        trendDuration = bar_index - trendRidingStartBar
        debugMessage("INFO", 'TREND-RIDING ACTIVE: 3x wider stops, 50% offset | Duration: ' + str.tostring(trendDuration) + ' bars | Filters: ' + str.tostring(trendStrengthScore) + '/' + str.tostring(enabledFilters), color.green, color.white, 0.05)

// Visual indicators for new systems
plotchar(inTrendRidingMode, title='Trend-Riding Active', char='T', location=location.top, color=color.purple, size=size.small)
plotchar(mtfZigzagEnable and mtfZigzagBullish, title='MTF-ZZ Bull', char='Z', location=location.belowbar, color=color.aqua, size=size.tiny)
plotchar(mtfZigzagEnable and not mtfZigzagBullish, title='MTF-ZZ Bear', char='Z', location=location.abovebar, color=color.orange, size=size.tiny)


// Probability Filter Visual Indicators - Fixed (Split conditional char parameters)
// Split into separate plots with fixed char parameters to avoid Pine Script errors
probabilityAlert = reversalProbEnable and (compositeProbability >= reversalExitThreshold or probabilityExitTriggered)
plotchar(probabilityAlert, title='Probability Alerts', char='🎯', location=location.top, color=probabilityExitTriggered ? color.red : color.orange, size=size.normal)
// Split entry filter alerts into separate plots with fixed char parameters
entryBlocked = entryProbFilterEnable and not entryProbabilityOK
entryWarning = entryProbFilterEnable and entryProbabilityOK and compositeProbability > entryProbFilterThreshold * 0.8
plotchar(entryBlocked, title='Entry Blocked', char='🚫', location=location.abovebar, color=color.red, size=size.normal)
plotchar(entryWarning, title='Entry Warning', char='⚠', location=location.abovebar, color=color.yellow, size=size.normal)

// Probability level background coloring (subtle)
bgColor = reversalProbEnable ? 
  (compositeProbability >= 90 ? color.new(color.red, 95) : 
   compositeProbability >= 70 ? color.new(color.orange, 95) : 
   compositeProbability <= 20 ? color.new(color.lime, 95) : na) : na
bgplot1 = plot(high, color=na, display=display.none)
bgplot2 = plot(low, color=na, display=display.none)
fill(bgplot1, bgplot2, color=bgColor, title='Probability Zones')

// ──────── 7. CUSTOM EXIT (Always Active) ────────────
if strategy.position_size != 0
    customDistance = 2.5 * atrVal
    
    // Ensure customDistance is valid
    if na(customDistance) or customDistance <= 0
        customDistance := 50.0  // Safe default value in points
    
    if strategy.position_size > 0  // Long position
        stopLevel = strategyEntryPrice - customDistance
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Custom-Long', from_entry='Long', stop=math.max(stopLevel, close * 0.99), comment='Custom Exit')
            if not customExitSent
                customExitSent := true
    
    else if strategy.position_size < 0  // Short position
        stopLevel = strategyEntryPrice + customDistance
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Custom-Short', from_entry='Short', stop=math.max(stopLevel, close * 1.01), comment='Custom Exit')
            if not customExitSent
                customExitSent := true

// Visual aids for active levels
plot(strategy.position_size > 0 and fixedEnable ? strategyEntryPrice - tpCalc(fixedStop) : na, 'Fixed SL', color.red, style=plot.style_linebr)
plot(strategy.position_size > 0 and fixedEnable and tp1Enable ? strategyEntryPrice + tpCalc(tp1Size) : na, 'Fixed TP', color.green, style=plot.style_linebr)

// ─────────────────── 8 · ENHANCED CHART VISUALS WITH BACKTESTING INTEGRATION ─────────────────────────
// Entry signals - simple visualization
plotshape(longEntrySignal, title = 'BUY', style = shape.triangleup, location = location.belowbar, color = color.lime, size = size.small)
plotshape(shortEntrySignal, title = 'SELL', style = shape.triangledown, location = location.abovebar, color = color.red, size = size.small)

// Confluence indicators (when multiple signals agree)
confluenceLong = longSignalCount >= 2
confluenceShort = shortSignalCount >= 2
plotshape(confluenceLong and longEntrySignal, title = 'BUY (Confluence)', style = shape.circle, location = location.belowbar, color = color.aqua, size = size.small)
plotshape(confluenceShort and shortEntrySignal, title = 'SELL (Confluence)', style = shape.circle, location = location.abovebar, color = color.fuchsia, size = size.small)

// Perfect confluence (all enabled signals agree)
perfectConfluenceLong = longSignalCount >= 3 and longEntrySignal
perfectConfluenceShort = shortSignalCount >= 3 and shortEntrySignal
plotshape(perfectConfluenceLong, title = 'BUY (Perfect)', style = shape.square, location = location.belowbar, color = color.yellow, size = size.normal)
plotshape(perfectConfluenceShort, title = 'SELL (Perfect)', style = shape.square, location = location.abovebar, color = color.yellow, size = size.normal)

// RBW Filter visual indicators
plotchar(rbwEnable and rbwSignal == 2 and rbwDesiredCond == "Higher Bandwidth", title = 'RBW Long', char = 'R', location = location.belowbar, color = color.blue, size = size.tiny)
plotchar(rbwEnable and rbwSignal == 0 and rbwDesiredCond == "Higher Bandwidth", title = 'RBW Short', char = 'R', location = location.abovebar, color = color.orange, size = size.tiny)
plotchar(rbwEnable and rbwSignal == 2 and rbwDesiredCond == "Lower Bandwidth", title = 'RBW Short', char = 'R', location = location.abovebar, color = color.orange, size = size.tiny)
plotchar(rbwEnable and rbwSignal == 0 and rbwDesiredCond == "Lower Bandwidth", title = 'RBW Long', char = 'R', location = location.belowbar, color = color.blue, size = size.tiny)

// ──────── OPTIMIZED SIGNAL PLOTTING (Consolidated) ────────────
// Consolidate all signals into fewer plots to stay under 64-plot limit
activeLongSignals = (sig1Long and signal1Enable ? 1 : 0) + (sig2Long and signal2Enable ? 2 : 0) + (sig3Long and signal3Enable ? 3 : 0) + (sig4Long and signal4Enable ? 4 : 0) + (sig5Long and signal5Enable ? 5 : 0)
activeShortSignals = (sig1Short and signal1Enable ? 1 : 0) + (sig2Short and signal2Enable ? 2 : 0) + (sig3Short and signal3Enable ? 3 : 0) + (sig4Short and signal4Enable ? 4 : 0) + (sig5Short and signal5Enable ? 5 : 0)

// Plot consolidated signal markers (plotchar doesn't count toward 64 limit)
plotchar(activeLongSignals > 0, title = 'Long Signals', char = '▲', location = location.belowbar, color = color.new(color.lime, 0), size = size.small)
plotchar(activeShortSignals > 0, title = 'Short Signals', char = '▼', location = location.abovebar, color = color.new(color.red, 0), size = size.small)

// ──────── RBW FILTER OSCILLATOR (Optimized) ────────────
// Consolidate RBW plots to reduce plot count while maintaining visual clarity
plot(rbwEnable ? rbwRelativeBandwidth : na, title="📊 RBW Bandwidth", color=color.new(color.purple, 0), linewidth=2)
// Combine Bollinger Bands into single conditional plot to save plot slots
rbwBandColor = rbwEnable ? (rbwBBUpper > rbwBBMiddle ? color.green : rbwBBLower < rbwBBMiddle ? color.red : color.blue) : na
rbwBandValue = rbwEnable ? (rbwBBUpper != rbwBBMiddle ? rbwBBUpper : rbwBBLower != rbwBBMiddle ? rbwBBLower : rbwBBMiddle) : na
plot(rbwBandValue, title="📊 RBW Bands", color=color.new(rbwBandColor, 50), linewidth=1)
hline(rbwEnable ? 0 : na, title="RBW Zero", color=color.gray, linestyle=hline.style_dotted)

// ═══════════════════ COMPREHENSIVE INDICATOR VISUALIZATION ═══════════════════
// Beautiful, human-readable plotting of all strategy components

// ──────── CORE STRATEGY INDICATORS ────────────
// Moving Average Exit Line (Thick, Color-Coded)
maExitMA = ta.ema(close, 21)  // Using EMA-21 as the primary MA exit
plot(maExitMA, title="📈 MA Exit Line", color=color.new(color.blue, 0), linewidth=3, display=display.all)

// Stop Loss and Profit Lines (Dynamic based on position)
stopLossLine = strategy.position_size > 0 ? strategyEntryPrice - (atrVal * 3.1) : 
               strategy.position_size < 0 ? strategyEntryPrice + (atrVal * 3.1) : na
profitLockerLine = strategy.position_size > 0 ? high - (atrVal * smartProfitVal * (inHybridExitMode ? 1.0 : smartProfitOffset)) :
                   strategy.position_size < 0 ? low + (atrVal * smartProfitVal * (inHybridExitMode ? 1.0 : smartProfitOffset)) : na

plot(stopLossLine, title="🛑 Stop Loss", color=color.new(color.red, 20), linewidth=2, style=plot.style_linebr)
plot(profitLockerLine, title="💰 Profit Locker", color=color.new(color.green, 20), linewidth=2, style=plot.style_linebr)

// ──────── DIRECTIONAL BIAS INDICATORS ────────────
// Hull Suite (when enabled) - Plot the existing hullMA variable
plot(hullEnable ? hullMA : na, title="🌊 Hull Suite", color=hullEnable ? (hullBullish ? color.new(color.lime, 30) : color.new(color.red, 30)) : na, linewidth=2)

// SuperTrend (when enabled) - Plot the existing supertrend variable
plot(supertrendEnable ? supertrend : na, title="⚡ SuperTrend", color=supertrendEnable ? (supertrendBullish ? color.new(color.aqua, 20) : color.new(color.orange, 20)) : na, linewidth=2)

// Quadrant/Nadaraya-Watson (when enabled)
plot(quadrantEnable ? quadrantEstimate : na, title="📊 Quadrant NW", color=quadrantEnable ? (quadrantBullish ? color.new(color.purple, 40) : color.new(color.maroon, 40)) : na, linewidth=2)

// ──────── MTF ZIGZAG VISUALIZATION ────────────
// Plot ZigZag pivots and trend lines when enabled
plot(mtfZigzagEnable and not na(mtfZigzagHigh) ? mtfZigzagHigh : na, title="🔺 ZigZag High", color=color.new(color.red, 0), linewidth=1, style=plot.style_circles)
plot(mtfZigzagEnable and not na(mtfZigzagLow) ? mtfZigzagLow : na, title="🔻 ZigZag Low", color=color.new(color.lime, 0), linewidth=1, style=plot.style_circles)

// ZigZag trend line
zigzagTrendColor = mtfZigzagEnable ? (mtfZigzagBullish ? color.new(color.lime, 70) : color.new(color.red, 70)) : na
plot(mtfZigzagEnable ? close : na, title="📈 ZigZag Trend", color=zigzagTrendColor, linewidth=1, display=display.none)

// ──────── HYBRID MODE TRANSITION MARKERS ────────────
// Visual markers for when hybrid exit mode activates/deactivates
plotshape(inHybridExitMode and not inHybridExitMode[1], title="🔄 Hybrid Mode ON", style=shape.labelup, location=location.belowbar, color=color.new(color.yellow, 0), size=size.normal, text="HYBRID", textcolor=color.black)
plotshape(not inHybridExitMode and inHybridExitMode[1], title="🔄 Hybrid Mode OFF", style=shape.labeldown, location=location.abovebar, color=color.new(color.gray, 0), size=size.normal, text="NORMAL", textcolor=color.white)

// ──────── REVERSAL PROBABILITY DISPLAY ────────────
// Probability Percentage as Chart Indicator
if reversalProbEnable and barstate.islast
    probLabel = label.new(bar_index, high + atrVal, 
                         "🎯 Rev Prob: " + str.tostring(compositeProbability, "#.#") + "%", 
                         style=label.style_label_down, 
                         color=compositeProbability > 70 ? color.red : compositeProbability < 30 ? color.lime : color.orange, 
                         textcolor=color.white, size=size.normal)

// ──────── TREND-RIDING MODE INDICATORS ────────────
// Enhanced visual feedback for trend-riding system
plotshape(inTrendRidingMode and not inTrendRidingMode[1], title="🚀 Trend-Riding ON", style=shape.labelup, location=location.bottom, color=color.new(color.purple, 0), size=size.large, text="TREND", textcolor=color.white)
plotshape(not inTrendRidingMode and inTrendRidingMode[1], title="🚀 Trend-Riding OFF", style=shape.labeldown, location=location.top, color=color.new(color.silver, 0), size=size.normal, text="EXIT", textcolor=color.black)

// ═══════════════════ EXIT INDICATOR VISUAL PLOTTING ═══════════════════
// Visual markers for exit signals and hybrid mode status

// AlgoAlpha Reversal Signals
plotshape(aaBullSignal and exitIndicator1Enable, title='AA Bull Exit', style=shape.labelup, location=location.belowbar, color=color.orange, size=size.small, text='🔄', textcolor=color.white)
plotshape(aaBearSignal and exitIndicator1Enable, title='AA Bear Exit', style=shape.labeldown, location=location.abovebar, color=color.orange, size=size.small, text='🔄', textcolor=color.white)

// External Plot Signals (LuxAlgo Integration Points) - Consolidated
// Consolidate exit plots to reduce plot count while maintaining functionality
anyExitLongSignal = exitPlot1LongSignal or exitPlot2LongSignal or exitPlot3LongSignal
anyExitShortSignal = exitPlot1ShortSignal or exitPlot2ShortSignal or exitPlot3ShortSignal
plotchar(anyExitLongSignal, title='Exit Signals Long', char='🚪', location=location.belowbar, color=color.new(color.blue, 0), size=size.small)
plotchar(anyExitShortSignal, title='Exit Signals Short', char='🚪', location=location.abovebar, color=color.new(color.blue, 0), size=size.small)

// Reversal Candle Patterns - Consolidated (Fixed location parameter)
plotchar(reversalCandleBull, title='Reversal Bull', char='🔄', location=location.belowbar, color=color.lime, size=size.small)
plotchar(reversalCandleBear, title='Reversal Bear', char='🔄', location=location.abovebar, color=color.red, size=size.small)

// Hybrid Exit Mode & Threshold Status - Consolidated (Fixed location parameters)
inHybridModeAny = inHybridExitMode and strategy.position_size != 0
exitThresholdMet = (longExitSignalCount >= hybridExitThreshold and strategy.position_size > 0) or (shortExitSignalCount >= hybridExitThreshold and strategy.position_size < 0)
// Split into separate plots with fixed locations
plotshape(inHybridModeAny and strategy.position_size > 0, title='Hybrid Mode Long', style=shape.diamond, location=location.belowbar, color=color.yellow, size=size.normal)
plotshape(inHybridModeAny and strategy.position_size < 0, title='Hybrid Mode Short', style=shape.diamond, location=location.abovebar, color=color.yellow, size=size.normal)
plotchar(exitThresholdMet and strategy.position_size > 0, title='Exit Threshold Long', char='⚠', location=location.belowbar, color=color.red, size=size.large)
plotchar(exitThresholdMet and strategy.position_size < 0, title='Exit Threshold Short', char='⚠', location=location.abovebar, color=color.red, size=size.large)



// ═══════════════════ PROFESSIONAL STRATEGY ANALYTICS PANEL ═══════════════════
// Strategy-based performance analytics with professional UI design
if showBacktestTable and barstate.isconfirmed
    // Create professional analytics table with enhanced readability
    var table strategyAnalytics = table.new(position.middle_left, 5, 7, bgcolor = color.new(color.black, 5), border_width = 2)

    // Professional headers with consistent sizing and high contrast
    table.cell(strategyAnalytics, 0, 0, '🎯 STRATEGY SIGNAL ANALYTICS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 15))
    table.cell(strategyAnalytics, 1, 0, 'Strategy Win%', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.green, 15))
    table.cell(strategyAnalytics, 2, 0, 'Strategy P&L $', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.green, 15))
    table.cell(strategyAnalytics, 3, 0, 'Max DD $', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.red, 15))
    table.cell(strategyAnalytics, 4, 0, 'Trades', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 15))

    // Global analytics panel variables (declared to avoid scope issues)
    var string health_emoji = '⚪'
    var color health_color = color.gray
    var string action_text = 'WAIT'
    var color action_color = color.gray
    
    // Signal 1 - Strategy-Based Analytics
    if signal1Enable
        // Use actual strategy stats instead of custom tracking
        strategy_trades = strategy.closedtrades
        strategy_wins = strategy.wintrades
        strategy_pnl = strategy.netprofit  // Dollar amount
        strategy_max_dd = strategy.max_drawdown  // Dollar amount
        strategy_contrib = array.get(signal_contributions, 0)
        
        // Calculate strategy-based metrics
        strategy_winrate = strategy_trades > 0 ? strategy_wins / strategy_trades * 100 : 0
        
        // Determine health status (traffic light system) - using dollar amounts
        health_emoji := '🟢'  // Green = Healthy
        health_color := color.lime
        if strategy_pnl < 0 or math.abs(strategy_max_dd) > 1500 or strategy_winrate < 50
            health_emoji := '🔴'  // Red = Unhealthy
            health_color := color.red
        else if strategy_pnl < 500 or math.abs(strategy_max_dd) > 800 or strategy_winrate < 65
            health_emoji := '🟡'  // Yellow = Caution
            health_color := color.yellow
        
        // Determine Keep/Cut recommendation - using dollar amounts
        action_text := 'KEEP ✅'
        action_color := color.lime
        if strategy_pnl < -200 or math.abs(strategy_max_dd) > 2000 or (strategy_trades > 5 and strategy_winrate < 40)
            action_text := 'CUT ❌'
            action_color := color.red
        else if strategy_pnl < 200 or math.abs(strategy_max_dd) > 1000 or strategy_winrate < 55
            action_text := 'TEST ⚠️'
            action_color := color.orange
        
        // Professional table cells with consistent sizing
        table.cell(strategyAnalytics, 0, 1, signal1Name, text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 70))
        table.cell(strategyAnalytics, 1, 1, str.tostring(strategy_winrate, '#.1') + '%', text_color = strategy_winrate >= 65 ? color.lime : strategy_winrate >= 50 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 2, 1, '$' + str.tostring(strategy_pnl, '#'), text_color = strategy_pnl > 0 ? color.lime : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 3, 1, '$' + str.tostring(math.abs(strategy_max_dd), '#'), text_color = math.abs(strategy_max_dd) < 500 ? color.lime : math.abs(strategy_max_dd) < 1000 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 4, 1, str.tostring(strategy_trades, '#'), text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Signal 2 - Strategy-Based Analytics
    if signal2Enable
        strategy_trades = array.get(signal_strategy_trades, 1)
        strategy_wins = array.get(signal_strategy_wins, 1)
        strategy_pnl = array.get(signal_strategy_profits, 1)
        strategy_max_dd = array.get(signal_strategy_drawdowns, 1)
        strategy_winrate = strategy_trades > 0 ? strategy_wins / strategy_trades * 100 : 0
        
        health_emoji := strategy_pnl < 0 or math.abs(strategy_max_dd) > 1500 or strategy_winrate < 50 ? '🔴' : strategy_pnl < 500 or math.abs(strategy_max_dd) > 800 or strategy_winrate < 65 ? '🟡' : '🟢'
        health_color := strategy_pnl < 0 or math.abs(strategy_max_dd) > 1500 or strategy_winrate < 50 ? color.red : strategy_pnl < 500 or math.abs(strategy_max_dd) > 800 or strategy_winrate < 65 ? color.yellow : color.lime
        
        action_text := strategy_pnl < -200 or math.abs(strategy_max_dd) > 2000 or (strategy_trades > 5 and strategy_winrate < 40) ? 'CUT ❌' : strategy_pnl < 200 or math.abs(strategy_max_dd) > 1000 or strategy_winrate < 55 ? 'TEST ⚠️' : 'KEEP ✅'
        action_color := strategy_pnl < -200 or math.abs(strategy_max_dd) > 2000 or (strategy_trades > 5 and strategy_winrate < 40) ? color.red : strategy_pnl < 200 or math.abs(strategy_max_dd) > 1000 or strategy_winrate < 55 ? color.orange : color.lime
        
        table.cell(strategyAnalytics, 0, 2, signal2Name, text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 70))
        table.cell(strategyAnalytics, 1, 2, str.tostring(strategy_winrate, '#.1') + '%', text_color = strategy_winrate >= 65 ? color.lime : strategy_winrate >= 50 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 2, 2, '$' + str.tostring(strategy_pnl, '#'), text_color = strategy_pnl > 0 ? color.lime : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 3, 2, '$' + str.tostring(math.abs(strategy_max_dd), '#'), text_color = math.abs(strategy_max_dd) < 500 ? color.lime : math.abs(strategy_max_dd) < 1000 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
        table.cell(strategyAnalytics, 4, 2, str.tostring(strategy_trades, '#'), text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Signals 3-5 - Strategy-Based Analytics (Streamlined)
    signal_names = array.from(signal3Name, signal4Name, signal5Name)
    signal_enables = array.from(signal3Enable, signal4Enable, signal5Enable)
    
    for i = 2 to 4
        if array.get(signal_enables, i - 2)
            strategy_trades = array.get(signal_strategy_trades, i)
            strategy_wins = array.get(signal_strategy_wins, i)
            strategy_pnl = array.get(signal_strategy_profits, i)
            strategy_max_dd = array.get(signal_strategy_drawdowns, i)
            strategy_winrate = strategy_trades > 0 ? strategy_wins / strategy_trades * 100 : 0
            
            health_emoji := strategy_pnl < 0 or math.abs(strategy_max_dd) > 1500 or strategy_winrate < 50 ? '🔴' : strategy_pnl < 500 or math.abs(strategy_max_dd) > 800 or strategy_winrate < 65 ? '🟡' : '🟢'
            health_color := strategy_pnl < 0 or math.abs(strategy_max_dd) > 1500 or strategy_winrate < 50 ? color.red : strategy_pnl < 500 or math.abs(strategy_max_dd) > 800 or strategy_winrate < 65 ? color.yellow : color.lime
            
            action_text := strategy_pnl < -200 or math.abs(strategy_max_dd) > 2000 or (strategy_trades > 5 and strategy_winrate < 40) ? 'CUT ❌' : strategy_pnl < 200 or math.abs(strategy_max_dd) > 1000 or strategy_winrate < 55 ? 'TEST ⚠️' : 'KEEP ✅'
            action_color := strategy_pnl < -200 or math.abs(strategy_max_dd) > 2000 or (strategy_trades > 5 and strategy_winrate < 40) ? color.red : strategy_pnl < 200 or math.abs(strategy_max_dd) > 1000 or strategy_winrate < 55 ? color.orange : color.lime
            
            table.cell(strategyAnalytics, 0, i + 1, array.get(signal_names, i - 2), text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 70))
            table.cell(strategyAnalytics, 1, i + 1, str.tostring(strategy_winrate, '#.1') + '%', text_color = strategy_winrate >= 65 ? color.lime : strategy_winrate >= 50 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
            table.cell(strategyAnalytics, 2, i + 1, '$' + str.tostring(strategy_pnl, '#'), text_color = strategy_pnl > 0 ? color.lime : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
            table.cell(strategyAnalytics, 3, i + 1, '$' + str.tostring(math.abs(strategy_max_dd), '#'), text_color = math.abs(strategy_max_dd) < 500 ? color.lime : math.abs(strategy_max_dd) < 1000 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.gray, 80))
            table.cell(strategyAnalytics, 4, i + 1, str.tostring(strategy_trades, '#'), text_color = color.white, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Enhanced Strategy Features Summary (Replaces Smart Filter section)
    table.cell(strategyAnalytics, 0, 6, '🚀 STRATEGY FEATURES', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 25), width = 100)
    table.cell(strategyAnalytics, 1, 6, 'BB Filter: ' + (bbEntryFilterEnable ? 'ON' : 'OFF'), text_color = bbEntryFilterEnable ? color.lime : color.red, text_size = size.normal, bgcolor = color.new(color.blue, 35))
    table.cell(strategyAnalytics, 2, 6, 'Trend Riding: ' + (trendRidingEnable ? 'ON' : 'OFF'), text_color = trendRidingEnable ? color.lime : color.red, text_size = size.normal, bgcolor = color.new(color.blue, 35))
    table.cell(strategyAnalytics, 3, 6, 'Filters: ' + str.tostring(enabledFilters) + '/5', text_color = enabledFilters >= 3 ? color.lime : enabledFilters >= 1 ? color.yellow : color.red, text_size = size.normal, bgcolor = color.new(color.blue, 35))
    table.cell(strategyAnalytics, 4, 6, 'Mode: ' + (inTrendRidingMode ? 'TREND' : inHybridExitMode ? 'HYBRID' : 'NORMAL'), text_color = inTrendRidingMode ? color.purple : inHybridExitMode ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.blue, 35))


// ═══════════════════ INTRABAR EXIT DEBUG SYSTEM ═══════════════════
// Visual monitoring and validation system for robust exit logic

debugOn = input.bool(false, '🔍 Enable Exit Debug Labels', group = '🛠️ Debug System', tooltip = 'Show visual labels when exits trigger to validate anti-spam logic')

if debugOn and barstate.isconfirmed
    // Determine which exit method was triggered (if any)
    exitType = 
      maExitSent ? 'MA' :
      fixedExitSent ? 'Fixed' :
      fibExitSent ? 'Fib' :
      trailExitSent ? 'Trail' :
      customExitSent ? 'Custom' : 'None'
    
    // Color coding for different exit types
    labelColor = 
      maExitSent ? color.red : 
      fixedExitSent ? color.orange : 
      fibExitSent ? color.purple : 
      trailExitSent ? color.blue : 
      customExitSent ? color.teal : color.gray
    
    // Show debug label when an exit is triggered
    if exitType != 'None'
        label.new(bar_index, high * 1.02, 'EXIT: ' + exitType + '\nBar: ' + str.tostring(bar_index) + '\nPrice: ' + str.tostring(close, '#.####') + '\nPos: ' + (strategy.position_size > 0 ? 'Long' : strategy.position_size < 0 ? 'Short' : 'Flat') + '\nProgress: ' + (exitInProgress ? 'YES' : 'NO'), color=labelColor, textcolor=color.white, style=label.style_label_down, yloc=yloc.abovebar, size=size.small)
    
    // Show position state changes
    if currentPosition != currentPosition[1]
        stateColor = currentPosition ? color.green : color.red
        stateText = currentPosition ? 'ENTRY' : 'EXIT'
        label.new(bar_index, low * 0.98, stateText + '\nFlags Reset: ' + (currentPosition ? 'YES' : 'NO'), color=stateColor, textcolor=color.white, style=label.style_label_up, yloc=yloc.belowbar, size=size.tiny)

// ──────── STATUS/SETTINGS PANEL (Optional) ────────────
showStatusPanel = input.bool(false, '⚙️ Status/Settings Panel', group = '🛠️ Debug System', tooltip = 'Show comprehensive status and settings for all enabled features')

if showStatusPanel and barstate.islast
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
        smartStatus = inPosition and not inTrendRidingMode ? (inHybridExitMode ? '🟠 TIGHT MODE' : '🟢 ACTIVE') : inPosition ? '🔵 DISABLED (TREND)' : '⚪ STANDBY'
        smartStatusColor = inPosition and not inTrendRidingMode ? (inHybridExitMode ? color.orange : color.lime) : inPosition ? color.blue : color.gray
        smartSettings = str.tostring(smartProfitVal, '#.1') + ' ' + smartProfitType + ', ' + str.tostring(smartProfitOffset * 100, '#.1') + '% PB'
        
        table.cell(statusTable, 0, currentRow, '🔒 Smart Locker', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, smartStatus, text_color = smartStatusColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, smartSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Tight Profit Locker
    if tightProfitType != 'Disabled'
        tightStatus = inPosition and inHybridExitMode ? '🟠 ACTIVE' : inPosition and bbExitTriggered ? '🟠 BB ACTIVE' : '⚪ STANDBY'
        tightStatusColor = inPosition and (inHybridExitMode or bbExitTriggered) ? color.orange : color.gray
        tightSettings = str.tostring(tightProfitVal, '#.1') + ' ' + tightProfitType + ', ' + str.tostring(tightProfitOffset * 100, '#.1') + '% PB'
        
        table.cell(statusTable, 0, currentRow, '🔐 Tight Locker', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(statusTable, 1, currentRow, tightStatus, text_color = tightStatusColor, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(statusTable, 2, currentRow, tightSettings, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        currentRow += 1
    
    // Additional advanced features will be added here once variable names are confirmed
    
    // System Status Row
    table.cell(statusTable, 0, currentRow, '📊 Position', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(statusTable, 1, currentRow, inPosition ? '🟢 IN TRADE' : '⚪ NO POSITION', text_color = inPosition ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.purple, 70))
    table.cell(statusTable, 2, currentRow, 'Size: ' + str.tostring(strategy.position_size), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 70))

// ═══════════════════ MASTER PANEL SYSTEM ═══════════════════
// Real-time strategy status panel for enhanced transparency

// Master Panel Enable Toggle
masterPanelEnable = input.bool(true, '📊 Master Panel', group='🎛️ Master Panel', tooltip='Show real-time strategy status panel with key metrics')
bigPositionDisplay = input.bool(true, '💯 Big Position Display', group='🎛️ Master Panel', tooltip='Show large, bold position indicator for instant recognition')

// Big Bold Position Display
if bigPositionDisplay and barstate.islast
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

// Master Panel Display
if masterPanelEnable and barstate.islast
    var table masterPanel = table.new(position.top_right, 2, 15, bgcolor = color.new(color.black, 15), border_width = 1)
    
    // Header
    table.cell(masterPanel, 0, 0, '🎛️ MASTER PANEL', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 30))
    table.cell(masterPanel, 1, 0, 'STATUS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 30))
    
    // Reversal Probability Row
    if reversalProbEnable
        probColor = compositeProbability > 70 ? color.red : compositeProbability < 30 ? color.lime : color.orange
        table.cell(masterPanel, 0, 1, '🔄 Reversal Prob', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 1, str.tostring(compositeProbability, '#.1') + '%', text_color = probColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Directional Bias Confluence Row
    activeBiasFilters = (rbwEnable ? 1 : 0) + (hullEnable ? 1 : 0) + (supertrendEnable ? 1 : 0) + (quadrantEnable ? 1 : 0) + (mtfZigzagEnable ? 1 : 0) + (reversalProbEnable ? 1 : 0)
    bullishBiasCount = (rbwEnable and rbwLongBias ? 1 : 0) + (hullEnable and hullLongBias ? 1 : 0) + (supertrendEnable and supertrendLongBias ? 1 : 0) + (quadrantEnable and quadrantLongBias ? 1 : 0) + (mtfZigzagEnable and mtfZigzagLongBias ? 1 : 0) + (reversalProbEnable and reversalProbLongBias ? 1 : 0)
    bearishBiasCount = activeBiasFilters - bullishBiasCount
    
    if activeBiasFilters > 0
        biasText = str.tostring(bullishBiasCount) + '/' + str.tostring(activeBiasFilters) + ' Bull'
        biasColor = bullishBiasCount > bearishBiasCount ? color.lime : bullishBiasCount < bearishBiasCount ? color.red : color.orange
        table.cell(masterPanel, 0, 2, '🎯 Bias Vote', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 2, biasText, text_color = biasColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Signal Strength Meter Row
    activeSignals = (signal1Enable ? 1 : 0) + (signal2Enable ? 1 : 0) + (signal3Enable ? 1 : 0) + (signal4Enable ? 1 : 0) + (signal5Enable ? 1 : 0)
    longSignalCount := (signal1Enable and sig1Long ? 1 : 0) + (signal2Enable and sig2Long ? 1 : 0) + (signal3Enable and sig3Long ? 1 : 0) + (signal4Enable and sig4Long ? 1 : 0) + (signal5Enable and sig5Long ? 1 : 0)
    shortSignalCount := (signal1Enable and sig1Short ? 1 : 0) + (signal2Enable and sig2Short ? 1 : 0) + (signal3Enable and sig3Short ? 1 : 0) + (signal4Enable and sig4Short ? 1 : 0) + (signal5Enable and sig5Short ? 1 : 0)
    
    if activeSignals > 0
        maxSignals = math.max(longSignalCount, shortSignalCount)
        signalStrength = maxSignals / activeSignals * 100
        strengthText = str.tostring(signalStrength, '#') + '% (' + str.tostring(maxSignals) + '/' + str.tostring(activeSignals) + ')'
        strengthColor = signalStrength >= 80 ? color.lime : signalStrength >= 60 ? color.yellow : signalStrength >= 40 ? color.orange : color.red
        table.cell(masterPanel, 0, 3, '📊 Signal Power', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 3, strengthText, text_color = strengthColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Volatility Alert Row
    atrCurrent = atrVal
    atrAvg = ta.sma(atrVal, 20)
    volatilityRatio := atrCurrent / atrAvg
    
    if volatilityRatio > 1.5  // Show alert when ATR is 50% above average
        volText = 'HIGH (' + str.tostring(volatilityRatio, '#.##') + 'x)'
        volColor = volatilityRatio > 2.0 ? color.red : color.orange
        table.cell(masterPanel, 0, 4, '⚡ Volatility', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 4, volText, text_color = volColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Mode Status Row
    if trendRidingEnable or hybridExitEnable
        modeText = ''
        modeColor = color.gray
        
        if trendRidingEnable and inTrendRidingMode
            modeText := 'TREND'
            modeColor := color.aqua
        else if hybridExitEnable and inHybridExitMode
            modeText := 'HYBRID'
            modeColor := color.orange
        else
            modeText := 'NORMAL'
            modeColor := color.gray
            
        table.cell(masterPanel, 0, 5, '🚀 Mode', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 5, modeText, text_color = modeColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Exit Proximity Alert Row
    if hybridExitEnable and strategy.position_size != 0
        exitSignalCount = strategy.position_size > 0 ? longExitSignalCount : shortExitSignalCount
        exitText = str.tostring(exitSignalCount) + '/' + str.tostring(hybridExitThreshold)
        exitColor = exitSignalCount >= hybridExitThreshold ? color.red : exitSignalCount >= (hybridExitThreshold - 1) ? color.orange : color.lime
        table.cell(masterPanel, 0, 6, '🚨 Exit Signals', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(masterPanel, 1, 6, exitText, text_color = exitColor, text_size = size.normal, bgcolor = color.new(color.gray, 90))
    
    // Exit System Status Row - NEW COMPREHENSIVE TRACKING
    if strategy.position_size != 0
        exitSystemText = ''
        exitSystemColor = color.gray
        
        // Determine which exit system is currently active
        if inTrendRidingMode and not inHybridExitMode
            // PURE TREND MODE: Signal-based exits only
            exitSystemText := 'SIGNAL-BASED'
            exitSystemColor := color.aqua
        else if inHybridExitMode
            // HYBRID MODE: Tight profit locker active
            exitSystemText := 'TIGHT LOCKER'
            exitSystemColor := color.orange
        else if bbExitTriggered
            // BB EXIT MODE: Tight profit locker from BB extreme
            exitSystemText := 'BB TIGHT'
            exitSystemColor := color.yellow
        else if smartProfitEnable and runSmartProfitLocker
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
        stopDistance = smartProfitVal * atrVal  // Assuming ATR-based stop
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
    if strategy.position_size > 0  // Long position
        current_trade_peak := math.max(current_trade_peak, close)
        current_trade_trough := math.min(current_trade_trough, close)
    else  // Short position
        current_trade_peak := math.max(current_trade_peak, current_trade_entry - close)
        current_trade_trough := math.min(current_trade_trough, current_trade_entry - close)

// Update performance arrays when trades close
if strategy.closedtrades > strategy.closedtrades[1]
    // Calculate trade P&L
    trade_pnl = strategy.position_size[1] > 0 ? (close - current_trade_entry) / current_trade_entry * 100 : (current_trade_entry - close) / current_trade_entry * 100
    trade_won = trade_pnl > 0
    
    // Calculate drawdown during this trade
    trade_drawdown = strategy.position_size[1] > 0 ? (current_trade_peak - current_trade_trough) / current_trade_entry * 100 : current_trade_peak
    
    // Update performance for each signal that contributed to this trade
    for i = 0 to 4
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
            array.set(signal_contributions, i, current_contrib + (trade_pnl * 0.1))  // Weighted contribution
    
    // Reset tracking variables
    current_trade_entry := na
    current_trade_peak := na
    current_trade_trough := na
    for i = 0 to 4
        array.set(current_trade_signals, i, false)
    current_trade_trough := na
    for i = 0 to 4
        array.set(current_trade_signals, i, false)
    current_trade_trough := na
    for i = 0 to 4
        array.set(current_trade_signals, i, false)
    current_trade_trough := na
    for i = 0 to 4
        array.set(current_trade_signals, i, false)
    current_trade_trough := na
    for i = 0 to 4
        array.set(current_trade_signals, i, false)
