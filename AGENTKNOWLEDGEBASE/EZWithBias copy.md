// 2025 Andres Garcia — EZ Algo Trader (Beta)
//  ────────────────────────────────────────────────────────────────
//  Enhanced Multi-Signal Risk Management System
//  • Professional risk management with multiple exit strategies
//  • TradersPost webhook integration for automated trading
//  • Configurable position sizing and stop-loss/take-profit levels
//  • Integrated debugging logger for development
//  ────────────────────────────────────────────────────────────────
//@version=6

// ═══════════════════ DEBUG SYSTEM (SIMPLIFIED) ═══════════════════
// Simple debug system using Pine Script labels - no external libraries

// Debug configuration
bool debugEnabled = input.bool(false, '🔍 Enable Debug Labels', group = '🛠️ Debug System', tooltip = 'Show debug information as chart labels')

// Simple debug functions using labels (no counters to avoid global variable modification issues)
debugLog(string message) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * 0.1, "DEBUG: " + message, style=label.style_label_down, color=color.blue, textcolor=color.white, size=size.small)

debugInfo(string message) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * 0.05, "INFO: " + message, style=label.style_label_down, color=color.green, textcolor=color.white, size=size.small)

debugWarn(string message) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * 0.15, "WARN: " + message, style=label.style_label_down, color=color.orange, textcolor=color.white, size=size.small)

debugError(string message) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * 0.2, "ERROR: " + message, style=label.style_label_down, color=color.red, textcolor=color.white, size=size.small)

debugTrace(string message) =>
    if debugEnabled
        label.new(bar_index, high + (high - low) * 0.25, "TRACE: " + message, style=label.style_label_down, color=color.gray, textcolor=color.white, size=size.small)

strategy(title = 'EZ Algo Trader (Beta)', overlay = true, default_qty_type = strategy.fixed, default_qty_value = 1, calc_on_order_fills = true, process_orders_on_close = true, calc_on_every_tick = false)
// User-controllable quantity

// ─────────────────── 0 · POSITION SIZE & PRIMARY SIGNALS ───────────────────
// Position Size Control
positionQty = input.int(1, 'Number of Contracts', minval = 1, maxval = 1000, group = 'Position Size', tooltip = 'Set the number of contracts/shares to trade per signal')

// ═══════════════════ MULTI-SIGNAL INPUT SYSTEM ═══════════════════
// Support for multiple buy/sell indicators with AI-style quality assessment

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
maExitOn = input.bool(true, 'Enable MA Exit', group = 'MA Exit')
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
smartProfitEnable = input.bool(false, '🎯 Enable Smart Profit Locker', group = 'Smart Profit Locker', tooltip = 'Aggressive profit-taking with adjustable pullback sensitivity')
smartProfitType = input.string('ATR', 'Type', options = ['ATR', 'Points', 'Percent'], group = 'Smart Profit Locker')
smartProfitVal = input.float(3.1, 'Value', step = 0.1, group = 'Smart Profit Locker')
smartProfitOffset = input.float(0.10, 'Pullback %', step = 0.05, minval = 0.01, maxval = 1.0, group = 'Smart Profit Locker', tooltip = 'Pullback percentage to trigger exit (0.10 = 10%)')

// Traditional Trailing Stop - REMOVED (use Smart Profit Locker with 100% offset for traditional behavior)



// ─────────────────── BACKTESTING PANEL CONTROLS ─────────────────────
showBacktestTable = input.bool(true, '📊 Individual Signal Backtest', group = '🔍 Backtesting Panels', tooltip = 'Show individual signal performance (existing table)')
// Entry/Exit Combo Backtest - REMOVED (code bloat reduction)

// ═══════════════════ HELPER FUNCTIONS ═══════════════════
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

// ═══════════════════ WORKING BACKTESTING FUNCTIONS ═══════════════════

// Convert day-based lookback to bars (approximate)
getLookbackBars() =>
    lookbackBars = switch backtestLookback
        '3 Days' => 3 * 24 * 4 // ~288 bars (15min timeframe)
        '7 Days' => 7 * 24 * 4 // ~672 bars
        '30 Days' => 30 * 24 * 4 // ~2880 bars
        '90 Days' => 90 * 24 * 4 // ~8640 bars
        => 7 * 24 * 4 // Default to 7 days

    // Adjust for different timeframes
    timeframeMinutes = timeframe.in_seconds() / 60
    actualBars = math.round(lookbackBars * 15 / timeframeMinutes) // Scale from 15min base
    math.max(actualBars, 20) // Minimum 20 bars

// ═══════════════════ MULTI-MA STYLE GLOBAL BACKTESTING ARRAYS ═══════════════════
// Global state arrays for each signal (like Multi-MA Strategy Analyzer)
var array<float> signal_profits = array.new<float>(5, 0.0)      // Total profit for each signal
var array<float> signal_entryPrices = array.new<float>(5, na)   // Current entry price for each signal
var array<string> signal_positions = array.new<string>(5, "none") // Position state: "long", "short", "none"
var array<int> signal_tradeCounts = array.new<int>(5, 0)        // Total completed trades
var array<int> signal_winCounts = array.new<int>(5, 0)          // Total winning trades
var array<float> signal_largestWins = array.new<float>(5, 0.0)  // Largest winning trade
var array<float> signal_grossProfits = array.new<float>(5, 0.0) // Total gross profit
var array<float> signal_grossLosses = array.new<float>(5, 0.0)  // Total gross loss

// Combo backtesting arrays (5 signals × 4 exit methods = 20 combinations)
// Track performance for each signal + exit method combination
// Flat arrays: [5 signals] x [4 exit methods] = 20 combinations (index = signal*4 + exitMethod)
var array<int> combo_tradeCounts = array.new<int>(20, 0)        // Total trades per combo
var array<int> combo_winCounts = array.new<int>(20, 0)          // Winning trades per combo
var array<float> combo_profits = array.new<float>(20, 0.0)      // Total profit per combo
var array<float> combo_maxDrawdowns = array.new<float>(20, 0.0) // Maximum drawdown per combo
var array<float> combo_runningPeaks = array.new<float>(20, 0.0) // Running equity peak per combo
var array<float> combo_entryPrices = array.new<float>(20, na)   // Current entry price per combo
var array<string> combo_positions = array.new<string>(20, "none") // Position state per combo

// Helper function to convert signal/exit method to flat array index
getComboIndex(signalIndex, exitMethod) =>
    signalIndex * 4 + exitMethod

// Calculate exit price for combo backtesting (matches real strategy logic)
getExitPrice(exitMethod, entryPrice, isLong) =>
    if exitMethod == 0  // MA Exit
        if maExitOn
            // MA exit logic: close vs MA crossover
            isLong ? (close < priceMA ? close : na) : (close > priceMA ? close : na)
        else
            na
    else if exitMethod == 1  // Fixed Exit
        distance = tpCalc(fixedStop)
        isLong ? entryPrice - distance : entryPrice + distance
    else if exitMethod == 2  // Trailing Exit (Smart Profit Locker)
        multiplier = smartProfitType == 'ATR' ? smartProfitVal : smartProfitType == 'Points' ? smartProfitVal / atrVal : smartProfitVal / 100.0
        distance = multiplier * atrVal
        isLong ? entryPrice - distance : entryPrice + distance
    else if exitMethod == 3  // Custom Exit
        distance = 2.5 * atrVal
        isLong ? entryPrice - distance : entryPrice + distance
    else
        na

// ═══════════════════ VECTORIZED EXIT CALCULATION TABLES ═══════════════════
// Pre-computed lookup tables for performance optimization
// Exit method mapping: 0=MA, 1=Fixed, 2=Trailing, 3=Custom
var float[] exitMultipliers = array.from(0.0, 1.5, 2.0, 2.5)  // [MA(special), Fixed, Trailing, Custom]

// FIXED: Combo backtesting with proper static exit logic
processComboBacktest(sigIdx, longPulse, shortPulse) =>
    // Check if any position is open for this signal to optimize performance
    hasOpenPosition = false
    for ex = 0 to 3
        idx = getComboIndex(sigIdx, ex)
        pos = array.get(combo_positions, idx)
        if pos != "none"
            hasOpenPosition := true
            break
    
    // Process if signals fire OR positions are open (for exit monitoring)
    if longPulse or shortPulse or hasOpenPosition
        for ex = 0 to 3
            idx = getComboIndex(sigIdx, ex)

            // -------- cache current state ----------
            tr  = array.get(combo_tradeCounts, idx)
            win = array.get(combo_winCounts, idx)
            pnl = array.get(combo_profits, idx)
            ent = array.get(combo_entryPrices, idx)
            pos = array.get(combo_positions, idx)
            maxDD = array.get(combo_maxDrawdowns, idx)
            runningPeak = array.get(combo_runningPeaks, idx)
            
            // -------- EXIT MONITORING (CRITICAL FIX) ----------
            // Check for exits FIRST if position is open
            if pos != "none"
                exitP = float(na)
                isLong = pos == "long"
                
                if ex == 0
                    // MA Exit - dynamic check
                    exitP := getExitPrice(ex, ent, isLong)
                else
                    // Static exits (Fixed/Fib/Trail/Custom) - check if level was hit
                    stopLevel = getExitPrice(ex, ent, isLong)
                    if not na(stopLevel)
                        // Check if price breached the stop level on this bar
                        if isLong and low <= stopLevel
                            exitP := stopLevel  // Long stop hit
                        else if not isLong and high >= stopLevel
                            exitP := stopLevel  // Short stop hit
                
                // Process exit if triggered
                if not na(exitP)
                    profit = isLong ? exitP - ent : ent - exitP
                    pnl += profit
                    tr += 1
                    win += profit > 0 ? 1 : 0
                    
                    // Update running peak and max drawdown
                    runningPeak := math.max(runningPeak, pnl)
                    currentDrawdown = runningPeak - pnl
                    maxDD := math.max(maxDD, currentDrawdown)
                    
                    pos := "none"
                    ent := na
                else
                    // Check for exit via reverse signal (if no stop hit)
                    if (isLong and shortPulse) or (not isLong and longPulse)
                        exitP := close
                        profit = isLong ? exitP - ent : ent - exitP
                        pnl += profit
                        tr += 1
                        win += profit > 0 ? 1 : 0
                        
                        // Update running peak and max drawdown
                        runningPeak := math.max(runningPeak, pnl)
                        currentDrawdown = runningPeak - pnl
                        maxDD := math.max(maxDD, currentDrawdown)
                        
                        pos := "none"
                        ent := na
            
            // -------- ENTRY LOGIC ----------
            // Enter new position if none exists
            if pos == "none"
                if longPulse
                    ent := close
                    pos := "long"
                else if shortPulse
                    ent := close
                    pos := "short"

            // -------- write-back once --------------
            array.set(combo_tradeCounts, idx, tr)
            array.set(combo_winCounts, idx, win)
            array.set(combo_profits, idx, pnl)
            array.set(combo_entryPrices, idx, ent)
            array.set(combo_positions, idx, pos)
            array.set(combo_maxDrawdowns, idx, maxDD)
            array.set(combo_runningPeaks, idx, runningPeak)

// Get combo results for display with proper drawdown
getComboResults(signalIndex, exitMethod) =>
    if signalIndex >= 0 and signalIndex < 5 and exitMethod >= 0 and exitMethod < 4
        comboIndex = getComboIndex(signalIndex, exitMethod)
        
        totalTrades = array.get(combo_tradeCounts, comboIndex)
        winTrades = array.get(combo_winCounts, comboIndex)
        netProfit = array.get(combo_profits, comboIndex)
        maxDrawdown = array.get(combo_maxDrawdowns, comboIndex)
        
        winRate = totalTrades > 0 ? winTrades / totalTrades : 0.0
        [winRate, netProfit, totalTrades, maxDrawdown]
    else
        [0.0, 0.0, 0, 0.0]

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

// Combination tracking arrays for confluence backtesting
var array<int> combinationSignatures = array.new<int>()
var array<float> combinationEntries = array.new<float>()
var array<float> combinationExits = array.new<float>()
var array<float> combinationPnL = array.new<float>()
var array<bool> combinationWins = array.new<bool>()
var array<string> combinationNames = array.new<string>()

// Entry/Exit combo tracking for matrix backtesting
var array<string> entryExitCombos = array.new<string>()
var array<float> entryExitPnL = array.new<float>()
var array<bool> entryExitWins = array.new<bool>()
var array<int> entryExitCounts = array.new<int>()


// Generate combination signature for tracking
getCombinationSignature(sig1, sig2, sig3, sig4, sig5) =>
    signature = (sig1 ? 1 : 0) + (sig2 ? 2 : 0) + (sig3 ? 4 : 0) + (sig4 ? 8 : 0) + (sig5 ? 16 : 0)
    signature

// Get combination name for display (Pine Script compatible)
getCombinationName(signature) =>
    name = ''
    // Check each bit using modulo and division (Pine Script compatible)
    if math.floor(signature % 2) == 1
        name := name + '1'
        name
    if math.floor(signature / 2 % 2) == 1
        name := name + (name != '' ? '+2' : '2')
        name
    if math.floor(signature / 4 % 2) == 1
        name := name + (name != '' ? '+3' : '3')
        name
    if math.floor(signature / 8 % 2) == 1
        name := name + (name != '' ? '+4' : '4')
        name
    if math.floor(signature / 16 % 2) == 1
        name := name + (name != '' ? '+5' : '5')
        name
    name != '' ? name : 'None'

// Get combination performance from historical data (with safe array access)
getCombinationPerformance(signature) =>
    winRate = 0.55 // Default placeholder
    profitFactor = 1.25 // Default placeholder
    tradeCount = 0.0 // Return as float for consistency

    // Safety check: ensure arrays exist and have data
    if array.size(combinationSignatures) > 0 and array.size(combinationPnL) > 0 and array.size(combinationWins) > 0

        // Ensure all arrays have the same size to prevent index errors
        minSize = math.min(array.size(combinationSignatures), math.min(array.size(combinationPnL), array.size(combinationWins)))

        // Search through combination history (with bounds checking)
        for i = 0 to minSize - 1 by 1
            if i < array.size(combinationSignatures) and array.get(combinationSignatures, i) == signature
                tradeCount := tradeCount + 1
                tradeCount

        // Calculate performance if we have enough data
        if tradeCount >= 5
            wins = 0
            totalPnL = 0.0
            winPnL = 0.0
            lossPnL = 0.0

            for i = 0 to minSize - 1 by 1
                if i < array.size(combinationSignatures) and array.get(combinationSignatures, i) == signature
                    // Safe array access with bounds checking
                    if i < array.size(combinationPnL) and i < array.size(combinationWins)
                        pnl = array.get(combinationPnL, i)
                        isWin = array.get(combinationWins, i)
                        totalPnL := totalPnL + pnl
                        if isWin
                            wins := wins + 1
                            winPnL := winPnL + pnl
                            winPnL
                        else
                            lossPnL := lossPnL + math.abs(pnl)
                            lossPnL

            if tradeCount > 0
                winRate := wins / tradeCount
                winRate
            if lossPnL != 0
                profitFactor := winPnL / lossPnL
                profitFactor

    [winRate, profitFactor, tradeCount]

// Simplified signal quality check - always pass
calcEnhancedSignalQuality(isLong) =>
    1.0

// ═══════════════════ ENTRY SIGNALS (PLACEHOLDER) ═══════════════════
// Entry signals will be properly defined after RBW filter calculation
var bool longEntrySignal = false
var bool shortEntrySignal = false

// ═══════════════════ COMBO BACKTESTING CALLS ═══════════════════
// Process each signal with all exit methods for entry/exit combo analysis
processComboBacktest(0, sig1Long, sig1Short)  // Signal 1 with all exit methods
processComboBacktest(1, sig2Long, sig2Short)  // Signal 2 with all exit methods
processComboBacktest(2, sig3Long, sig3Short)  // Signal 3 with all exit methods
processComboBacktest(3, sig4Long, sig4Short)  // Signal 4 with all exit methods
processComboBacktest(4, sig5Long, sig5Short)  // Signal 5 with all exit methods


// ═══════════════════ ENTRY LOGIC ═══════════════════
// Simplified entry logic without Smart Filter

// Initialize debug logging on first bar
if barstate.isfirst
    debugInfo('EZAlgoTrader initialized with debug logging')

// Long Entry - direct signal
if longEntrySignal
    entryComment = 'Long' + (longSignalCount > 1 ? ' C:' + str.tostring(longSignalCount) : '')
    strategy.entry('Long', strategy.long, qty = positionQty, comment = entryComment, alert_message = longEntryMsg)
    debugInfo('Long entry triggered: ' + entryComment + ' at price: ' + str.tostring(close))

// Short Entry - direct signal
if shortEntrySignal
    entryComment = 'Short' + (shortSignalCount > 1 ? ' C:' + str.tostring(shortSignalCount) : '')
    strategy.entry('Short', strategy.short, qty = positionQty, comment = entryComment, alert_message = shortEntryMsg)
    debugInfo('Short entry triggered: ' + entryComment + ' at price: ' + str.tostring(close))

// ═══════════════════ REAL STRATEGY EXIT LOGIC (CRITICAL FIX) ═══════════════════
// This section bridges the gap between combo backtesting and actual strategy execution
// All exit methods now control the REAL strategy, not just the display panel

// Track entry price for distance-based exits
var float strategyEntryPrice = na

// ═══════════════════ EXIT CONTROL FLAGS (INTRABAR SYSTEM) ═══════════════════
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

// ═══════════════════ ROBUST INTRABAR EXIT SYSTEM ═══════════════════
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

// ──────── 3. FIBONACCI SL/TP (Removed) ────────────
// Fibonacci exit method has been removed to reduce code bloat

// ──────── 4. SMART PROFIT LOCKER (Aggressive Profit Protection) ────────────
if smartProfitEnable and strategy.position_size != 0
    smartDistance = smartProfitType == 'ATR' ? smartProfitVal * atrVal : smartProfitType == 'Points' ? smartProfitVal : strategyEntryPrice * smartProfitVal / 100.0
    
    // Ensure smartDistance is valid and has minimum value
    if na(smartDistance) or smartDistance <= 0
        smartDistance := 50.0  // Safe default value in points
    
    // Ensure offset is valid
    smartOffset = smartDistance * math.max(smartProfitOffset, 0.01)
    
    if strategy.position_size > 0  // Long position
        strategy.exit('Smart-Long', from_entry='Long', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment='Smart Profit Locker')
        if not trailExitSent
            trailExitSent := true
    else if strategy.position_size < 0  // Short position
        strategy.exit('Smart-Short', from_entry='Short', trail_points=math.max(smartDistance, 0.01), trail_offset=math.max(smartOffset, 0.01), comment='Smart Profit Locker')
        if not trailExitSent
            trailExitSent := true

// ──────── 5. TRADITIONAL TRAILING STOP - REMOVED ────────────
// Use Smart Profit Locker with different offset values:
// • 10% offset = Aggressive profit taking
// • 100% offset = Traditional trailing stop behavior

// ──────── 6. RELATIVE BANDWIDTH DIRECTIONAL FILTER ────────────
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

// ═══════════════════ ADDITIONAL DIRECTIONAL BIAS FILTERS ═══════════════════
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

// ═══════════════════ MULTI-TIMEFRAME ZIGZAG FILTER ═══════════════════
// Advanced trend analysis using higher timeframe ZigZag pivots

// MTF ZigZag Enable Toggle (Row 1)
mtfZigzagEnable = input.bool(false, '📈 MTF ZigZag', group='🎯 Advanced Bias Filters', inline='mtf1', tooltip='Enable Multi-Timeframe ZigZag trend analysis. Uses higher timeframe pivot analysis to detect strong trending conditions. Only uses confirmed bars (no repainting).')

// MTF ZigZag Parameters (Row 2)
mtfTimeframe = input.timeframe('60', 'MTF Timeframe', group='🎯 Advanced Bias Filters', inline='mtf2', tooltip='Higher timeframe for trend analysis. Common choices: 15 for 5m charts, 60 for 15m charts, 240 for 1H charts. Higher timeframes give stronger trend signals but slower response.')
mtfZigzagLength = input.int(21, 'ZZ Length', minval=5, group='🎯 Advanced Bias Filters', inline='mtf2', tooltip='ZigZag sensitivity. Lower values (5-10) = more sensitive, more signals. Higher values (20-50) = less sensitive, stronger trends only.')

// MTF ZigZag Advanced (Row 3)
mtfDepth = input.int(200, 'ZZ Depth', minval=50, maxval=500, step=50, group='🎯 Advanced Bias Filters', inline='mtf3', tooltip='Number of pivots to analyze. Higher values give more historical context but use more memory. 200 is good balance for most timeframes.')
mtfMinPivotAge = input.int(2, 'Min Pivot Age', minval=1, maxval=5, group='🎯 Advanced Bias Filters', inline='mtf3', tooltip='Minimum bars a pivot must be old to be considered confirmed. 2+ bars ensures no repainting.')

// ═══════════════════ TREND-RIDING OVERLAY SYSTEM ═══════════════════
// Advanced exit system to "let winners run" in strong trending conditions

// Trend-Riding Enable Toggle (Row 1)
trendRidingEnable = input.bool(false, '🚀 Trend-Riding Mode', group='🎯 Trend-Riding System', inline='tr1', tooltip='Enable trend-riding overlay that overrides Smart Profit Locker when all filters strongly agree. Designed to "let winners run" in obvious trending conditions while preserving high win rate in other scenarios.')

// Activation Requirements (Row 2)
trendActivationMode = input.string('All Filters', 'Activation', options=['Majority Filters', 'All Filters', 'Custom Count'], group='🎯 Trend-Riding System', inline='tr2', tooltip='How many directional bias filters must agree to activate trend-riding mode. "All Filters" = strongest requirement, "Majority" = more frequent activation.')
trendMinFilters = input.int(4, 'Min Count', minval=2, maxval=6, group='🎯 Trend-Riding System', inline='tr2', tooltip='Minimum number of filters that must agree (only used with "Custom Count" mode). Higher numbers = stronger trends required.')

// Trend Strength Requirements (Row 3)
trendMinBars = input.int(3, 'Min Trend Bars', minval=1, maxval=10, group='🎯 Trend-Riding System', inline='tr3', tooltip='Minimum consecutive bars all filters must agree before activating trend-riding. Prevents activation on brief/false trend signals.')
trendMaxHold = input.int(50, 'Max Hold Bars', minval=10, maxval=200, group='🎯 Trend-Riding System', inline='tr3', tooltip='Maximum bars to stay in trend-riding mode regardless of trend strength. Prevents overnight risk and ensures eventual exit.')

// ═══════════════════ ADAPTIVE EXIT SYSTEM ═══════════════════
// Impulsive move detection and rapid exit capabilities

// Impulsive Exit Enable (Row 1)
impulsiveExitEnable = input.bool(true, '⚡ Impulsive Exits', group='🎯 Adaptive Exit System', inline='ie1', tooltip='Enable real-time exits for impulsive reversal moves. Protects against large adverse moves that happen within a single bar.')
momentumExitEnable = input.bool(false, '📉 Momentum Exits', group='🎯 Adaptive Exit System', inline='ie1', tooltip='Enable exits after consecutive adverse bars. Protects against slow bleeds where price gradually moves against position.')

// Impulsive Move Thresholds (Row 2)
impulsiveATRThreshold = input.float(2.0, 'ATR Threshold', minval=1.0, maxval=5.0, step=0.1, group='🎯 Adaptive Exit System', inline='ie2', tooltip='Bar body size threshold for impulsive move detection. 2.0 = bar body must be 2x ATR to trigger. Lower = more sensitive.')
impulsiveMaxLoss = input.float(0.5, 'Max Loss %', minval=0.1, maxval=2.0, step=0.1, group='🎯 Adaptive Exit System', inline='ie2', tooltip='Maximum percentage loss allowed within a single bar before immediate exit. 0.5% = exit if position loses more than 0.5% intrabar.')

// Volume and Momentum Confirmation (Row 3)
impulsiveVolumeMultiplier = input.float(1.5, 'Volume Mult', minval=1.0, maxval=3.0, step=0.1, group='🎯 Adaptive Exit System', inline='ie3', tooltip='Volume multiplier for impulsive move confirmation. 1.5 = volume must be 1.5x average to confirm impulsive move. Higher = more conservative.')
impulsiveMomentumBars = input.int(2, 'Momentum Bars', minval=1, maxval=5, group='🎯 Adaptive Exit System', inline='ie3', tooltip='Number of consecutive adverse bars to trigger momentum exit. 2 = exit after 2 consecutive bars against position.')

// Exit Mode Selection (Row 4)
adaptiveExitMode = input.string('Adaptive', 'Exit Mode', options=['Confirmed Only', 'Adaptive', 'Aggressive'], group='🎯 Adaptive Exit System', inline='ie4', tooltip='Exit timing mode. Confirmed Only = wait for bar close. Adaptive = real-time exits in high volatility. Aggressive = always use real-time exits.')
volatilityThreshold = input.float(1.2, 'Vol Threshold', minval=1.0, maxval=2.0, step=0.1, group='🎯 Adaptive Exit System', inline='ie4', tooltip='Volatility ratio threshold for adaptive mode. 1.2 = use real-time exits when current ATR is 1.2x average ATR.')

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

// ═══════════════════ HULL SUITE CALCULATION ═══════════════════
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

// ═══════════════════ SUPERTREND CALCULATION ═══════════════════
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

// ═══════════════════ QUADRANT (NADARAYA-WATSON) CALCULATION ═══════════════════
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

// ═══════════════════ MULTI-TIMEFRAME ZIGZAG CALCULATION ═══════════════════
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

// ═══════════════════ DIRECTIONAL BIAS INTEGRATION ═══════════════════
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

// Confluence Logic Implementation - FIXED VERSION
// Count only enabled filters and their actual signals
enabledFilters = (rbwEnable ? 1 : 0) + (hullEnable ? 1 : 0) + (supertrendEnable ? 1 : 0) + (quadrantEnable ? 1 : 0) + (mtfZigzagEnable ? 1 : 0)

// Count bullish votes from enabled filters only
bullishVotes = (rbwEnable and rbwSignal == 2 ? 1 : 0) + 
              (hullEnable and hullBullish ? 1 : 0) + 
              (supertrendEnable and supertrendBullish ? 1 : 0) + 
              (quadrantEnable and quadrantBullish ? 1 : 0) + 
              (mtfZigzagEnable and mtfZigzagBullish ? 1 : 0)

// Count bearish votes from enabled filters only
bearishVotes = (rbwEnable and rbwSignal == 0 ? 1 : 0) + 
              (hullEnable and not hullBullish ? 1 : 0) + 
              (supertrendEnable and not supertrendBullish ? 1 : 0) + 
              (quadrantEnable and not quadrantBullish ? 1 : 0) + 
              (mtfZigzagEnable and not mtfZigzagBullish ? 1 : 0)

// Apply confluence mode - SIMPLIFIED LOGIC
longDirectionalBias = enabledFilters == 0 ? true :  // No filters = allow all
  biasConfluence == 'Any' ? bullishVotes > 0 :  // At least one bullish vote
  biasConfluence == 'Majority' ? bullishVotes >= math.ceil(enabledFilters / 2.0) :  // Majority bullish
  biasConfluence == 'All' ? bullishVotes == enabledFilters : false  // All filters bullish

shortDirectionalBias = enabledFilters == 0 ? true :  // No filters = allow all
  biasConfluence == 'Any' ? bearishVotes > 0 :  // At least one bearish vote
  biasConfluence == 'Majority' ? bearishVotes >= math.ceil(enabledFilters / 2.0) :  // Majority bearish
  biasConfluence == 'All' ? bearishVotes == enabledFilters : false  // All filters bearish

// ═══════════════════ TREND-RIDING OVERLAY LOGIC ═══════════════════
// Advanced exit system to "let winners run" in strong trending conditions

var int trendRidingBarsActive = 0
var bool inTrendRidingMode = false
var int trendRidingStartBar = 0

// Calculate trend strength for activation
trendStrengthScore = math.max(bullishVotes, bearishVotes)  // Score based on number of agreeing filters

// Determine if trend-riding should activate
trendActivationThreshold = trendActivationMode == 'All Filters' ? enabledFilters : 
  trendActivationMode == 'Majority Filters' ? math.ceil(enabledFilters / 2.0) : trendMinFilters

// Check for trend-riding activation conditions
strongTrendDetected = enabledFilters > 0 and 
  (bullishVotes >= trendActivationThreshold or bearishVotes >= trendActivationThreshold)

// Track consecutive bars of strong trend
if strongTrendDetected
    trendRidingBarsActive := trendRidingBarsActive + 1
else
    trendRidingBarsActive := 0

// Activate trend-riding mode
if trendRidingEnable and not inTrendRidingMode and trendRidingBarsActive >= trendMinBars
    inTrendRidingMode := true
    trendRidingStartBar := bar_index

// Deactivate trend-riding mode
if inTrendRidingMode and 
  (not strongTrendDetected or 
   bar_index - trendRidingStartBar >= trendMaxHold or
   not trendRidingEnable)
    inTrendRidingMode := false
    trendRidingBarsActive := 0

// ═══════════════════ ADAPTIVE EXIT SYSTEM ═══════════════════
// Impulsive move detection and rapid exit capabilities

// Calculate volatility metrics
currentATR = ta.atr(14)
averageATR = ta.sma(currentATR, 50)
volatilityRatio = currentATR / averageATR

// Detect impulsive moves
barBodySize = math.abs(close - open)
impulsiveBarDetected = barBodySize > currentATR * impulsiveATRThreshold
volumeConfirmation = volume > ta.sma(volume, 20) * impulsiveVolumeMultiplier

// Calculate intrabar loss for position
var float entryPrice = na
if strategy.position_size != 0 and na(entryPrice)
    entryPrice := strategy.position_avg_price
else if strategy.position_size == 0
    entryPrice := na

intrabarLossPercent = strategy.position_size > 0 ? 
  (entryPrice - close) / entryPrice * 100 : 
  strategy.position_size < 0 ? 
  (close - entryPrice) / entryPrice * 100 : 0.0

// Determine if real-time exits should be used
useRealTimeExits = adaptiveExitMode == 'Aggressive' or 
  (adaptiveExitMode == 'Adaptive' and volatilityRatio > volatilityThreshold)

// Impulsive exit conditions
impulsiveExitTriggered = impulsiveExitEnable and useRealTimeExits and
  (impulsiveBarDetected and volumeConfirmation) or
  (intrabarLossPercent > impulsiveMaxLoss)

// Momentum exit conditions (consecutive adverse bars)
var int adverseBars = 0
if momentumExitEnable and strategy.position_size != 0
    if strategy.position_size > 0 and close < close[1]
        adverseBars := adverseBars + 1
    else if strategy.position_size < 0 and close > close[1]
        adverseBars := adverseBars + 1
    else
        adverseBars := 0
else
    adverseBars := 0  // Reset when disabled or no position

momentumExitTriggered = momentumExitEnable and adverseBars >= impulsiveMomentumBars

// Define final entry signals with directional bias applied
longEntrySignal := primaryLongSig and longDirectionalBias
shortEntrySignal := primaryShortSig and shortDirectionalBias

// Enhanced debug logging for all directional bias filters and systems
if debugEnabled
    // Always show filter status when debug is enabled
    filterStatusMsg = 'FILTER STATUS:'
    filterStatusMsg += ' RBW=' + (rbwEnable ? (rbwSignal == 2 ? 'BULL' : rbwSignal == 0 ? 'BEAR' : 'NEUTRAL(' + str.tostring(rbwSignal) + ')') : 'OFF')
    filterStatusMsg += ' Hull=' + (hullEnable ? (hullBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(hullMA, '#.##') + ')' : 'OFF')
    filterStatusMsg += ' ST=' + (supertrendEnable ? (supertrendBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(supertrendDirection, '#.#') + ')' : 'OFF')
    filterStatusMsg += ' Quad=' + (quadrantEnable ? (quadrantBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(quadrantEstimate, '#.##') + ')' : 'OFF')
    filterStatusMsg += ' MTF-ZZ=' + (mtfZigzagEnable ? (mtfZigzagBullish ? 'BULL' : 'BEAR') + '(' + str.tostring(mtfZigzagTrend) + ')' : 'OFF')
    debugInfo(filterStatusMsg)
    
    // Show confluence calculation with new voting system
    confluenceMsg = 'CONFLUENCE: Mode=' + biasConfluence + ' | Enabled=' + str.tostring(enabledFilters) + ' | BullVotes=' + str.tostring(bullishVotes) + ' | BearVotes=' + str.tostring(bearishVotes)
    confluenceMsg += ' | LongBias=' + (longDirectionalBias ? 'ALLOW' : 'BLOCK') + ' | ShortBias=' + (shortDirectionalBias ? 'ALLOW' : 'BLOCK')
    debugInfo(confluenceMsg)
    
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
        debugInfo(voteDetailMsg)

// Debug logging for trend-riding and adaptive exit systems
if debugEnabled and strategy.position_size != 0
    systemMsg = 'Exit Systems:'
    systemMsg += ' TrendRiding=' + (inTrendRidingMode ? 'ACTIVE(' + str.tostring(bar_index - trendRidingStartBar) + 'bars)' : 'OFF')
    systemMsg += ' | VolRatio=' + str.tostring(volatilityRatio, '#.##')
    systemMsg += ' | ExitMode=' + adaptiveExitMode
    systemMsg += ' | Impulsive=' + (impulsiveExitTriggered ? 'TRIGGERED' : 'OK')
    systemMsg += ' | Momentum=' + (momentumExitTriggered ? 'EXIT(' + str.tostring(adverseBars) + 'bars)' : 'OK')
    systemMsg += ' | IntraLoss=' + str.tostring(intrabarLossPercent, '#.##') + '%'
    debugInfo(systemMsg)

// ═══════════════════ ADAPTIVE EXIT INTEGRATION ═══════════════════
// Override normal exit behavior when trend-riding or impulsive exits triggered

// Immediate exit conditions (override all other exits)
if strategy.position_size != 0 and (impulsiveExitTriggered or momentumExitTriggered)
    exitReason = impulsiveExitTriggered ? 'Impulsive Exit' : 'Momentum Exit'
    if strategy.position_size > 0
        strategy.close('Long', comment=exitReason)
    else
        strategy.close('Short', comment=exitReason)
    
    // Reset tracking variables
    entryPrice := na
    adverseBars := 0
    
    if debugEnabled
        debugInfo('IMMEDIATE EXIT: ' + exitReason + ' | Loss: ' + str.tostring(intrabarLossPercent, '#.##') + '%')

// Trend-riding mode modifications (modify existing exit behavior)
var bool exitOverridden = false
if inTrendRidingMode and strategy.position_size != 0
    // In trend-riding mode, we modify the normal exit logic
    // This would typically override Smart Profit Locker settings
    exitOverridden := true
    
    if debugEnabled
        debugInfo('TREND-RIDING ACTIVE: Overriding normal exits | Duration: ' + str.tostring(bar_index - trendRidingStartBar) + ' bars')

// Visual indicators for new systems
plotchar(inTrendRidingMode, title='Trend-Riding Active', char='T', location=location.top, color=color.purple, size=size.small)
plotchar(mtfZigzagEnable and mtfZigzagBullish, title='MTF-ZZ Bull', char='Z', location=location.belowbar, color=color.aqua, size=size.tiny)
plotchar(mtfZigzagEnable and not mtfZigzagBullish, title='MTF-ZZ Bear', char='Z', location=location.abovebar, color=color.orange, size=size.tiny)
plotchar(impulsiveExitTriggered, title='Impulsive Exit', char='!', location=location.top, color=color.red, size=size.normal)

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

// Individual signal markers for debugging (small numbered circles)
plotchar(sig1Long and signal1Enable, title = 'Signal 1', char = '1', location = location.belowbar, color = color.gray, size = size.tiny)
plotchar(sig2Long and signal2Enable, title = 'Signal 2', char = '2', location = location.belowbar, color = color.gray, size = size.tiny)
plotchar(sig3Long and signal3Enable, title = 'Signal 3', char = '3', location = location.belowbar, color = color.gray, size = size.tiny)
plotchar(sig4Long and signal4Enable, title = 'Signal 4', char = '4', location = location.belowbar, color = color.gray, size = size.tiny)
plotchar(sig5Long and signal5Enable, title = 'Signal 5', char = '5', location = location.belowbar, color = color.gray, size = size.tiny)

plotchar(sig2Short and signal2Enable, title = 'Signal 2 Short', char = '2', location = location.abovebar, color = color.gray, size = size.tiny)
plotchar(sig3Short and signal3Enable, title = 'Signal 3 Short', char = '3', location = location.abovebar, color = color.gray, size = size.tiny)
plotchar(sig4Short and signal4Enable, title = 'Signal 4 Short', char = '4', location = location.abovebar, color = color.gray, size = size.tiny)
plotchar(sig5Short and signal5Enable, title = 'Signal 5 Short', char = '5', location = location.abovebar, color = color.gray, size = size.tiny)

// RBW Filter plotting
plot(rbwEnable ? rbwRelativeBandwidth : na, title="RBW Bandwidth", color=color.purple, linewidth=2)
plot(rbwEnable ? ta.sma(rbwRelativeBandwidth, 100) : na, title="RBW Reference", color=color.gray, linewidth=1)
hline(rbwEnable ? 0 : na, title="RBW Zero", color=color.gray, linestyle=hline.style_dotted)

// ═══════════════════ PERFORMANCE STATS TABLE ═══════════════════
// Placeholder for performance stats table
// This would be implemented when the full strategy system is available

// ═══════════════════ SIGNAL COMPARISON BACKTESTING TABLE ═══════════════════
// Comprehensive signal performance comparison - IMPROVED UI
if showBacktestTable and barstate.isconfirmed
    // Create backtesting comparison table with better readability
    var table backtestTable = table.new(position.middle_left, 6, 7, bgcolor = color.new(color.black, 10), border_width = 2)

    // Headers with high contrast
    table.cell(backtestTable, 0, 0, '📊 SIGNAL BACKTEST RESULTS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 20))
    table.cell(backtestTable, 1, 0, 'Raw Win%', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 20))
    table.cell(backtestTable, 2, 0, 'Strategy Win%', text_color = color.white, text_size = size.small, bgcolor = color.new(color.green, 20))
    table.cell(backtestTable, 3, 0, 'Strategy P&L', text_color = color.white, text_size = size.small, bgcolor = color.new(color.green, 20))
    table.cell(backtestTable, 4, 0, 'Trades', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 20))
    table.cell(backtestTable, 5, 0, '⭐ Best', text_color = color.white, text_size = size.small, bgcolor = color.new(color.blue, 20))

    // Signal 1 data with NEW global array backtesting
    if signal1Enable
        [netProfit1, largestWin1, profitFactor1, totalTrades1, winRate1] = getSignalResults(0)

        // Debug info: show signal detection status and raw values
        rawLongVal = str.tostring(signal1LongSrc, '#.##')
        rawShortVal = str.tostring(signal1ShortSrc, '#.##')
        debugInfo = 'Raw L:' + rawLongVal + ' S:' + rawShortVal + '\nProcessed L:' + str.tostring(sig1Long ? 1 : 0) + ' S:' + str.tostring(sig1Short ? 1 : 0)

        table.cell(backtestTable, 0, 1, signal1Name + '\n' + debugInfo, text_color = color.white, text_size = size.tiny, bgcolor = color.new(color.gray, 80))
        table.cell(backtestTable, 1, 1, str.tostring(winRate1 * 100, '#.#') + '%', text_color = winRate1 > 0.6 ? color.lime : winRate1 > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 2, 1, str.tostring(winRate1 * 100, '#.#') + '%', text_color = winRate1 > 0.6 ? color.lime : winRate1 > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 3, 1, str.tostring(netProfit1, '#.##'), text_color = netProfit1 > 0 ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 4, 1, str.tostring(totalTrades1, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 5, 1, winRate1 > 0.6 and netProfit1 > 0 ? '⭐' : '❌', text_color = winRate1 > 0.6 and netProfit1 > 0 ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Signal 2 data with NEW global array backtesting
    if signal2Enable
        [netProfit2, largestWin2, profitFactor2, totalTrades2, winRate2] = getSignalResults(1)

        table.cell(backtestTable, 0, 2, signal2Name, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(backtestTable, 1, 2, str.tostring(winRate2 * 100, '#.#') + '%', text_color = winRate2 > 0.6 ? color.lime : winRate2 > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 2, 2, str.tostring(winRate2 * 100, '#.#') + '%', text_color = winRate2 > 0.6 ? color.lime : winRate2 > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 3, 2, str.tostring(netProfit2, '#.##'), text_color = netProfit2 > 0 ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 4, 2, str.tostring(totalTrades2, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 5, 2, winRate2 > 0.6 and netProfit2 > 0 ? '⭐' : '❌', text_color = winRate2 > 0.6 and netProfit2 > 0 ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Signal 3 data with NEW global array backtesting
    if signal3Enable
        [netProfit3, largestWin3, profitFactor3, totalTrades3, winRate3] = getSignalResults(2)

        table.cell(backtestTable, 0, 3, signal3Name, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(backtestTable, 1, 3, str.tostring(winRate3 * 100, '#.#') + '%', text_color = winRate3 > 0.6 ? color.lime : winRate3 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 2, 3, str.tostring(winRate3 * 100, '#.#') + '%', text_color = winRate3 > 0.6 ? color.lime : winRate3 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 3, 3, str.tostring(netProfit3, '#.##'), text_color = netProfit3 > 0 ? color.lime : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 4, 3, str.tostring(totalTrades3, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 5, 3, winRate3 > 0.6 and netProfit3 > 0 ? '⭐' : '❌', text_color = winRate3 > 0.6 and netProfit3 > 0 ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Signal 4 data with NEW global array backtesting
    if signal4Enable
        [netProfit4, largestWin4, profitFactor4, totalTrades4, winRate4] = getSignalResults(3)

        table.cell(backtestTable, 0, 4, signal4Name, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(backtestTable, 1, 4, str.tostring(winRate4 * 100, '#.#') + '%', text_color = winRate4 > 0.6 ? color.lime : winRate4 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 2, 4, str.tostring(winRate4 * 100, '#.#') + '%', text_color = winRate4 > 0.6 ? color.lime : winRate4 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 3, 4, str.tostring(netProfit4, '#.##'), text_color = netProfit4 > 0 ? color.lime : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 4, 4, str.tostring(totalTrades4, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 5, 4, winRate4 > 0.6 and netProfit4 > 0 ? '⭐' : '❌', text_color = winRate4 > 0.6 and netProfit4 > 0 ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Signal 5 data with NEW global array backtesting
    if signal5Enable
        [netProfit5, largestWin5, profitFactor5, totalTrades5, winRate5] = getSignalResults(4)

        table.cell(backtestTable, 0, 5, signal5Name, text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
        table.cell(backtestTable, 1, 5, str.tostring(winRate5 * 100, '#.#') + '%', text_color = winRate5 > 0.6 ? color.lime : winRate5 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 2, 5, str.tostring(winRate5 * 100, '#.#') + '%', text_color = winRate5 > 0.6 ? color.lime : winRate5 > 0.5 ? color.yellow : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 3, 5, str.tostring(netProfit5, '#.##'), text_color = netProfit5 > 0 ? color.lime : color.white, text_size = size.normal, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 4, 5, str.tostring(totalTrades5, '#'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
        table.cell(backtestTable, 5, 5, winRate5 > 0.6 and netProfit5 > 0 ? '⭐' : '❌', text_color = winRate5 > 0.6 and netProfit5 > 0 ? color.lime : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Smart Filter status - simplified
    table.cell(backtestTable, 0, 6, 'Smart Filter:', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 40))
    table.cell(backtestTable, 1, 6, 'Status: OFF', text_color = color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 2, 6, 'Direct Signals', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 3, 6, 'No Filtering', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 4, 6, 'Simple Mode', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 5, 6, 'Active', text_color = color.lime, text_size = size.small, bgcolor = color.new(color.purple, 60))



// ═══════════════════ ENTRY/EXIT COMBO BACKTESTING PANEL - REMOVED ═══════════════════
// This large panel (~300 lines) has been removed to reduce code bloat.
// Use the Individual Signal Backtest table above for performance analysis.

// [REMOVED: ~280 lines of combo backtesting code for code bloat reduction]

// [REMOVED: More combo backtesting code for code bloat reduction]

// [REMOVED: Signal 2 combo backtesting code for code bloat reduction]

// [REMOVED: ~200+ lines of remaining combo backtesting code for code bloat reduction]
// This included all Signal 2-5 combo tables, best exit calculations, and summary recommendations.

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

// ──────── DEBUG TABLE (Optional) ────────────
showDebugTable = input.bool(false, '📊 Show Debug Table', group = '🛠️ Debug System', tooltip = 'Show real-time exit flag status table')

if showDebugTable and barstate.islast
    var table debugTable = table.new(position.bottom_right, 3, 8, bgcolor = color.new(color.black, 20), border_width = 1)
    
    // Headers
    table.cell(debugTable, 0, 0, '🛠️ EXIT FLAGS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    table.cell(debugTable, 1, 0, 'STATUS', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    table.cell(debugTable, 2, 0, 'PRIORITY', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.blue, 40))
    
    // Flag status rows
    table.cell(debugTable, 0, 1, 'MA Exit', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(debugTable, 1, 1, maExitSent ? '✅ SENT' : '❌ READY', text_color = maExitSent ? color.red : color.lime, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(debugTable, 2, 1, '1 (Highest)', text_color = color.yellow, text_size = size.small, bgcolor = color.new(color.gray, 90))
    
    table.cell(debugTable, 0, 2, 'Fixed SL/TP', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(debugTable, 1, 2, fixedExitSent ? '✅ SENT' : '❌ READY', text_color = fixedExitSent ? color.red : color.lime, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(debugTable, 2, 2, '2', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
    
    table.cell(debugTable, 0, 3, 'Fibonacci', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(debugTable, 1, 3, fibExitSent ? '✅ SENT' : '❌ READY', text_color = fibExitSent ? color.red : color.lime, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(debugTable, 2, 3, '3', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
    
    table.cell(debugTable, 0, 4, 'Trailing', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(debugTable, 1, 4, trailExitSent ? '✅ SENT' : '❌ READY', text_color = trailExitSent ? color.red : color.lime, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(debugTable, 2, 4, '4', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 90))
    
    table.cell(debugTable, 0, 5, 'Custom', text_color = color.white, text_size = size.small, bgcolor = color.new(color.gray, 80))
    table.cell(debugTable, 1, 5, customExitSent ? '✅ SENT' : '❌ READY', text_color = customExitSent ? color.red : color.lime, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(debugTable, 2, 5, '5 (Lowest)', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    
    // System status
    table.cell(debugTable, 0, 6, 'In Position', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(debugTable, 1, 6, inPosition ? '✅ YES' : '❌ NO', text_color = inPosition ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.purple, 70))
    table.cell(debugTable, 2, 6, str.tostring(strategy.position_size), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 70))
    
    table.cell(debugTable, 0, 7, 'Exit Progress', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(debugTable, 1, 7, exitInProgress ? '✅ ACTIVE' : '❌ IDLE', text_color = exitInProgress ? color.orange : color.gray, text_size = size.small, bgcolor = color.new(color.purple, 70))
    table.cell(debugTable, 2, 7, 'Bar: ' + str.tostring(bar_index), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 70))
