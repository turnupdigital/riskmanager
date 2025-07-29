# Individual Signal Backtesting Implementation Plan
## Critical Strategy Analytics for EZ Algo Trader

---

## 🎯 **PROJECT OBJECTIVE**

Create a **real-time, individual signal backtesting system** that provides accurate performance metrics for each of the 10 signals independently, allowing for data-driven signal optimization and risk diversification analysis.

---

## 📋 **INTRODUCTION & PROBLEM ANALYSIS**

### **Previous Attempts & Current Issues**

After extensive analysis by 7 different AI systems (documented in `Backtest.md`), we've identified the core problems with our current approach:

#### **❌ Current Implementation Problems:**

1. **Signal Attribution Crisis**: 
   - Signals are combined into `primaryLongSig = sig1Long or sig2Long or ...`
   - Impossible to track which specific signal(s) generated profit/loss
   - All metrics are aggregated, not individual

2. **Inaccurate P&L Calculations**:
   - Missing proper futures contract multiplier integration
   - No separation of long vs short performance
   - Drawdown calculations are fundamentally flawed
   - Not using TradingView's built-in tick size functions

3. **Pyramiding Attribution Issues**:
   - When multiple signals fire simultaneously, can't determine individual contribution
   - Position sizing doesn't properly attribute contracts to specific signals
   - Exit attribution is completely missing

4. **Real-Time Update Failures**:
   - Table doesn't update when strategy parameters change
   - Arrays don't reset when signals are toggled on/off
   - Performance metrics lag behind actual strategy changes

#### **✅ What Currently Works:**
- Basic table structure and display
- Signal counting and combination logic
- General strategy execution framework
- Individual signal naming and management

---

## 🏗️ **COMPREHENSIVE SOLUTION ARCHITECTURE**

### **Core Principle: Virtual Signal Backtesting**

**CRITICAL INSIGHT FROM ANALYSIS**: Instead of trying to fix the combined approach, implement **10 parallel virtual accounts** - one per signal - running alongside the main strategy.

### **Key Technical Approach:**

```pine
// VIRTUAL SIGNAL BACKTESTER - Each signal gets its own "account"
var array<bool>  _isLive     = array.new<bool>(10, false)   // Signal has open position
var array<bool>  _isLong     = array.new<bool>(10, false)   // Direction of position  
var array<float> _entryPx    = array.new<float>(10, na)     // Entry price per signal
var array<int>   _qty        = array.new<int>(10, 0)        // Contracts per signal
var array<float> _eqPeak     = array.new<float>(10, 0)      // Peak equity per signal
var array<float> _eqLow      = array.new<float>(10, na)     // Trough equity per signal

// Per-signal performance tracking
type SignalData
    int longTrades = 0
    int shortTrades = 0
    int longWins = 0
    int shortWins = 0
    float longProfits = 0.0
    float shortProfits = 0.0
    float longMaxDD = 0.0
    float shortMaxDD = 0.0
    float avgWin = 0.0
    float avgLoss = 0.0
    float profitFactor = 0.0
    float sharpeRatio = 0.0
```

---

## 📊 **REQUIRED METRICS SPECIFICATION**

### **Enhanced Per-Signal Metrics (Based on Analysis):**

| Metric | Long Side | Short Side | Calculation Method |
|--------|-----------|------------|-------------------|
| **Trades** | Total long entries | Total short entries | Counter increment on signal fire |
| **Win Rate** | Long wins / Long trades × 100 | Short wins / Short trades × 100 | Win = P&L > 0 |
| **P&L (Dollars)** | Sum of long profits in $ | Sum of short profits in $ | `(exit - entry) × contracts × syminfo.pointvalue` |
| **Max Drawdown** | Worst long unrealized loss | Worst short unrealized loss | `(peak - trough) × syminfo.pointvalue` |
| **Avg Win/Loss** | Average winning trade $ | Average losing trade $ | Total wins/losses ÷ count |
| **Profit Factor** | Gross profit ÷ Gross loss | Gross profit ÷ Gross loss | Risk-reward efficiency |
| **Sharpe Ratio** | Risk-adjusted returns | Risk-adjusted returns | Return ÷ volatility |

### **Critical Requirements:**
- **Real-time updates** as strategy parameters change
- **Futures-accurate calculations** using `syminfo.pointvalue` 
- **Separate long/short tracking** for directional analysis
- **Commission & slippage integration** for realistic results
- **Virtual account isolation** for perfect attribution
- **Parameter change detection** for array resets

---

## 🎨 **ADVANCED STYLING ARCHITECTURE** *(NEW - StyleLibrary Integration)*

### **Professional Visual Enhancement System** 📊 *Priority: MEDIUM*

Based on `StyleLibrary.md` analysis, we can implement **professional-grade styling** that rivals commercial trading platforms:

#### **1. StyleLibrary Integration** 
```pine
import StyleLibrary as sty

// Professional styling constants
HEADER_SIZE = sty.sizeNumeric(sty.sizeEnum.large, sty.sizeNumericType.new(20, 14, 16, 20, 24, 0, 16))
DATA_SIZE = sty.sizeNumeric(sty.sizeEnum.normal, sty.sizeNumericType.new(8, 10, 12, 16, 20, 0, 12))
LABEL_SIZE = sty.sizeNumeric(sty.sizeEnum.small, sty.sizeNumericType.new(7, 9, 11, 15, 19, 0, 11))
```

#### **2. Dynamic Visual Status System** 🎯
**Signal Performance Indicators**:
- **🟢 Excellent**: Win rate >70%, PF >2.0, positive P&L
- **🟡 Good**: Win rate 50-70%, PF 1.5-2.0, positive P&L  
- **🟠 Warning**: Win rate 40-50%, PF 1.0-1.5, break-even
- **🔴 Poor**: Win rate <40%, PF <1.0, negative P&L
- **⚫ Disabled**: Signal not active

#### **3. Enhanced Table Architecture** 📋
```pine
// Multi-level table system with professional styling
var table virtualAccountTable = table.new(
    position.top_right, 
    columns=11, rows=12,
    bgcolor=color.new(color.black, 85),
    border_width=2,
    border_color=color.new(color.white, 70)
)

// Professional header formatting with StyleLibrary
if barstate.islast
    // Bold headers with custom sizing
    table.cell(virtualAccountTable, 0, 0, "📊 SIGNAL", 
               text_color=color.white, bgcolor=color.new(color.blue, 80),
               text_size=HEADER_SIZE)
    sty.textFormat(virtualAccountTable, 0, 0, true, false) // Bold header
```

#### **4. Advanced Label System** 🏷️
**Dynamic Signal Attribution Labels**:
```pine
// Professional signal labels with tooltips
createSignalLabel(signalName, signalStrength, position) =>
    labelColor = signalStrength > 0.8 ? color.lime : 
                 signalStrength > 0.6 ? color.yellow : 
                 signalStrength > 0.4 ? color.orange : color.red
    
    label.new(
        bar_index, high + (2 * ta.atr(14)),
        text=signalName + " (" + str.tostring(signalStrength, "#.##") + ")",
        style=sty.labelStyle("Diamond"),
        color=labelColor,
        textcolor=color.white,
        size=sty.sizeStyle("Normal"),
        tooltip="Signal Strength: " + str.tostring(signalStrength*100, "#.#") + "%\n" +
                "Historical Win Rate: " + str.tostring(historicalWinRate, "#.#") + "%\n" +
                "Last 10 Trades P&L: $" + str.tostring(recent10TradesProfit, "#.##")
    )
```

#### **5. Color-Coded Performance Hierarchy** 🌈
```pine
// Professional color scheme for performance tiers
getPerformanceColor(winRate, profitFactor, totalPnL) =>
    if winRate >= 70 and profitFactor >= 2.0 and totalPnL > 0
        color.new(color.lime, 20)      // Excellent - Bright Green
    else if winRate >= 50 and profitFactor >= 1.5 and totalPnL > 0
        color.new(color.yellow, 20)    // Good - Yellow
    else if winRate >= 40 and profitFactor >= 1.0
        color.new(color.orange, 20)    // Warning - Orange
    else
        color.new(color.red, 20)       // Poor - Red
```

#### **6. Bar Coloring for Trade Attribution** 🎨
```pine
// Color bars based on which signal(s) triggered the trade
barcolor(
    longEntrySignal and primarySignalName == "LuxAlgo" ? color.new(color.lime, 70) :
    longEntrySignal and primarySignalName == "UTBot" ? color.new(color.blue, 70) :
    shortEntrySignal and primarySignalName == "LuxAlgo" ? color.new(color.red, 70) :
    shortEntrySignal and primarySignalName == "UTBot" ? color.new(color.purple, 70) : na
)
```

#### **7. Professional Debug Visualization** 🔍
```pine
// Enhanced debug labels with professional styling
createDebugLabel(message, severity) =>
    debugColor = severity == "ERROR" ? color.red :
                 severity == "WARN" ? color.orange :
                 severity == "INFO" ? color.blue : color.gray
    
    if debugLevel != "OFF"
        label.new(
            bar_index, low - (ta.atr(14) * 0.5),
            text="🔍 " + severity + ": " + message,
            style=sty.labelStyle("Label Lower Right"),
            color=debugColor,
            textcolor=color.white,
            size=sty.sizeStyle("Small"),
            tooltip="Debug Info - Click to expand details"
        )
```

#### **8. Enhanced Table Text Formatting** ✨
```pine
// Professional table cell formatting
formatTableCell(table_id, col, row, text, isHeader, isPositive, isNegative) =>
    cellColor = isHeader ? color.new(color.navy, 80) :
                isPositive ? color.new(color.green, 90) :
                isNegative ? color.new(color.red, 90) : 
                color.new(color.gray, 95)
    
    textColor = isHeader ? color.white :
                isPositive ? color.lime :
                isNegative ? color.red : color.white
    
    table.cell(table_id, col, row, text,
               bgcolor=cellColor, text_color=textColor,
               text_size=isHeader ? HEADER_SIZE : DATA_SIZE)
    
    // Apply bold formatting for headers
    if isHeader
        sty.textFormat(table_id, col, row, true, false)
```

---

## 🔧 **TECHNICAL IMPLEMENTATION PHASES**

### **Phase 1: Virtual Signal Architecture** ⚡ *Priority: CRITICAL*

**Objective**: Implement 10 virtual signal accounts running parallel to main strategy

**Key Innovation from Analysis**:
```pine
// REAL STRATEGY EXECUTES NORMALLY
if longEntrySignal and strategy.position_size == 0
    strategy.entry("Long", strategy.long, qty=positionQty)

// VIRTUAL ACCOUNTS TRACK EACH SIGNAL INDIVIDUALLY  
for i = 0 to 9
    if signalLong[i] and signalEnabled[i] and not array.get(_isLive, i)
        // Virtual entry for this signal
        array.set(_isLive, i, true)
        array.set(_isLong, i, true) 
        array.set(_entryPx, i, close)
        array.set(_qty, i, 1)  // 1 contract per signal
        array.set(_eqPeak, i, 0.0)
        array.set(_eqLow, i, 0.0)
```

**Tasks**:
1. Create virtual account arrays for all 10 signals
2. Implement individual signal entry/exit logic
3. Separate virtual tracking from main strategy execution
4. Add signal state management (live/flat, long/short)

---

### **Phase 2: Accurate P&L Engine with Commission/Slippage** 💰 *Priority: CRITICAL*

**Objective**: Implement futures-accurate profit/loss with realistic trading costs

**Key Enhancement from Analysis**:
```pine
// Use TradingView's built-in functions + realistic costs
pointValue = syminfo.pointvalue              // Automatic futures multiplier
tickSize = syminfo.mintick                   // Minimum price movement
commission_per_contract = 4.00               // Round-trip commission
slippage_ticks = 1                          // Conservative slippage

calculateNetPnL(entryPrice, exitPrice, contracts, isLong) =>
    priceDiff = isLong ? (exitPrice - entryPrice) : (entryPrice - exitPrice)
    grossPnl = (priceDiff / tickSize) * pointValue * contracts
    totalCommission = commission_per_contract * contracts
    slippageCost = slippage_ticks * pointValue * contracts
    netPnl = grossPnl - totalCommission - slippageCost
    netPnl
```

**Tasks**:
1. Integrate `syminfo.pointvalue` for automatic contract multipliers
2. Add commission and slippage calculations
3. Implement separate long/short P&L methods
4. Create real-time unrealized P&L tracking

---

### **Phase 3: Advanced Drawdown System** 📉 *Priority: HIGH*

**Objective**: Implement proper peak-to-trough drawdown tracking during open trades

**Critical Fix from Analysis**:
```pine
// Track drawdown during OPEN trades (not just closed)
if array.get(_isLive, i)  // Signal has open position
    currentUnrealizedPnL = calculateUnrealizedPnL(i)
    
    // Update peak/trough for this signal
    currentPeak = array.get(_eqPeak, i)
    currentTrough = array.get(_eqLow, i)
    
    array.set(_eqPeak, i, math.max(currentPeak, currentUnrealizedPnL))
    array.set(_eqLow, i, math.min(currentTrough, currentUnrealizedPnL))
    
    // Calculate current drawdown in dollars
    currentDD = (array.get(_eqPeak, i) - array.get(_eqLow, i)) * pointValue
    
    // Update max drawdown if worse
    signalData = array.get(signals, i)
    if currentDD > signalData.longMaxDD  // or shortMaxDD
        signalData.longMaxDD := currentDD
```

---

### **Phase 4: Enhanced Table with Long/Short Separation** 📋 *Priority: HIGH*

**Objective**: Create comprehensive performance display with separate directional metrics

**New Table Structure from Analysis**:
```
| Signal   | L.Trades | L.Win% | L.P&L($) | S.Trades | S.Win% | S.P&L($) | Max DD($) | PF | Action  |
|----------|----------|--------|----------|----------|--------|----------|-----------|----|---------| 
| LuxAlgo  |    15    | 73.3%  | +$1,240  |    8     | 62.5%  | +$680    | -$420     |2.1 | KEEP ✅ |
| UTBot    |    12    | 58.3%  | +$890    |    6     | 50.0%  | -$120    | -$380     |1.8 | TEST ⚠️ |
```

**Features**:
- **Larger text size** (`text_size=size.normal` instead of `size.small`)
- **Separate long/short columns** for directional analysis
- **Dollar values** using `syminfo.pointvalue`
- **Profit Factor** and **Sharpe Ratio** columns
- **Real-time parameter change detection**

---

## 📊 **OPTIMIZED TABLE ARCHITECTURE**

*Based on TradingView tables.md best practices for maximum performance*

### **Global Table Declaration Strategy:**
```pine
// Declare all tables with var at global scope for optimal performance
var table performanceTable = table.new(position.bottom_right, 9, 11,
                                      bgcolor=color.new(color.black, 5),
                                      frame_width=2, frame_color=color.white)

var table debugTable = table.new(position.top_right, 6, 11,
                                bgcolor=color.new(color.black, 10),
                                frame_width=1, frame_color=color.gray)

var table validationTable = table.new(position.bottom_left, 5, 12,
                                     bgcolor=color.new(color.navy, 10),
                                     frame_width=1, frame_color=color.blue)
```

### **Performance-Optimized Updates:**
```pine
// CRITICAL: Only update tables on barstate.islast for performance
updateAllTables() =>
    if barstate.islast
        if showBacktestTable
            updatePerformanceTable()
        if debugLevel != "OFF"
            updateDebugTables()
        if validationMode
            updateValidationTable()

// Single call per bar instead of multiple updates
updateAllTables()
```

### **Enhanced Visual Design:**
```pine
// Professional table formatting with visual hierarchy
createTableHeaders() =>
    if barstate.islast
        // Performance table headers with icons
        table.cell(performanceTable, 0, 0, "📊 SIGNAL", 
                  bgcolor=color.new(color.blue, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 1, 0, "📈 L.TRADES", 
                  bgcolor=color.new(color.green, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 2, 0, "✅ L.WIN%", 
                  bgcolor=color.new(color.green, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 3, 0, "💰 L.P&L($)", 
                  bgcolor=color.new(color.green, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 4, 0, "📉 S.TRADES", 
                  bgcolor=color.new(color.red, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 5, 0, "✅ S.WIN%", 
                  bgcolor=color.new(color.red, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 6, 0, "💰 S.P&L($)", 
                  bgcolor=color.new(color.red, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 7, 0, "⚠️ MAX DD($)", 
                  bgcolor=color.new(color.orange, 20), text_color=color.white, text_size=size.normal)
        table.cell(performanceTable, 8, 0, "🎯 ACTION", 
                  bgcolor=color.new(color.purple, 20), text_color=color.white, text_size=size.normal)

// Color-coded performance cells
updatePerformanceRow(signalIndex, row) =>
    if barstate.islast and getSignalEnabled(signalIndex)
        signalData = array.get(signals, signalIndex)
        
        // Long side metrics
        longTrades = signalData.longTrades
        longWinRate = longTrades > 0 ? (signalData.longWins / longTrades) * 100 : 0
        longPnL = signalData.longProfits * syminfo.pointvalue
        
        // Short side metrics  
        shortTrades = signalData.shortTrades
        shortWinRate = shortTrades > 0 ? (signalData.shortWins / shortTrades) * 100 : 0
        shortPnL = signalData.shortProfits * syminfo.pointvalue
        
        // Combined metrics
        maxDD = math.max(signalData.longMaxDD, signalData.shortMaxDD) * syminfo.pointvalue
        totalPnL = longPnL + shortPnL
        
        // Action recommendation
        action = getSignalRecommendation(totalPnL, longWinRate, shortWinRate, maxDD, longTrades + shortTrades)
        
        // Populate row with color coding
        table.cell(performanceTable, 0, row, str.substring(signalNames[signalIndex], 0, 8), text_size=size.small)
        table.cell(performanceTable, 1, row, str.tostring(longTrades), text_size=size.small)
        table.cell(performanceTable, 2, row, str.tostring(longWinRate, "#.1") + "%", 
                  text_color=longWinRate >= 60 ? color.lime : longWinRate >= 45 ? color.yellow : color.red, text_size=size.small)
        table.cell(performanceTable, 3, row, "$" + str.tostring(longPnL, "#"), 
                  text_color=longPnL > 0 ? color.lime : color.red, text_size=size.small)
        table.cell(performanceTable, 4, row, str.tostring(shortTrades), text_size=size.small)
        table.cell(performanceTable, 5, row, str.tostring(shortWinRate, "#.1") + "%", 
                  text_color=shortWinRate >= 60 ? color.lime : shortWinRate >= 45 ? color.yellow : color.red, text_size=size.small)
        table.cell(performanceTable, 6, row, "$" + str.tostring(shortPnL, "#"), 
                  text_color=shortPnL > 0 ? color.lime : color.red, text_size=size.small)
        table.cell(performanceTable, 7, row, "$" + str.tostring(maxDD, "#"), text_color=color.red, text_size=size.small)
        table.cell(performanceTable, 8, row, action, 
                  text_color=getActionColor(action), text_size=size.small)
```

### **Dynamic Table Optimization:**
```pine
// Optimize table size based on enabled signals
calculateOptimalTableSize() =>
    enabledCount = 0
    for i = 0 to 9
        if getSignalEnabled(i)
            enabledCount += 1
    math.max(3, enabledCount + 1)  // Header + enabled signals

// Memory-efficient table management
if debugLevel == "OFF"
    if not na(debugTable)
        table.clear(debugTable)
if not showBacktestTable  
    if not na(performanceTable)
        table.clear(performanceTable)
```

---

### **Phase 5: Parameter Change Detection & Array Reset** 🔄 *Priority: HIGH*

**Critical Issue from Analysis**: Arrays don't reset when parameters change

**Solution**:
```pine
// Detect parameter changes and reset arrays
param_hash = str.tostring(signal1Enable) + str.tostring(signal2Enable) + 
             str.tostring(signal3Enable) + str.tostring(maExitOn) + 
             str.tostring(fixedEnable) + str.tostring(trendChangeExitEnable)

if param_hash != param_hash[1]
    // Parameters changed - reset all virtual accounts
    for i = 0 to 9
        array.set(_isLive, i, false)
        array.set(_entryPx, i, na)
        array.set(_eqPeak, i, 0.0)
        array.set(_eqLow, i, na)
        
        // Reset performance arrays
        signalData = array.get(signals, i)
        signalData.longTrades := 0
        signalData.shortTrades := 0
        signalData.longProfits := 0.0
        signalData.shortProfits := 0.0
        // ... reset all metrics
```

---

### **Phase 6: Advanced Debugging & Validation System** 🔍 *Priority: MEDIUM*

**From Analysis**: Comprehensive debug system for accuracy validation

**Debug Features**:
```pine
// Debug table showing real-time signal status
debugSignalTracking = input.bool(false, '🔍 Debug Signal Tracking', group='🛠️ Debug')

if debugSignalTracking
    var table debugTable = table.new(position.top_left, 5, 12)
    
    // Show live signal states
    for i = 0 to 9
        signalActive = getSignalStatus(i)
        isLive = array.get(_isLive, i)
        currentPnL = calculateUnrealizedPnL(i)
        
        table.cell(debugTable, 0, i+1, signalNames[i])
        table.cell(debugTable, 1, i+1, signalActive ? '🟢' : '⚫')
        table.cell(debugTable, 2, i+1, isLive ? 'LIVE' : 'FLAT')
        table.cell(debugTable, 3, i+1, '$' + str.tostring(currentPnL))
        table.cell(debugTable, 4, i+1, 'Contracts: ' + str.tostring(array.get(_qty, i)))

// Validation against TradingView results
validateResults() =>
    calculated_pnl = 0.0
    for i = 0 to 9
        signalData = array.get(signals, i)
        calculated_pnl += signalData.longProfits + signalData.shortProfits
    
    tv_net_profit = strategy.netprofit
    difference = math.abs(calculated_pnl - tv_net_profit)
    
    if difference > 50  // Alert if difference > $50
        alert("P&L Mismatch: Calculated $" + str.tostring(calculated_pnl) + 
              " vs TradingView $" + str.tostring(tv_net_profit))
```

---

## 🚀 **IMPLEMENTATION TIMELINE**

### **Week 1: Virtual Architecture** 
- [ ] Phase 1: Virtual Signal Architecture
- [ ] Phase 5: Parameter Change Detection
- [ ] Test with 2-3 signals first

### **Week 2: Accurate Calculations**
- [ ] Phase 2: P&L Engine with Commission/Slippage
- [ ] Phase 3: Advanced Drawdown System
- [ ] Validation against known benchmarks

### **Week 3: Enhanced Display**
- [ ] Phase 4: Long/Short Separated Table
- [ ] Real-time update mechanisms
- [ ] Performance optimization

### **Week 4: Advanced Features**
- [ ] Phase 6: Debug & Validation System
- [ ] Advanced metrics (Profit Factor, Sharpe)
- [ ] Comprehensive testing

---

## 💡 **CRITICAL INSIGHTS FROM LINE-BY-LINE ANALYSIS**

### **🔥 Major Discoveries:**

1. **Virtual Signal Backtesting** (Lines 334-414): The key breakthrough is running 10 parallel virtual accounts instead of trying to fix attribution in combined signals.

2. **Commission Integration** (Lines 706-715): Must include realistic trading costs:
   - Commission: $4 round-trip per contract
   - Slippage: 1 tick per entry/exit
   - Use `syminfo.pointvalue` for automatic multipliers

3. **Parameter Change Detection** (Lines 930-940): Critical for real-time updates:
   ```pine
   param_hash = str.tostring(signal1Enable) + str.tostring(signal2Enable) + ...
   if param_hash != param_hash[1]
       resetPerformanceTracking()
   ```

4. **Proper Drawdown Calculation** (Lines 408-414): Track peak-to-trough during OPEN trades:
   ```pine
   draw[i] := equity_peak[i] - running_equity[i]
   dd_max[i] := math.max(dd_max[i], draw[i])
   ```

5. **Individual Entry Tracking** (Lines 905-923): Use maps for perfect attribution:
   ```pine
   strategy.entry("Long_" + str.tostring(signal_id), strategy.long, qty=1)
   ```

6. **Performance Optimization** (Lines 963-989): Only calculate when positions change:
   ```pine
   calculations_needed := strategy.position_size != strategy.position_size[1] or 
                         strategy.closedtrades != strategy.closedtrades[1]
   ```

7. **Advanced Metrics** (Lines 777-811): Add Profit Factor, Sharpe Ratio, correlation analysis

8. **Memory Management** (Lines 947-959): Circular buffers to prevent array overflow:
   ```pine
   if array.size(arr) > MAX_HISTORY
       array.shift(arr)  // Remove oldest element
   ```

### **🎯 Updated Success Criteria:**

- ✅ Virtual accounts provide perfect signal isolation
- ✅ Commission/slippage included for realistic results  
- ✅ Parameter changes trigger immediate array resets
- ✅ Drawdown calculated during open trades (not just closed)
- ✅ Debug system validates against TradingView results
- ✅ Memory management prevents performance degradation
- ✅ Advanced metrics (PF, Sharpe) for deeper analysis

---

## 🔧 **TECHNICAL IMPLEMENTATION DETAILS**

### **Futures Contract Specifications** (Lines 447-453):
```pine
// Automatic point value detection
pointValue = syminfo.pointvalue  // NQ=$20, ES=$50, YM=$5, RTY=$50

// Manual override for specific contracts
futuresMultiplier = input.int(20, "Manual Multiplier Override", 
                             tooltip="Leave 0 for auto-detection")
actualMultiplier = futuresMultiplier == 0 ? syminfo.pointvalue : futuresMultiplier
```

### **Signal Correlation Analysis** (Lines 992-1014):
```pine
calculateSignalCorrelation(signal1_idx, signal2_idx, lookback) =>
    both_wins = 0
    for i = 0 to lookback - 1
        if getHistoricalWin(signal1_idx, i) and getHistoricalWin(signal2_idx, i)
            both_wins += 1
    correlation = both_wins / math.min(signal1_wins, signal2_wins)
```

### **Market Condition Adaptation** (Lines 1016-1038):
```pine
analyzeMarketCondition() =>
    volatility = ta.atr(14) / ta.sma(ta.atr(14), 50)
    trend_strength = ta.adx(14)
    
    condition = volatility > 1.5 ? "HIGH_VOL" : 
                volatility < 0.7 ? "LOW_VOL" : 
                trend_strength > 25 ? "TRENDING" : "RANGING"
```

---

This updated plan now incorporates **every critical insight** from the 7 AI analyses, providing a robust foundation for finally solving the individual signal backtesting challenge. The virtual account approach is the key breakthrough that eliminates all attribution issues while maintaining perfect accuracy.

---

*This enhanced plan addresses all major findings from the comprehensive AI analysis and provides the definitive roadmap for implementing accurate, real-time individual signal backtesting.* 