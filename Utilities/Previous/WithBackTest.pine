// 2025 Andres Garcia — Multi‑Signal Risk Manager · TradersPost Webhook
//  ────────────────────────────────────────────────────────────────
//  Enhanced Multi-Signal Risk Management System
//  • Professional risk management with multiple exit strategies
//  • TradersPost webhook integration for automated trading
//  • Configurable position sizing and stop-loss/take-profit levels
//  ────────────────────────────────────────────────────────────────
//@version=6
strategy(title = 'Multi‑Signal Risk Manager · Performance Webhooks', overlay = true, default_qty_type = strategy.fixed, default_qty_value = 1, calc_on_order_fills = true, process_orders_on_close = true, calc_on_every_tick = false)
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


// ═══════════════════ HELPER FUNCTIONS ═══════════════════
// Calculate combo index for signal/exit method combination (defined later with other combo functions)

// ═══════════════════ SIGNAL PROCESSING SETUP ═══════════════════
// Legacy compatibility - combine all signals
primaryLongSig = sig1Long or sig2Long or sig3Long or sig4Long or sig5Long
primaryShortSig = sig1Short or sig2Short or sig3Short or sig4Short or sig5Short

// ═══════════════════ COMBO BACKTESTING FUNCTIONS ═══════════════════
// (All combo backtesting functions defined later with complete implementations)

// ═══════════════════ COMBO BACKTESTING CALLS ═══════════════════
// (Function calls moved to after proper function definitions)

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

// ─────────────────── 3B · FIBONACCI STOP LOSS ─────────────────────
fibEnable = input.bool(false, 'Enable Fibonacci SL/TP', group = 'Fibonacci SL/TP')
fibUnit = input.string('ATR', 'Unit', options = ['ATR', 'Points'], group = 'Fibonacci SL/TP', tooltip = 'ATR = Multiple of Average True Range | Points = Absolute price points')

// SIMPLE: One Stop Loss Dropdown
fibSL = input.string('0.618', 'Stop Loss Fib Level', options = ['0.236', '0.382', '0.500', '0.618', '0.786'], group = 'Fibonacci SL/TP', tooltip = 'Select Fibonacci level for stop loss. 0.618 (Golden Ratio) recommended')

// SIMPLE: Three Take Profit Dropdowns
fibTP1Enable = input.bool(false, 'TP1', inline = 'tp1', group = 'Fibonacci SL/TP')
fibTP1 = input.string('1.272', '', inline = 'tp1', options = ['1.000', '1.272', '1.618', '2.000', '2.618'], group = 'Fibonacci SL/TP', tooltip = 'First take profit level. 1.272 recommended')

fibTP2Enable = input.bool(false, 'TP2', inline = 'tp2', group = 'Fibonacci SL/TP')
fibTP2 = input.string('1.618', '', inline = 'tp2', options = ['1.000', '1.272', '1.618', '2.000', '2.618'], group = 'Fibonacci SL/TP', tooltip = 'Second take profit level. 1.618 (Golden Ratio) recommended')

fibTP3Enable = input.bool(false, 'TP3', inline = 'tp3', group = 'Fibonacci SL/TP')
fibTP3 = input.string('2.618', '', inline = 'tp3', options = ['1.000', '1.272', '1.618', '2.000', '2.618'], group = 'Fibonacci SL/TP', tooltip = 'Third take profit level. 2.618 recommended')

// Calculate Fibonacci distance based on unit type
fibCalc(level) =>
    fibUnit == 'ATR' ? level * atrVal : level

// Convert string selections to float values
getFibValue(fibString) =>
    fibString == '0.236' ? 0.236 : fibString == '0.382' ? 0.382 : fibString == '0.500' ? 0.500 : fibString == '0.618' ? 0.618 : fibString == '0.786' ? 0.786 : fibString == '1.000' ? 1.000 : fibString == '1.272' ? 1.272 : fibString == '1.618' ? 1.618 : fibString == '2.000' ? 2.000 : 2.618

trailEnable = input.bool(false, 'Enable Trailing Stop', group = 'Trailing Stop')
trailType = input.string('ATR', 'Type', options = ['ATR', 'Points', 'Percent'], group = 'Trailing Stop')
trailVal = input.float(3.1, 'Value', step = 0.1, group = 'Trailing Stop')

// ═══════════════════ ANTI-WHIPSAW ENHANCEMENTS ═══════════════════
// Smart features to reduce whipsaws and improve trade quality

// ─────────────────── CANDLE STRENGTH FILTER ─────────────────────
candleStrengthEnable = input.bool(false, '🕯️ Skip Trailing on Strong Candles', group = '🚫 Anti-Whipsaw', tooltip = 'Pause trailing stop on strong momentum candles to avoid early exits')
strengthThreshold = input.float(0.7, 'Candle Strength Threshold', step = 0.1, minval = 0.1, maxval = 1.0, group = '🚫 Anti-Whipsaw', tooltip = '0.7 = 70% of candle is body (vs wicks)')
strengthBars = input.int(2, 'Resume After X Weak Bars', minval = 1, maxval = 5, group = '🚫 Anti-Whipsaw', tooltip = 'Resume trailing after this many weak/consolidation bars')

// ─────────────────── SIGNAL SPACING FILTER ─────────────────────
spacingEnable = input.bool(false, '⏱️ Min Bars Between Entries', group = '🚫 Anti-Whipsaw', tooltip = 'Prevent rapid entry/exit cycles')
minSpacingBars = input.int(5, 'Minimum Spacing (Bars)', minval = 1, maxval = 20, group = '🚫 Anti-Whipsaw', tooltip = 'Wait this many bars before allowing new entry')

// ─────────────────── SESSION-AWARE EXITS ─────────────────────
sessionExitsEnable = input.bool(false, '🕐 Session-Aware Exits', group = '⏰ Session Control', tooltip = 'Adapt exit behavior based on session time')
autoExitEnable = input.bool(false, 'Auto-Exit Before Close', group = '⏰ Session Control')
autoExitMinutes = input.int(30, 'Minutes Before Close', minval = 5, maxval = 120, group = '⏰ Session Control')

// ─────────────────── TIME-OF-DAY MULTIPLIERS ─────────────────────
timeMultipliersEnable = input.bool(false, '📊 Time-of-Day Multipliers', group = '⏰ Session Control', tooltip = 'Adjust stop behavior based on market hours')
openingMultiplier = input.float(1.3, 'Opening Hour Multiplier', step = 0.1, group = '⏰ Session Control', tooltip = 'Looser stops during 9:30-11:00 AM')
lunchMultiplier = input.float(0.8, 'Lunch Hour Multiplier', step = 0.1, group = '⏰ Session Control', tooltip = 'Tighter stops during 11:30 AM-1:30 PM')
closingMultiplier = input.float(0.9, 'Closing Hour Multiplier', step = 0.1, group = '⏰ Session Control', tooltip = 'Tighter stops during final 2 hours')

// ─────────────────── BACKTESTING PANEL CONTROLS ─────────────────────
showBacktestTable = input.bool(true, '📊 Individual Signal Backtest', group = '🔍 Backtesting Panels', tooltip = 'Show individual signal performance (existing table)')
showEntryExitBacktest = input.bool(false, '🎯 Entry/Exit Combo Backtest', group = '🔍 Backtesting Panels', tooltip = 'Show entry signal + exit method combinations')

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

// ─────────────────── ENHANCED SMART SIGNAL FILTERING SYSTEM ─────────────────────
// NOTE: Now integrates real backtesting data for intelligent decisions
smartFilterEnable = input.bool(false, '🧠 Enhanced Smart Filter', group = '📊 Smart Filter', tooltip = 'Data-driven filter using real backtest performance')
filterEntries = input.bool(true, 'Filter Entries', group = '📊 Smart Filter', tooltip = 'Apply quality filter to entry signals')
filterExits = input.bool(false, 'Filter Exits', group = '📊 Smart Filter', tooltip = 'Apply quality filter to exit signals')
confluenceWeight = input.float(0.4, 'Confluence Weight', step = 0.1, minval = 0.0, maxval = 1.0, group = '📊 Smart Filter', tooltip = 'Weight for signal combination performance')
performanceWeight = input.float(0.6, 'Performance Weight', step = 0.1, minval = 0.0, maxval = 1.0, group = '📊 Smart Filter', tooltip = 'Weight for individual signal performance')
minQualityScore = input.float(0.3, 'Min Quality Score', step = 0.1, minval = 0.0, maxval = 1.0, group = '📊 Smart Filter', tooltip = 'Block signals below this score (0.3 = 30%)')
// Day-based lookback periods for more practical analysis
backtestLookback = input.string('7 Days', 'Backtest Lookback Period', options = ['3 Days', '7 Days', '30 Days', '90 Days'], group = '🔍 Backtesting Panels', tooltip = 'How far back to analyze performance')
adaptiveThreshold = input.bool(true, 'Adaptive Threshold', group = '📊 Smart Filter', tooltip = 'Auto-adjust quality threshold based on recent performance')
combinationBonus = input.float(0.2, 'Combination Bonus', step = 0.1, minval = 0.0, maxval = 0.5, group = '📊 Smart Filter', tooltip = 'Extra score for historically profitable combinations')

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

// Combo backtesting arrays (5 signals × 5 exit methods = 25 combinations)
// Track performance for each signal + exit method combination
// Flat arrays: [5 signals] x [5 exit methods] = 25 combinations (index = signal*5 + exitMethod)
var array<int> combo_tradeCounts = array.new<int>(25, 0)        // Total trades per combo
var array<int> combo_winCounts = array.new<int>(25, 0)          // Winning trades per combo
var array<float> combo_profits = array.new<float>(25, 0.0)      // Total profit per combo
var array<float> combo_maxDrawdowns = array.new<float>(25, 0.0) // Maximum drawdown per combo
var array<float> combo_runningPeaks = array.new<float>(25, 0.0) // Running equity peak per combo
var array<float> combo_entryPrices = array.new<float>(25, na)   // Current entry price per combo
var array<string> combo_positions = array.new<string>(25, "none") // Position state per combo

// Helper function to convert signal/exit method to flat array index
getComboIndex(signalIndex, exitMethod) =>
    signalIndex * 5 + exitMethod

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
    else if exitMethod == 2  // Fibonacci Exit
        fibLevel = getFibValue(fibSL)
        distance = fibCalc(fibLevel)
        isLong ? entryPrice - distance : entryPrice + distance
    else if exitMethod == 3  // Trailing Exit
        multiplier = trailType == 'ATR' ? trailVal : trailType == 'Points' ? trailVal / atrVal : trailVal / 100.0
        distance = multiplier * atrVal
        isLong ? entryPrice - distance : entryPrice + distance
    else if exitMethod == 4  // Custom Exit
        distance = 2.5 * atrVal
        isLong ? entryPrice - distance : entryPrice + distance
    else
        na

// ═══════════════════ VECTORIZED EXIT CALCULATION TABLES ═══════════════════
// Pre-computed lookup tables for performance optimization
// Exit method mapping: 0=MA, 1=Fixed, 2=Fib, 3=Trail, 4=Custom
var float[] exitMultipliers = array.from(0.0, 1.5, 0.618, 2.0, 2.5)  // [MA(special), Fixed, Fib, Trail, Custom]

// FIXED: Combo backtesting with proper static exit logic
processComboBacktest(sigIdx, longPulse, shortPulse) =>
    // Check if any position is open for this signal to optimize performance
    hasOpenPosition = false
    for ex = 0 to 4
        idx = getComboIndex(sigIdx, ex)
        pos = array.get(combo_positions, idx)
        if pos != "none"
            hasOpenPosition := true
            break
    
    // Process if signals fire OR positions are open (for exit monitoring)
    if longPulse or shortPulse or hasOpenPosition
        for ex = 0 to 4
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
    if signalIndex >= 0 and signalIndex < 5 and exitMethod >= 0 and exitMethod < 5
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

// Performance tracking for Smart Filter
var array<float> recentFilteredOutcomes = array.new<float>()
var array<string> recentFilteredPatterns = array.new<string>()
var array<float> signalPerformanceHistory = array.new<float>()

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

// Enhanced Smart Signal Quality Calculator with real data integration
calcEnhancedSignalQuality(isLong) =>
    if not smartFilterEnable
        1.0 // Always pass if smart filter is disabled
    else // Get current signal pattern
        sig1Active = isLong ? sig1Long : sig1Short
        sig2Active = isLong ? sig2Long : sig2Short
        sig3Active = isLong ? sig3Long : sig3Short
        sig4Active = isLong ? sig4Long : sig4Short
        sig5Active = isLong ? sig5Long : sig5Short

        currentSignature = getCombinationSignature(sig1Active, sig2Active, sig3Active, sig4Active, sig5Active)

        // Enhanced confluence scoring using real performance data
        activeSignals = isLong ? longSignalCount : shortSignalCount
        totalEnabledSignals = (signal1Enable ? 1 : 0) + (signal2Enable ? 1 : 0) + (signal3Enable ? 1 : 0) + (signal4Enable ? 1 : 0) + (signal5Enable ? 1 : 0)
        baseConfluence = totalEnabledSignals > 0 ? activeSignals / totalEnabledSignals : 0.0

        // Get historical performance for this combination
        [comboWinRate, comboProfitFactor, comboTradeCount] = getCombinationPerformance(currentSignature)

        // Performance multiplier based on historical data
        performanceMultiplier = comboWinRate > 0.6 ? 1.3 : comboWinRate > 0.5 ? 1.1 : comboWinRate < 0.4 ? 0.7 : 1.0

        enhancedConfluenceScore = baseConfluence * performanceMultiplier

        // Individual signal performance scoring
        performanceScore = 0.5 // Default neutral, will be enhanced with real data

        // Combination bonus for historically profitable patterns
        bonusScore = comboTradeCount >= 10 and comboWinRate > 0.55 ? combinationBonus : 0.0

        // Combined quality score
        qualityScore = enhancedConfluenceScore * confluenceWeight + performanceScore * performanceWeight + bonusScore

        // Adaptive threshold adjustment
        finalThreshold = minQualityScore
        if adaptiveThreshold and array.size(recentFilteredOutcomes) >= 20
            recentWinRate = 0.0
            recentCount = math.min(50, array.size(recentFilteredOutcomes))
            wins = 0

            for i = array.size(recentFilteredOutcomes) - recentCount to array.size(recentFilteredOutcomes) - 1 by 1
                if array.get(recentFilteredOutcomes, i) > 0
                    wins := wins + 1
                    wins

            recentWinRate := wins / recentCount

            // Adjust threshold based on recent performance
            finalThreshold := recentWinRate > 0.65 ? minQualityScore * 0.9 : recentWinRate > 0.55 ? minQualityScore : recentWinRate < 0.45 ? minQualityScore * 1.1 : minQualityScore * 1.2
            finalThreshold

        // Return both quality score and whether it passes the adaptive threshold
        qualityScore

// Calculate enhanced quality scores for current signals
longQualityScore = calcEnhancedSignalQuality(true)
shortQualityScore = calcEnhancedSignalQuality(false)

// Get adaptive threshold for filtering
currentThreshold = minQualityScore
if adaptiveThreshold and array.size(recentFilteredOutcomes) >= 20
    recentWinRate = 0.0
    recentCount = math.min(50, array.size(recentFilteredOutcomes))
    wins = 0

    for i = array.size(recentFilteredOutcomes) - recentCount to array.size(recentFilteredOutcomes) - 1 by 1
        if array.get(recentFilteredOutcomes, i) > 0
            wins := wins + 1
            wins

    recentWinRate := wins / recentCount
    currentThreshold := recentWinRate > 0.65 ? minQualityScore * 0.9 : recentWinRate > 0.55 ? minQualityScore : recentWinRate < 0.45 ? minQualityScore * 1.1 : minQualityScore * 1.2
    currentThreshold

// Apply enhanced smart filter to signals
qualityFilteredLongSig = primaryLongSig and (not filterEntries or not smartFilterEnable or longQualityScore >= currentThreshold)
qualityFilteredShortSig = primaryShortSig and (not filterExits or not smartFilterEnable or shortQualityScore >= currentThreshold)

// Track combination data for backtesting when signals fire
if qualityFilteredLongSig or qualityFilteredShortSig
    currentSignature = getCombinationSignature(sig1Long or sig1Short, sig2Long or sig2Short, sig3Long or sig3Short, sig4Long or sig4Short, sig5Long or sig5Short)
    combinationName = getCombinationName(currentSignature)

    // Store entry data for later exit tracking
    array.push(combinationSignatures, currentSignature)
    array.push(combinationEntries, close)
    array.push(combinationNames, combinationName)
    // --- ADD THESE TWO LINES ---
    array.push(combinationPnL, 0.0)          // placeholder
    array.push(combinationWins, false)        // placeholder

    // Limit array size to prevent memory issues
    if array.size(combinationSignatures) > 500
        array.shift(combinationSignatures)
        array.shift(combinationEntries)
        array.shift(combinationNames)
        array.shift(combinationPnL)
        array.shift(combinationWins)

// ═══════════════════ COMBO BACKTESTING CALLS ═══════════════════
// Process each signal with all exit methods for entry/exit combo analysis
processComboBacktest(0, sig1Long, sig1Short)  // Signal 1 with all exit methods
processComboBacktest(1, sig2Long, sig2Short)  // Signal 2 with all exit methods
processComboBacktest(2, sig3Long, sig3Short)  // Signal 3 with all exit methods
processComboBacktest(3, sig4Long, sig4Short)  // Signal 4 with all exit methods
processComboBacktest(4, sig5Long, sig5Short)  // Signal 5 with all exit methods


// ═══════════════════ ENHANCED ENTRY LOGIC ═══════════════════
// Entry logic with AI quality filter

// Long Entry - with AI quality filter
if qualityFilteredLongSig
    entryComment = 'Long' + (smartFilterEnable ? ' Q:' + str.tostring(longQualityScore, '#.##') : '') + (longSignalCount > 1 ? ' C:' + str.tostring(longSignalCount) : '')
    strategy.entry('Long', strategy.long, qty = positionQty, comment = entryComment, alert_message = longEntryMsg)

// Short Entry - with AI quality filter
if qualityFilteredShortSig
    entryComment = 'Short' + (smartFilterEnable ? ' Q:' + str.tostring(shortQualityScore, '#.##') : '') + (shortSignalCount > 1 ? ' C:' + str.tostring(shortSignalCount) : '')
    strategy.entry('Short', strategy.short, qty = positionQty, comment = entryComment, alert_message = shortEntryMsg)

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
        stopLevel = strategyEntryPrice - stopDistance
        profitLevel = not na(profitDistance) ? strategyEntryPrice + profitDistance : na
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Fixed-Long', from_entry='Long', stop=stopLevel, limit=profitLevel, comment='Fixed SL/TP')
            if not fixedExitSent
                fixedExitSent := true
    
    else if strategy.position_size < 0  // Short position
        stopLevel = strategyEntryPrice + stopDistance
        profitLevel = not na(profitDistance) ? strategyEntryPrice - profitDistance : na
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Fixed-Short', from_entry='Short', stop=stopLevel, limit=profitLevel, comment='Fixed SL/TP')
            if not fixedExitSent
                fixedExitSent := true

// ──────── 3. FIBONACCI SL/TP (Always Active) ────────────
if fibEnable and not na(strategyEntryPrice) and strategy.position_size != 0
    fibStopValue = getFibValue(fibSL)
    stopDistance = fibCalc(fibStopValue)
    profitDistance = fibTP1Enable ? fibCalc(getFibValue(fibTP1)) : na
    
    // Ensure distances are valid
    if na(stopDistance) or stopDistance <= 0
        stopDistance := 0.01  // Safe default
    
    if strategy.position_size > 0  // Long position
        stopLevel = strategyEntryPrice - stopDistance
        profitLevel = not na(profitDistance) ? strategyEntryPrice + profitDistance : na
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Fib-Long', from_entry='Long', stop=stopLevel, limit=profitLevel, comment='Fib SL/TP')
            if not fibExitSent
                fibExitSent := true
    
    else if strategy.position_size < 0  // Short position
        stopLevel = strategyEntryPrice + stopDistance
        profitLevel = not na(profitDistance) ? strategyEntryPrice - profitDistance : na
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Fib-Short', from_entry='Short', stop=stopLevel, limit=profitLevel, comment='Fib SL/TP')
            if not fibExitSent
                fibExitSent := true

// ──────── 4. TRAILING STOP (Always Active) ────────────
if trailEnable and strategy.position_size != 0
    trailDistance = trailType == 'ATR' ? trailVal * atrVal : trailType == 'Points' ? trailVal : strategyEntryPrice * trailVal / 100.0
    
    // Ensure trailDistance is valid
    if na(trailDistance) or trailDistance <= 0
        trailDistance := 50.0  // Safe default value in points
    
    if strategy.position_size > 0  // Long position
        strategy.exit('Trail-Long', from_entry='Long', trail_points=trailDistance, trail_offset=trailDistance * 0.1, comment='Trailing Stop')
        if not trailExitSent
            trailExitSent := true
    else if strategy.position_size < 0  // Short position
        strategy.exit('Trail-Short', from_entry='Short', trail_points=trailDistance, trail_offset=trailDistance * 0.1, comment='Trailing Stop')
        if not trailExitSent
            trailExitSent := true

// ──────── 5. CUSTOM EXIT (Always Active) ────────────
if strategy.position_size != 0
    customDistance = 2.5 * atrVal
    
    // Ensure customDistance is valid
    if na(customDistance) or customDistance <= 0
        customDistance := 0.01  // Safe default value (1 cent)
    
    if strategy.position_size > 0  // Long position
        stopLevel = strategyEntryPrice - customDistance
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Custom-Long', from_entry='Long', stop=stopLevel, comment='Custom Exit')
            if not customExitSent
                customExitSent := true
    
    else if strategy.position_size < 0  // Short position
        stopLevel = strategyEntryPrice + customDistance
        if not na(stopLevel) and stopLevel > 0
            strategy.exit('Custom-Short', from_entry='Short', stop=stopLevel, comment='Custom Exit')
            if not customExitSent
                customExitSent := true

// Visual aids for active levels
plot(strategy.position_size > 0 and fixedEnable ? strategyEntryPrice - tpCalc(fixedStop) : na, 'Fixed SL', color.red, style=plot.style_linebr)
plot(strategy.position_size > 0 and fixedEnable and tp1Enable ? strategyEntryPrice + tpCalc(tp1Size) : na, 'Fixed TP', color.green, style=plot.style_linebr)

// ─────────────────── 8 · ENHANCED CHART VISUALS WITH BACKTESTING INTEGRATION ─────────────────────────
// Enhanced entry signals with quality indicators and combination feedback

// High-quality signals (passed Smart Filter)
plotshape(qualityFilteredLongSig, title = 'BUY (Quality)', style = shape.triangleup, location = location.belowbar, color = color.lime, size = size.small)
plotshape(qualityFilteredShortSig, title = 'SELL (Quality)', style = shape.triangledown, location = location.abovebar, color = color.red, size = size.small)

// Blocked signals (failed Smart Filter)
blockedLongSig = primaryLongSig and smartFilterEnable and filterEntries and longQualityScore < currentThreshold
blockedShortSig = primaryShortSig and smartFilterEnable and filterExits and shortQualityScore < currentThreshold
plotshape(blockedLongSig, title = 'BUY (Blocked)', style = shape.triangleup, location = location.belowbar, color = color.orange, size = size.tiny)
plotshape(blockedShortSig, title = 'SELL (Blocked)', style = shape.triangledown, location = location.abovebar, color = color.orange, size = size.tiny)

// Confluence indicators (when multiple signals agree)
confluenceLong = longSignalCount >= 2
confluenceShort = shortSignalCount >= 2
plotshape(confluenceLong and qualityFilteredLongSig, title = 'BUY (Confluence)', style = shape.circle, location = location.belowbar, color = color.aqua, size = size.small)
plotshape(confluenceShort and qualityFilteredShortSig, title = 'SELL (Confluence)', style = shape.circle, location = location.abovebar, color = color.fuchsia, size = size.small)

// Perfect confluence (all enabled signals agree)
perfectConfluenceLong = longSignalCount >= 3 and qualityFilteredLongSig
perfectConfluenceShort = shortSignalCount >= 3 and qualityFilteredShortSig
plotshape(perfectConfluenceLong, title = 'BUY (Perfect)', style = shape.square, location = location.belowbar, color = color.yellow, size = size.normal)
plotshape(perfectConfluenceShort, title = 'SELL (Perfect)', style = shape.square, location = location.abovebar, color = color.yellow, size = size.normal)

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

    // Smart Signal Filter info with improved readability
    table.cell(backtestTable, 0, 6, '🧠 Enhanced Smart Filter:', text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 40))
    table.cell(backtestTable, 1, 6, 'Long: ' + str.tostring(longQualityScore, '#.##'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 2, 6, 'Short: ' + str.tostring(shortQualityScore, '#.##'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 3, 6, 'Threshold: ' + str.tostring(currentThreshold, '#.##'), text_color = color.white, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 4, 6, 'Filter: ' + (smartFilterEnable ? 'ON' : 'OFF'), text_color = smartFilterEnable ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
    table.cell(backtestTable, 5, 6, 'Adaptive: ' + (adaptiveThreshold ? 'ON' : 'OFF'), text_color = adaptiveThreshold ? color.lime : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))



// ═══════════════════ ENTRY/EXIT COMBO BACKTESTING PANEL ═══════════════════
if showEntryExitBacktest and barstate.isconfirmed
    var table entryExitTable = table.new(position.middle_right, 7, 9, bgcolor = color.new(color.purple, 10), border_width = 2)

    // Headers with larger text size
    table.cell(entryExitTable, 0, 0, '🎯 ENTRY/EXIT COMBOS', text_color = color.white, text_size = size.large, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 1, 0, 'MA Exit', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 2, 0, 'Fixed SL/TP', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 3, 0, 'Fibonacci', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 4, 0, 'Trailing', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 5, 0, 'Custom', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))
    table.cell(entryExitTable, 6, 0, '⭐ Best', text_color = color.white, text_size = size.normal, bgcolor = color.new(color.purple, 20))

    // Entry/Exit matrix data - ALWAYS show all 5 signals
    matrixRow = 1

    // Signal 1 entry combinations (always show)
    signalDisplayName1 = signal1Enable ? signal1Name : signal1Name + ' (OFF)'
    table.cell(entryExitTable, 0, matrixRow, signalDisplayName1, text_color = signal1Enable ? color.white : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Real performance data from combo backtesting
    // Get actual results for Signal 1 (index 0) with each exit method
    [maExitWinRate, maExitProfit, maExitTrades, maExitDrawdown] = getComboResults(0, 0)     // MA Exit
    [fixedExitWinRate, fixedExitProfit, fixedExitTrades, fixedExitDrawdown] = getComboResults(0, 1)  // Fixed SL/TP
    [fibExitWinRate, fibExitProfit, fibExitTrades, fibExitDrawdown] = getComboResults(0, 2)   // Fibonacci
    [trailExitWinRate, trailExitProfit, trailExitTrades, trailExitDrawdown] = getComboResults(0, 3)  // Trailing
    [customExitWinRate, customExitProfit, customExitTrades, customExitDrawdown] = getComboResults(0, 4) // Custom
    
    // Display stacked metrics: Win Rate + PNL + Drawdown with smaller text for better readability
    table.cell(entryExitTable, 1, matrixRow, formatStackedCell(maExitWinRate, maExitProfit, maExitTrades, maExitDrawdown, signal1Enable), text_color = signal1Enable and maExitTrades > 0 ? (maExitWinRate > 0.6 ? color.lime : maExitWinRate > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 2, matrixRow, formatStackedCell(fixedExitWinRate, fixedExitProfit, fixedExitTrades, fixedExitDrawdown, signal1Enable), text_color = signal1Enable and fixedExitTrades > 0 ? (fixedExitWinRate > 0.6 ? color.lime : fixedExitWinRate > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 3, matrixRow, formatStackedCell(fibExitWinRate, fibExitProfit, fibExitTrades, fibExitDrawdown, signal1Enable), text_color = signal1Enable and fibExitTrades > 0 ? (fibExitWinRate > 0.6 ? color.lime : fibExitWinRate > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 4, matrixRow, formatStackedCell(trailExitWinRate, trailExitProfit, trailExitTrades, trailExitDrawdown, signal1Enable), text_color = signal1Enable and trailExitTrades > 0 ? (trailExitWinRate > 0.6 ? color.lime : trailExitWinRate > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 5, matrixRow, formatStackedCell(customExitWinRate, customExitProfit, customExitTrades, customExitDrawdown, signal1Enable), text_color = signal1Enable and customExitTrades > 0 ? (customExitWinRate > 0.6 ? color.lime : customExitWinRate > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Calculate best exit method dynamically based on real data
    bestExitMethod = 'N/A'
    bestWinRate = 0.0
    if signal1Enable
        bestExitMethod := 'MA'
        bestWinRate := maExitWinRate
        if fixedExitWinRate > bestWinRate
            bestExitMethod := 'Fixed'
            bestWinRate := fixedExitWinRate
        if fibExitWinRate > bestWinRate
            bestExitMethod := 'Fib'
            bestWinRate := fibExitWinRate
        if trailExitWinRate > bestWinRate
            bestExitMethod := 'Trail'
            bestWinRate := trailExitWinRate
        if customExitWinRate > bestWinRate
            bestExitMethod := 'Custom'
            bestWinRate := customExitWinRate

    table.cell(entryExitTable, 6, matrixRow, bestExitMethod != 'N/A' ? bestExitMethod + ' ⭐' : 'N/A', text_color = bestExitMethod != 'N/A' ? color.lime : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 90))

    matrixRow := matrixRow + 1

    // Signal 2 entry combinations (always show)
    signalDisplayName2 = signal2Enable ? signal2Name : signal2Name + ' (OFF)'
    table.cell(entryExitTable, 0, matrixRow, signalDisplayName2, text_color = signal2Enable ? color.white : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Real performance data from combo backtesting for Signal 2
    [maExitWinRate2, maExitProfit2, maExitTrades2, maExitDrawdown2] = getComboResults(1, 0)     // MA Exit
    [fixedExitWinRate2, fixedExitProfit2, fixedExitTrades2, fixedExitDrawdown2] = getComboResults(1, 1)  // Fixed SL/TP
    [fibExitWinRate2, fibExitProfit2, fibExitTrades2, fibExitDrawdown2] = getComboResults(1, 2)   // Fibonacci
    [trailExitWinRate2, trailExitProfit2, trailExitTrades2, trailExitDrawdown2] = getComboResults(1, 3)  // Trailing
    [customExitWinRate2, customExitProfit2, customExitTrades2, customExitDrawdown2] = getComboResults(1, 4) // Custom

    table.cell(entryExitTable, 1, matrixRow, formatStackedCell(maExitWinRate2, maExitProfit2, maExitTrades2, maExitDrawdown2, signal2Enable), text_color = signal2Enable and maExitTrades2 > 0 ? (maExitWinRate2 > 0.6 ? color.lime : maExitWinRate2 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 2, matrixRow, formatStackedCell(fixedExitWinRate2, fixedExitProfit2, fixedExitTrades2, fixedExitDrawdown2, signal2Enable), text_color = signal2Enable and fixedExitTrades2 > 0 ? (fixedExitWinRate2 > 0.6 ? color.lime : fixedExitWinRate2 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 3, matrixRow, formatStackedCell(fibExitWinRate2, fibExitProfit2, fibExitTrades2, fibExitDrawdown2, signal2Enable), text_color = signal2Enable and fibExitTrades2 > 0 ? (fibExitWinRate2 > 0.6 ? color.lime : fibExitWinRate2 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 4, matrixRow, formatStackedCell(trailExitWinRate2, trailExitProfit2, trailExitTrades2, trailExitDrawdown2, signal2Enable), text_color = signal2Enable and trailExitTrades2 > 0 ? (trailExitWinRate2 > 0.6 ? color.lime : trailExitWinRate2 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 5, matrixRow, formatStackedCell(customExitWinRate2, customExitProfit2, customExitTrades2, customExitDrawdown2, signal2Enable), text_color = signal2Enable and customExitTrades2 > 0 ? (customExitWinRate2 > 0.6 ? color.lime : customExitWinRate2 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Calculate best exit method dynamically based on real data
    bestExitMethod2 = 'N/A'
    bestWinRate2 = 0.0
    if signal2Enable
        bestExitMethod2 := 'MA'
        bestWinRate2 := maExitWinRate2
        if fixedExitWinRate2 > bestWinRate2
            bestExitMethod2 := 'Fixed'
            bestWinRate2 := fixedExitWinRate2
        if fibExitWinRate2 > bestWinRate2
            bestExitMethod2 := 'Fib'
            bestWinRate2 := fibExitWinRate2
        if trailExitWinRate2 > bestWinRate2
            bestExitMethod2 := 'Trail'
            bestWinRate2 := trailExitWinRate2
        if customExitWinRate2 > bestWinRate2
            bestExitMethod2 := 'Custom'
            bestWinRate2 := customExitWinRate2
    
    table.cell(entryExitTable, 6, matrixRow, bestExitMethod2 != 'N/A' ? bestExitMethod2 + ' ⭐' : 'N/A', text_color = bestExitMethod2 != 'N/A' ? color.lime : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 90))

    matrixRow := matrixRow + 1

    // Signal 3 entry combinations (always show)
    signalDisplayName3 = signal3Enable ? signal3Name : signal3Name + ' (OFF)'
    table.cell(entryExitTable, 0, matrixRow, signalDisplayName3, text_color = signal3Enable ? color.white : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Real performance data from combo backtesting for Signal 3
    [maExitWinRate3, maExitProfit3, maExitTrades3, maExitDrawdown3] = getComboResults(2, 0)     // MA Exit
    [fixedExitWinRate3, fixedExitProfit3, fixedExitTrades3, fixedExitDrawdown3] = getComboResults(2, 1)  // Fixed SL/TP
    [fibExitWinRate3, fibExitProfit3, fibExitTrades3, fibExitDrawdown3] = getComboResults(2, 2)   // Fibonacci
    [trailExitWinRate3, trailExitProfit3, trailExitTrades3, trailExitDrawdown3] = getComboResults(2, 3)  // Trailing
    [customExitWinRate3, customExitProfit3, customExitTrades3, customExitDrawdown3] = getComboResults(2, 4) // Custom

    table.cell(entryExitTable, 1, matrixRow, formatStackedCell(maExitWinRate3, maExitProfit3, maExitTrades3, maExitDrawdown3, signal3Enable), text_color = signal3Enable and maExitTrades3 > 0 ? (maExitWinRate3 > 0.6 ? color.lime : maExitWinRate3 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 2, matrixRow, formatStackedCell(fixedExitWinRate3, fixedExitProfit3, fixedExitTrades3, fixedExitDrawdown3, signal3Enable), text_color = signal3Enable and fixedExitTrades3 > 0 ? (fixedExitWinRate3 > 0.6 ? color.lime : fixedExitWinRate3 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 3, matrixRow, formatStackedCell(fibExitWinRate3, fibExitProfit3, fibExitTrades3, fibExitDrawdown3, signal3Enable), text_color = signal3Enable and fibExitTrades3 > 0 ? (fibExitWinRate3 > 0.6 ? color.lime : fibExitWinRate3 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 4, matrixRow, formatStackedCell(trailExitWinRate3, trailExitProfit3, trailExitTrades3, trailExitDrawdown3, signal3Enable), text_color = signal3Enable and trailExitTrades3 > 0 ? (trailExitWinRate3 > 0.6 ? color.lime : trailExitWinRate3 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 5, matrixRow, formatStackedCell(customExitWinRate3, customExitProfit3, customExitTrades3, customExitDrawdown3, signal3Enable), text_color = signal3Enable and customExitTrades3 > 0 ? (customExitWinRate3 > 0.6 ? color.lime : customExitWinRate3 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Calculate best exit method dynamically based on real data
    bestExitMethod3 = 'N/A'
    bestWinRate3 = 0.0
    if signal3Enable
        bestExitMethod3 := 'MA'
        bestWinRate3 := maExitWinRate3
        if fixedExitWinRate3 > bestWinRate3
            bestExitMethod3 := 'Fixed'
            bestWinRate3 := fixedExitWinRate3
        if fibExitWinRate3 > bestWinRate3
            bestExitMethod3 := 'Fib'
            bestWinRate3 := fibExitWinRate3
        if trailExitWinRate3 > bestWinRate3
            bestExitMethod3 := 'Trail'
            bestWinRate3 := trailExitWinRate3
        if customExitWinRate3 > bestWinRate3
            bestExitMethod3 := 'Custom'
            bestWinRate3 := customExitWinRate3
    
    table.cell(entryExitTable, 6, matrixRow, bestExitMethod3 != 'N/A' ? bestExitMethod3 + ' ⭐' : 'N/A', text_color = bestExitMethod3 != 'N/A' ? color.lime : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 90))

    matrixRow := matrixRow + 1

    // Signal 4 entry combinations (always show)
    signalDisplayName4 = signal4Enable ? signal4Name : signal4Name + ' (OFF)'
    table.cell(entryExitTable, 0, matrixRow, signalDisplayName4, text_color = signal4Enable ? color.white : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Real performance data from combo backtesting for Signal 4
    [maExitWinRate4, maExitProfit4, maExitTrades4, maExitDrawdown4] = getComboResults(3, 0)     // MA Exit
    [fixedExitWinRate4, fixedExitProfit4, fixedExitTrades4, fixedExitDrawdown4] = getComboResults(3, 1)  // Fixed SL/TP
    [fibExitWinRate4, fibExitProfit4, fibExitTrades4, fibExitDrawdown4] = getComboResults(3, 2)   // Fibonacci
    [trailExitWinRate4, trailExitProfit4, trailExitTrades4, trailExitDrawdown4] = getComboResults(3, 3)  // Trailing
    [customExitWinRate4, customExitProfit4, customExitTrades4, customExitDrawdown4] = getComboResults(3, 4) // Custom

    table.cell(entryExitTable, 1, matrixRow, formatStackedCell(maExitWinRate4, maExitProfit4, maExitTrades4, maExitDrawdown4, signal4Enable), text_color = signal4Enable and maExitTrades4 > 0 ? (maExitWinRate4 > 0.6 ? color.lime : maExitWinRate4 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 2, matrixRow, formatStackedCell(fixedExitWinRate4, fixedExitProfit4, fixedExitTrades4, fixedExitDrawdown4, signal4Enable), text_color = signal4Enable and fixedExitTrades4 > 0 ? (fixedExitWinRate4 > 0.6 ? color.lime : fixedExitWinRate4 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 3, matrixRow, formatStackedCell(fibExitWinRate4, fibExitProfit4, fibExitTrades4, fibExitDrawdown4, signal4Enable), text_color = signal4Enable and fibExitTrades4 > 0 ? (fibExitWinRate4 > 0.6 ? color.lime : fibExitWinRate4 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 4, matrixRow, formatStackedCell(trailExitWinRate4, trailExitProfit4, trailExitTrades4, trailExitDrawdown4, signal4Enable), text_color = signal4Enable and trailExitTrades4 > 0 ? (trailExitWinRate4 > 0.6 ? color.lime : trailExitWinRate4 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 5, matrixRow, formatStackedCell(customExitWinRate4, customExitProfit4, customExitTrades4, customExitDrawdown4, signal4Enable), text_color = signal4Enable and customExitTrades4 > 0 ? (customExitWinRate4 > 0.6 ? color.lime : customExitWinRate4 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Calculate best exit method dynamically based on real data
    bestExitMethod4 = 'N/A'
    bestWinRate4 = 0.0
    if signal4Enable
        bestExitMethod4 := 'MA'
        bestWinRate4 := maExitWinRate4
        if fixedExitWinRate4 > bestWinRate4
            bestExitMethod4 := 'Fixed'
            bestWinRate4 := fixedExitWinRate4
        if fibExitWinRate4 > bestWinRate4
            bestExitMethod4 := 'Fib'
            bestWinRate4 := fibExitWinRate4
        if trailExitWinRate4 > bestWinRate4
            bestExitMethod4 := 'Trail'
            bestWinRate4 := trailExitWinRate4
        if customExitWinRate4 > bestWinRate4
            bestExitMethod4 := 'Custom'
            bestWinRate4 := customExitWinRate4
    
    table.cell(entryExitTable, 6, matrixRow, bestExitMethod4 != 'N/A' ? bestExitMethod4 + ' ⭐' : 'N/A', text_color = bestExitMethod4 != 'N/A' ? color.lime : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 90))

    matrixRow := matrixRow + 1

    // Signal 5 entry combinations (always show)
    signalDisplayName5 = signal5Enable ? signal5Name : signal5Name + ' (OFF)'
    table.cell(entryExitTable, 0, matrixRow, signalDisplayName5, text_color = signal5Enable ? color.white : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 80))

    // Real performance data from combo backtesting for Signal 5
    [maExitWinRate5, maExitProfit5, maExitTrades5, maExitDrawdown5] = getComboResults(4, 0)     // MA Exit
    [fixedExitWinRate5, fixedExitProfit5, fixedExitTrades5, fixedExitDrawdown5] = getComboResults(4, 1)  // Fixed SL/TP
    [fibExitWinRate5, fibExitProfit5, fibExitTrades5, fibExitDrawdown5] = getComboResults(4, 2)   // Fibonacci
    [trailExitWinRate5, trailExitProfit5, trailExitTrades5, trailExitDrawdown5] = getComboResults(4, 3)  // Trailing
    [customExitWinRate5, customExitProfit5, customExitTrades5, customExitDrawdown5] = getComboResults(4, 4) // Custom

    table.cell(entryExitTable, 1, matrixRow, formatStackedCell(maExitWinRate5, maExitProfit5, maExitTrades5, maExitDrawdown5, signal5Enable), text_color = signal5Enable and maExitTrades5 > 0 ? (maExitWinRate5 > 0.6 ? color.lime : maExitWinRate5 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 2, matrixRow, formatStackedCell(fixedExitWinRate5, fixedExitProfit5, fixedExitTrades5, fixedExitDrawdown5, signal5Enable), text_color = signal5Enable and fixedExitTrades5 > 0 ? (fixedExitWinRate5 > 0.6 ? color.lime : fixedExitWinRate5 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 3, matrixRow, formatStackedCell(fibExitWinRate5, fibExitProfit5, fibExitTrades5, fibExitDrawdown5, signal5Enable), text_color = signal5Enable and fibExitTrades5 > 0 ? (fibExitWinRate5 > 0.6 ? color.lime : fibExitWinRate5 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 4, matrixRow, formatStackedCell(trailExitWinRate5, trailExitProfit5, trailExitTrades5, trailExitDrawdown5, signal5Enable), text_color = signal5Enable and trailExitTrades5 > 0 ? (trailExitWinRate5 > 0.6 ? color.lime : trailExitWinRate5 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))
    table.cell(entryExitTable, 5, matrixRow, formatStackedCell(customExitWinRate5, customExitProfit5, customExitTrades5, customExitDrawdown5, signal5Enable), text_color = signal5Enable and customExitTrades5 > 0 ? (customExitWinRate5 > 0.6 ? color.lime : customExitWinRate5 > 0.5 ? color.yellow : color.white) : color.gray, text_size = size.small, bgcolor = color.new(color.gray, 90))

    // Calculate best exit method dynamically based on real data
    bestExitMethod5 = 'N/A'
    bestWinRate5 = 0.0
    if signal5Enable
        bestExitMethod5 := 'MA'
        bestWinRate5 := maExitWinRate5
        if fixedExitWinRate5 > bestWinRate5
            bestExitMethod5 := 'Fixed'
            bestWinRate5 := fixedExitWinRate5
        if fibExitWinRate5 > bestWinRate5
            bestExitMethod5 := 'Fib'
            bestWinRate5 := fibExitWinRate5
        if trailExitWinRate5 > bestWinRate5
            bestExitMethod5 := 'Trail'
            bestWinRate5 := trailExitWinRate5
        if customExitWinRate5 > bestWinRate5
            bestExitMethod5 := 'Custom'
            bestWinRate5 := customExitWinRate5
    
    table.cell(entryExitTable, 6, matrixRow, bestExitMethod5 != 'N/A' ? bestExitMethod5 + ' ⭐' : 'N/A', text_color = bestExitMethod5 != 'N/A' ? color.lime : color.gray, text_size = size.normal, bgcolor = color.new(color.gray, 90))

    matrixRow := matrixRow + 1

    // Summary row showing real calculated averages from enabled signals
    table.cell(entryExitTable, 0, matrixRow, '💡 Recommendations', text_color = color.yellow, text_size = size.small, bgcolor = color.new(color.purple, 40))
    
    // Calculate real averages across all enabled signals
    enabledSignalCount = (signal1Enable ? 1 : 0) + (signal2Enable ? 1 : 0) + (signal3Enable ? 1 : 0) + (signal4Enable ? 1 : 0) + (signal5Enable ? 1 : 0)
    
    if enabledSignalCount > 0
        // Calculate average win rates for each exit method
        totalMAWinRate = 0.0
        totalFixedWinRate = 0.0
        totalFibWinRate = 0.0
        totalTrailWinRate = 0.0
        totalCustomWinRate = 0.0
        
        for i = 0 to 4
            if (i == 0 and signal1Enable) or (i == 1 and signal2Enable) or (i == 2 and signal3Enable) or (i == 3 and signal4Enable) or (i == 4 and signal5Enable)
                [maWR, _, _] = getComboResults(i, 0)
                [fixedWR, _, _] = getComboResults(i, 1)
                [fibWR, _, _] = getComboResults(i, 2)
                [trailWR, _, _] = getComboResults(i, 3)
                [customWR, _, _] = getComboResults(i, 4)
                
                totalMAWinRate := totalMAWinRate + maWR
                totalFixedWinRate := totalFixedWinRate + fixedWR
                totalFibWinRate := totalFibWinRate + fibWR
                totalTrailWinRate := totalTrailWinRate + trailWR
                totalCustomWinRate := totalCustomWinRate + customWR
        
        avgMAWinRate = totalMAWinRate / enabledSignalCount
        avgFixedWinRate = totalFixedWinRate / enabledSignalCount
        avgFibWinRate = totalFibWinRate / enabledSignalCount
        avgTrailWinRate = totalTrailWinRate / enabledSignalCount
        avgCustomWinRate = totalCustomWinRate / enabledSignalCount
        
        // Find best overall exit method
        bestOverallMethod = 'MA'
        bestOverallRate = avgMAWinRate
        if avgFixedWinRate > bestOverallRate
            bestOverallMethod := 'Fixed'
            bestOverallRate := avgFixedWinRate
        if avgFibWinRate > bestOverallRate
            bestOverallMethod := 'Fib'
            bestOverallRate := avgFibWinRate
        if avgTrailWinRate > bestOverallRate
            bestOverallMethod := 'Trail'
            bestOverallRate := avgTrailWinRate
        if avgCustomWinRate > bestOverallRate
            bestOverallMethod := 'Custom'
            bestOverallRate := avgCustomWinRate
        
        // Display real averages with color coding
        table.cell(entryExitTable, 1, matrixRow, 'Avg: ' + str.tostring(avgMAWinRate * 100, '#.1') + '%', text_color = avgMAWinRate > 0.6 ? color.lime : avgMAWinRate > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 2, matrixRow, 'Avg: ' + str.tostring(avgFixedWinRate * 100, '#.1') + '%', text_color = avgFixedWinRate > 0.6 ? color.lime : avgFixedWinRate > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 3, matrixRow, 'Avg: ' + str.tostring(avgFibWinRate * 100, '#.1') + '%', text_color = avgFibWinRate > 0.6 ? color.lime : avgFibWinRate > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 4, matrixRow, 'Avg: ' + str.tostring(avgTrailWinRate * 100, '#.1') + '%', text_color = avgTrailWinRate > 0.6 ? color.lime : avgTrailWinRate > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 5, matrixRow, 'Avg: ' + str.tostring(avgCustomWinRate * 100, '#.1') + '%', text_color = avgCustomWinRate > 0.6 ? color.lime : avgCustomWinRate > 0.5 ? color.yellow : color.red, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 6, matrixRow, bestOverallMethod + ' ⭐', text_color = color.lime, text_size = size.small, bgcolor = color.new(color.purple, 60))
    else
        // No signals enabled - show placeholder
        table.cell(entryExitTable, 1, matrixRow, 'No Data', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 2, matrixRow, 'No Data', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 3, matrixRow, 'No Data', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 4, matrixRow, 'No Data', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 5, matrixRow, 'No Data', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))
        table.cell(entryExitTable, 6, matrixRow, 'Enable Signals', text_color = color.gray, text_size = size.small, bgcolor = color.new(color.purple, 60))

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
