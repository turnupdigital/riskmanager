// 2025 Andres Garcia — Multi‑Signal Risk Manager · TradersPost Webhook
//  ────────────────────────────────────────────────────────────────
//  Enhanced Multi-Signal Risk Management System
//  • Professional risk management with multiple exit strategies
//  • TradersPost webhook integration for automated trading
//  • Configurable position sizing and stop-loss/take-profit levels
//  ────────────────────────────────────────────────────────────────
//@version=5
strategy(
     title             = "Multi‑Signal Risk Manager · Performance Webhooks",
     overlay           = true,
     // User-controllable quantity
     default_qty_type  = strategy.fixed,
     default_qty_value = 1,
     calc_on_order_fills = true,
     process_orders_on_close = true,
     calc_on_every_tick = false)

// ─────────────────── 0 · POSITION SIZE & PRIMARY SIGNALS ───────────────────
// Position Size Control
positionQty = input.int(1, "Number of Contracts", minval = 1, maxval = 1000, group = "Position Size", 
                       tooltip = "Set the number of contracts/shares to trade per signal")

// ─────────────────── PRIMARY ENTRY SIGNALS ───────────────────
primaryLongEnable = input.bool(true, "", inline="primaryLong", group = "Primary Signals")
primaryLongSrc  = input.source(close, "Primary Long Signal", inline="primaryLong", group = "Primary Signals", tooltip="Enable/disable this signal with the checkbox")

primaryShortEnable = input.bool(true, "", inline="primaryShort", group = "Primary Signals")
primaryShortSrc = input.source(close, "Primary Short Signal", inline="primaryShort", group = "Primary Signals", tooltip="Enable/disable this signal with the checkbox")



// Process the primary signals using the enable checkboxes
_primaryLongRaw  = primaryLongEnable and nz(primaryLongSrc, 0) != 0
_primaryShortRaw = primaryShortEnable and nz(primaryShortSrc, 0) != 0
primaryLongSig   = _primaryLongRaw and not _primaryLongRaw[1]  // Rising edge detection
primaryShortSig  = _primaryShortRaw and not _primaryShortRaw[1]  // Rising edge detection

// ─────────────────── 1 · TRADERSPOST JSON HELPERS ───────────────

// ───── Pre-built JSON messages (compile-time constants) ─────
// Use TradingView alert placeholders so we avoid any per-bar string operations.
// Placeholders {{close}} and {{timenow}} will be expanded at alert trigger time.
var string _jsonBase = '{"ticker":"' + syminfo.ticker + '","price":{{close}},"time":{{timenow}}'

var string longEntryMsg  = _jsonBase + ',"action":"buy","sentiment":"long"}'
var string shortEntryMsg = _jsonBase + ',"action":"sell","sentiment":"short"}'
var string flatExitMsg   = _jsonBase + ',"action":"exit","sentiment":"flat"}'
var string longExitMsg   = _jsonBase + ',"action":"sell","sentiment":"flat"}'   // closes long
var string shortExitMsg  = _jsonBase + ',"action":"buy","sentiment":"flat"}'    // closes short

// ─────────────────── 2 · ATR SETTINGS ───────────────────────────
atrLen = input.int(14,  "ATR Length", minval = 1, group = "ATR Settings")
atrVal = ta.atr(atrLen)

// ─────────────────── 3 · EXIT PARAMETERS (ASCII SAFE) ───────────
maExitOn = input.bool(true,  "Enable MA Exit", group = "MA Exit")
maLen    = input.int(8,      "MA Length",      minval = 1, group = "MA Exit")
maType   = input.string("EMA", "MA Type", options = ["SMA","EMA","WMA","VWMA","SMMA"], group = "MA Exit")
// Intrabar exits removed - exits only trigger once per bar on close

priceMA = maType == "SMA"  ? ta.sma(close, maLen)  :
          maType == "EMA"  ? ta.ema(close, maLen)  :
          maType == "WMA"  ? ta.wma(close, maLen)  :
          maType == "VWMA" ? ta.vwma(close, maLen) :
                             ta.rma(close, maLen)

fixedEnable = input.bool(false, "Enable Fixed SL/TP", group = "Fixed SL/TP")
fixedUnit   = input.string("ATR", "Unit", options = ["ATR","Points"], group = "Fixed SL/TP")
fixedStop   = input.float(1.0,   "Stop Size", step = 0.1, minval = 0.0, group = "Fixed SL/TP")

tpCalc(d) => fixedUnit == "ATR" ? d * atrVal : d

tp1Enable = input.bool(false, "TP1", inline = "tp1", group = "Fixed SL/TP")
tp1Size   = input.float(2.0 ,  ""  , inline = "tp1", group = "Fixed SL/TP")
tp2Enable = input.bool(false, "TP2", inline = "tp2", group = "Fixed SL/TP")
tp2Size   = input.float(3.0 ,  ""  , inline = "tp2", group = "Fixed SL/TP")
tp3Enable = input.bool(false, "TP3", inline = "tp3", group = "Fixed SL/TP")
tp3Size   = input.float(4.0 ,  ""  , inline = "tp3", group = "Fixed SL/TP")

// ─────────────────── 3B · FIBONACCI STOP LOSS ─────────────────────
fibEnable = input.bool(false, "Enable Fibonacci SL/TP", group = "Fibonacci SL/TP")
fibUnit   = input.string("ATR", "Unit", options = ["ATR","Points"], group = "Fibonacci SL/TP", tooltip = "ATR = Multiple of Average True Range | Points = Absolute price points")

// SIMPLE: One Stop Loss Dropdown
fibSL = input.string("0.618", "Stop Loss Fib Level", options = ["0.236", "0.382", "0.500", "0.618", "0.786"], group = "Fibonacci SL/TP",                     tooltip = "Select Fibonacci level for stop loss. 0.618 (Golden Ratio) recommended")

// SIMPLE: Three Take Profit Dropdowns
fibTP1Enable = input.bool(false, "TP1", inline = "tp1", group = "Fibonacci SL/TP")
fibTP1 = input.string("1.272", "", inline = "tp1", options = ["1.000", "1.272", "1.618", "2.000", "2.618"], group = "Fibonacci SL/TP", 
                     tooltip = "First take profit level. 1.272 recommended")

fibTP2Enable = input.bool(false, "TP2", inline = "tp2", group = "Fibonacci SL/TP")
fibTP2 = input.string("1.618", "", inline = "tp2", options = ["1.000", "1.272", "1.618", "2.000", "2.618"], group = "Fibonacci SL/TP", 
                     tooltip = "Second take profit level. 1.618 (Golden Ratio) recommended")

fibTP3Enable = input.bool(false, "TP3", inline = "tp3", group = "Fibonacci SL/TP")
fibTP3 = input.string("2.618", "", inline = "tp3", options = ["1.000", "1.272", "1.618", "2.000", "2.618"], group = "Fibonacci SL/TP", 
                     tooltip = "Third take profit level. 2.618 recommended")

// Calculate Fibonacci distance based on unit type
fibCalc(level) => fibUnit == "ATR" ? level * atrVal : level

// Convert string selections to float values
getFibValue(fibString) => 
    fibString == "0.236" ? 0.236 :     fibString == "0.382" ? 0.382 :     fibString == "0.500" ? 0.500 :     fibString == "0.618" ? 0.618 :     fibString == "0.786" ? 0.786 :     fibString == "1.000" ? 1.000 :     fibString == "1.272" ? 1.272 :     fibString == "1.618" ? 1.618 :     fibString == "2.000" ? 2.000 : 2.618

trailEnable = input.bool(false, "Enable Trailing Stop", group = "Trailing Stop")
trailType   = input.string("ATR", "Type", options = ["ATR","Points","Percent"], group = "Trailing Stop")
trailVal    = input.float(1.0,   "Value", step = 0.1, group = "Trailing Stop")

// Custom Opposite Exit - FIXED LOGIC
// Short Exit signals are bearish and close LONG positions
// Long Exit signals are bullish and close SHORT positions
custEnable  = input.bool(false, "Enable Custom Exit", group = "Custom Opposite Exit", tooltip="Custom exits use the opposite logic: Short Exit signals close LONG positions, Long Exit signals close SHORT positions")

// Fixed inputs with better defaults and tooltips
// Using standard inputs with proper default series as required by Pine Script v5
exitShort1_enable = input.bool(false, "", inline="exitShort1", group="Custom Opposite Exit")
exitShort1_input = input.source(close, "Short Exit 1", inline="exitShort1", group="Custom Opposite Exit", tooltip="Bearish signal to exit LONG positions")

exitShort2_enable = input.bool(false, "", inline="exitShort2", group="Custom Opposite Exit")
exitShort2_input = input.source(close, "Short Exit 2", inline="exitShort2", group="Custom Opposite Exit", tooltip="Bearish signal to exit LONG positions")

exitLong1_enable = input.bool(false, "", inline="exitLong1", group="Custom Opposite Exit")
exitLong1_input = input.source(close, "Long Exit 1", inline="exitLong1", group="Custom Opposite Exit", tooltip="Bullish signal to exit SHORT positions")

exitLong2_enable = input.bool(false, "", inline="exitLong2", group="Custom Opposite Exit")
exitLong2_input = input.source(close, "Long Exit 2", inline="exitLong2", group="Custom Opposite Exit", tooltip="Bullish signal to exit SHORT positions")

// Process the inputs using the enable checkboxes
// If checkbox is off, signal is forced to 0 (disabled)
exitShort1 = exitShort1_enable ? nz(exitShort1_input, 0) : 0
exitShort2 = exitShort2_enable ? nz(exitShort2_input, 0) : 0
exitLong1  = exitLong1_enable ? nz(exitLong1_input, 0) : 0
exitLong2  = exitLong2_enable ? nz(exitLong2_input, 0) : 0

// Fixed logic that detects pulses properly from both inputs
customExitShort1 = exitShort1_enable and nz(exitShort1,0)!=0 and nz(exitShort1[1],0)==0
customExitShort2 = exitShort2_enable and nz(exitShort2,0)!=0 and nz(exitShort2[1],0)==0
customExitLong1  = exitLong1_enable and nz(exitLong1,0)!=0 and nz(exitLong1[1],0)==0
customExitLong2  = exitLong2_enable and nz(exitLong2,0)!=0 and nz(exitLong2[1],0)==0

// Combine after pulse detection
customExitShort = customExitShort1 or customExitShort2
customExitLong  = customExitLong1 or customExitLong2

// Check if we're in a flat position
isFlat = strategy.position_size == 0

// State tracking to prevent redundant strategy.exit() calls
var bool fixedExitsSet = false
var bool fibExitsSet = false

// Reset exit flags when position becomes flat
if isFlat
    fixedExitsSet := false
    fibExitsSet := false

// ─────────────────── 5 · ENTRY MANAGEMENT ──────────────────────
// ───  X · TIME GATING (Optional - can be disabled by setting to always true) ───
tradeHour = hour(time)
tradeMin  = minute(time)
tradeHHMM = tradeHour * 100 + tradeMin

// Allow entries (can be modified as needed)
canEnter = true  // Set to 'tradeHHMM < 1645' if you want time gating

// Capture primary signals (simplified - no continuation filter)
if primaryLongSig and canEnter
    strategy.entry("Long", strategy.long, qty = positionQty, alert_message = longEntryMsg)

if primaryShortSig and canEnter
    strategy.entry("Short", strategy.short, qty = positionQty, alert_message = shortEntryMsg)



// ─────────────────── 6 · EXIT RULES ────────────────────────────


isLong  = strategy.position_size > 0
entryPx = strategy.position_avg_price

// 6-A MA exit (Standard candlestick close only - once per bar)
// Precompute MA cross signals for consistency
crossDn = ta.crossunder(close, priceMA)
crossUp = ta.crossover(close, priceMA)
if maExitOn and not isFlat
    // Exit only on candlestick close crossover/crossunder (once per bar)
    if isLong and crossDn
        strategy.close("Long",  comment="MAExit", alert_message=flatExitMsg)
    if not isLong and crossUp
        strategy.close("Short", comment="MAExit", alert_message=flatExitMsg)

// 6‑B Fixed SL / TP (Optimized - only set exits once per position)
if fixedEnable and not isFlat and not fixedExitsSet
    stopDist  = fixedUnit == "ATR" ? fixedStop * atrVal : fixedStop
    stopPrice = isLong ? entryPx - stopDist : entryPx + stopDist
    strategy.exit(isLong ? "FX_L" : "FX_S", from_entry = isLong ? "Long" : "Short", stop = stopPrice, alert_message = flatExitMsg)

    if tp1Enable
        limit1 = isLong ? entryPx + tpCalc(tp1Size) : entryPx - tpCalc(tp1Size)
        strategy.exit("TP1", from_entry = isLong ? "Long" : "Short", qty_percent = 33, limit = limit1, alert_message = flatExitMsg)
    if tp2Enable
        limit2 = isLong ? entryPx + tpCalc(tp2Size) : entryPx - tpCalc(tp2Size)
        strategy.exit("TP2", from_entry = isLong ? "Long" : "Short", qty_percent = 33, limit = limit2, alert_message = flatExitMsg)
    if tp3Enable
        limit3 = isLong ? entryPx + tpCalc(tp3Size) : entryPx - tpCalc(tp3Size)
        strategy.exit("TP3", from_entry = isLong ? "Long" : "Short", qty_percent = 34, limit = limit3, alert_message = flatExitMsg)
    
    // Mark that fixed exits have been set for this position
    fixedExitsSet := true

// 6‑B2 Fibonacci SL / TP (Optimized - only set exits once per position)
if fibEnable and not isFlat and not fibExitsSet
    // Stop Loss
    fibStopDist  = fibCalc(getFibValue(fibSL))
    fibStopPrice = isLong ? entryPx - fibStopDist : entryPx + fibStopDist
    
    // Count enabled TPs for smart position sizing
    enabledTPs = (fibTP1Enable ? 1 : 0) + (fibTP2Enable ? 1 : 0) + (fibTP3Enable ? 1 : 0)
    
    // Smart position sizing based on number of enabled TPs
    tp1Percent = enabledTPs == 1 ? 100 : enabledTPs == 2 ? 50 : 33  // 100%, 50%, or 33%
    tp2Percent = enabledTPs == 2 ? 50 : 33                          // 50% or 33%
    tp3Percent = 34                                                  // Always 34% (remainder)
    
    // Take Profit 1
    if fibTP1Enable
        fibTP1Dist  = fibCalc(getFibValue(fibTP1))
        fibTP1Price = isLong ? entryPx + fibTP1Dist : entryPx - fibTP1Dist
        strategy.exit("FIB_TP1", from_entry = isLong ? "Long" : "Short", 
                      stop = fibStopPrice, limit = fibTP1Price, qty_percent = tp1Percent, 
                      comment = "Fib SL:" + fibSL + " TP1:" + fibTP1 + " (" + str.tostring(tp1Percent) + "%)", 
                      alert_message = flatExitMsg)
    
    // Take Profit 2
    if fibTP2Enable
        fibTP2Dist  = fibCalc(getFibValue(fibTP2))
        fibTP2Price = isLong ? entryPx + fibTP2Dist : entryPx - fibTP2Dist
        strategy.exit("FIB_TP2", from_entry = isLong ? "Long" : "Short", 
                      stop = fibStopPrice, limit = fibTP2Price, qty_percent = tp2Percent, 
                      comment = "Fib SL:" + fibSL + " TP2:" + fibTP2 + " (" + str.tostring(tp2Percent) + "%)", 
                      alert_message = flatExitMsg)
    
    // Take Profit 3
    if fibTP3Enable
        fibTP3Dist  = fibCalc(getFibValue(fibTP3))
        fibTP3Price = isLong ? entryPx + fibTP3Dist : entryPx - fibTP3Dist
        strategy.exit("FIB_TP3", from_entry = isLong ? "Long" : "Short", 
                      stop = fibStopPrice, limit = fibTP3Price, qty_percent = tp3Percent, 
                      comment = "Fib SL:" + fibSL + " TP3:" + fibTP3 + " (" + str.tostring(tp3Percent) + "%)", 
                      alert_message = flatExitMsg)
    
    // If no TPs enabled, just use stop loss
    if enabledTPs == 0
        strategy.exit("FIB_SL_ONLY", from_entry = isLong ? "Long" : "Short", 
                      stop = fibStopPrice, comment = "Fib SL:" + fibSL, alert_message = flatExitMsg)
    
    // Mark that fibonacci exits have been set for this position
    fibExitsSet := true

// 6‑C Trailing stop
if trailEnable and not isFlat
    trailPts = trailType == "ATR"     ? (trailVal * atrVal) / syminfo.mintick :
               trailType == "Points"  ?  trailVal           / syminfo.mintick :
               trailType == "Percent" ? (close * trailVal / 100) / syminfo.mintick : 0
    strategy.exit(isLong ? "TR_L" : "TR_S", from_entry = isLong ? "Long" : "Short", trail_points = trailPts, trail_offset = 0, alert_message = flatExitMsg)

// 6‑E Custom opposite‑signal exit
if custEnable and not isFlat
    if isLong  and customExitShort
        strategy.close("Long",  comment = "CustExit", alert_message = flatExitMsg)
    if not isLong and customExitLong
        strategy.close("Short", comment = "CustExit", alert_message = flatExitMsg)


// ─────────────────── 8 · CHART VISUALS ─────────────────────────
plotshape(primaryLongSig , title = "BUY" , style = shape.triangleup , location = location.belowbar, color = color.lime , size = size.small)
plotshape(primaryShortSig, title = "SELL", style = shape.triangledown, location = location.abovebar,  color = color.red  , size = size.small)
