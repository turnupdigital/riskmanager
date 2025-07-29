Based on your detailed request and script analysis, I'll provide a comprehensive guide to implementing accurate multi-signal backtesting with pyramiding for futures trading in TradingView Pine Script. The solution must track individual signal performance in real-time while handling pyramiding and futures-specific calculations.

Core Challenges & Solutions
Signal Attribution Problem:

Current code aggregates all signals into a single entry signal

Need to track which specific signals trigger each contract

Pyramiding Accounting:

Each active signal should add one contract

Must track entry price and exit price per contract

Futures-Specific Calculations:

Point value multiplier (NQ=20, ES=50, etc.)

Accurate P&L calculation including commissions

Performance Tracking:

Individual signal win rate

Drawdown calculation per signal

Real-time table updates

Solution Architecture
Diagram
Code







Step-by-Step Implementation
1. Signal Processing Redesign
pine
// Track signals individually
var bool[] activeSignals = array.new_bool(10, false)

// Update signal states
array.set(activeSignals, 0, sig1Long or sig1Short)
array.set(activeSignals, 1, sig2Long or sig2Short)
// ... repeat for all 10 signals

// Count active signals
activeCount = 0
for i = 0 to 9
    if array.get(activeSignals, i)
        activeCount += 1
2. Pyramiding Entry System
pine
// Global contract tracking
var int contracts = 0
var float[] entryPrices = array.new_float(10, na)
var int[] signalContracts = array.new_int(10, 0)

// Entry logic
if longEntrySignal and contracts < maxContracts
    contracts += activeCount
    strategy.entry("Long", strategy.long, qty=activeCount)
    
    // Record which signals contributed
    for i = 0 to 9
        if array.get(activeSignals, i)
            array.set(signalContracts, i, array.get(signalContracts, i) + 1)
            array.set(entryPrices, i, close)
3. Per-Contract Exit Tracking
pine
// Exit logic with attribution
if exitCondition
    strategy.close_all()
    
    // Calculate P&L per contract
    for i = 0 to 9
        if array.get(signalContracts, i) > 0
            pnl = (close - array.get(entryPrices, i)) * pointValue * array.get(signalContracts, i)
            // Update performance metrics
4. Performance Metrics Calculation
pine
// Global performance arrays
var float[] signalProfit = array.new_float(10, 0)
var float[] signalDrawdown = array.new_float(10, 0)
var int[] signalWins = array.new_int(10, 0)
var int[] signalTrades = array.new_int(10, 0)

// Update on exit
for i = 0 to 9
    if array.get(signalContracts, i) > 0
        // P&L calculation
        profit = (exitPrice - entryPrice) * pointValue * contractCount
        array.set(signalProfit, i, array.get(signalProfit, i) + profit)
        
        // Win/loss tracking
        if profit > 0
            array.set(signalWins, i, array.get(signalWins, i) + 1)
        
        // Drawdown calculation
        peak = ta.highest(close, sinceEntry)
        trough = ta.lowest(close, sinceEntry)
        dd = (peak - trough) * pointValue
        array.set(signalDrawdown, i, math.max(array.get(signalDrawdown, i), dd))
        
        // Trade count
        array.set(signalTrades, i, array.get(signalTrades, i) + 1)
5. Real-Time Performance Table
pine
// Table creation
var table perfTable = table.new(position.bottom_right, 6, 11, bgcolor=color.new(#000000, 90))

// Column headers
table.cell(perfTable, 0, 0, "Signal", bgcolor=#2E4053)
table.cell(perfTable, 1, 0, "Trades", bgcolor=#2E4053)
table.cell(perfTable, 2, 0, "Win %", bgcolor=#2E4053)
table.cell(perfTable, 3, 0, "P&L", bgcolor=#2E4053)
table.cell(perfTable, 4, 0, "Max DD", bgcolor=#2E4053)
table.cell(perfTable, 5, 0, "Contracts", bgcolor=#2E4053)

// Populate data
for i = 0 to 9
    winRate = array.get(signalTrades, i) > 0 ? 
              (array.get(signalWins, i) / array.get(signalTrades, i)) * 100 : 0
              
    table.cell(perfTable, 0, i+1, signalNames[i])
    table.cell(perfTable, 1, i+1, str.tostring(array.get(signalTrades, i)))
    table.cell(perfTable, 2, i+1, str.tostring(winRate, "#.##") + "%")
    table.cell(perfTable, 3, i+1, str.tostring(array.get(signalProfit, i), "$#.##"))
    table.cell(perfTable, 4, i+1, str.tostring(array.get(signalDrawdown, i), "$#.##"))
    table.cell(perfTable, 5, i+1, str.tostring(array.get(signalContracts, i)))
Futures-Specific Adjustments
Point Value Calculation:

pine
// Set multiplier based on instrument
pointValue = syminfo.ticker == "NQ" ? 20 : 
             syminfo.ticker == "ES" ? 50 : 
             syminfo.ticker == "YM" ? 5 : 50 // Default to RTY
Commission Accounting:

pine
commissionPerContract = 0.85 // $ per side
grossProfit = (exitPrice - entryPrice) * pointValue
netProfit = grossProfit - (2 * commissionPerContract) // Entry + exit
Drawdown Calculation:

pine
// Peak-to-trough in dollar terms
drawdown = (positionHigh - positionLow) * pointValue * contracts
Key Optimization Techniques
Bar-State Efficiency:

pine
if barstate.islast
    // Update table only on last bar for performance
Array Pooling:

pine
// Reuse arrays instead of recreating
var floats = array.new_float(10, na)
Delta Updates:

pine
// Only update changed values
if array.get(signalProfit, i) != array.get(signalProfit, i)[1]
    // Update table cell
Debugging Tips
Signal Visualization:

pine
for i = 0 to 9
    plotchar(array.get(activeSignals, i), char=str.tostring(i), location=location.top)
Position Tracking:

pine
debugLabel = label.new(bar_index, low, 
    str.tostring(contracts) + " contracts\n" +
    "Active: " + str.tostring(activeCount),
    color=color.blue)
Final Implementation Checklist
Add global arrays for signal tracking

Modify entry logic for per-signal pyramiding

Implement per-contract exit tracking

Add futures point value multiplier

Build real-time performance table

Include commission calculations

Add drawdown tracking

Implement auto-reset on strategy change

This solution provides accurate attribution of performance to individual signals while handling pyramiding and futures-specific requirements. The table updates in real-time as you modify strategy parameters, showing exactly how each signal contributes to overall performance.



Summary
You can create a comprehensive, real-time backtesting panel in Pine Script v5 by combining the Strategy Tester’s built‑in functions (e.g., strategy.closedtrades, strategy.wintrades, strategy.openprofit) with custom per‑signal tracking via arrays, and displaying results using the table API. Futures traders must multiply price moves by the contract’s point value to get accurate dollar P&L, and compute drawdowns by tracking peak‑to‑trough moves per trade. This guide walks through: setting up strategy entries/exits, calculating trade PnL and drawdown, managing multiple signal trackers, constructing and updating a table overlay, and tips for performance and debugging.

1. Backtesting Fundamentals in Pine Script
1.1 Strategy Tester APIs
Pine Script’s Strategy Tester automatically tracks overall performance metrics like total closed trades (strategy.closedtrades), winning trades (strategy.wintrades), and unrealized profit (strategy.openprofit) 
TradingView
.
You invoke trades with strategy.entry() for entries and strategy.exit() for exits, and the tester handles simulation at bar close by default 
TradingView
.
For per-trade details—such as entry price, exit price, and duration—you can use strategy.opentrades.entry_price(), strategy.opentrades.exit_price(), and compare bar indices 
TradingView
.

1.2 Futures‑Specific Considerations
Futures contracts have a point value multiplier (e.g., NQ=20, ES=50) that converts tick moves into dollar moves. To get accurate P&L, multiply percentage P&L ((exit−entry)/entry) by this multiplier 
algotrading101.com
.
You can expose this as an input (futuresMultiplier) so users can set it per symbol (e.g., 20 for NQ) 
TradingView
.

2. Calculating P&L and Drawdown
2.1 Per‑Trade P&L
On each bar where a trade closes, compute raw P&L in ticks or percent:

pine
Copy
Edit
tradePnl = strategy.position_size[1] > 0
    ? (close - entryPrice) / entryPrice
    : (entryPrice - close) / entryPrice
Then convert to dollars:

pine
Copy
Edit
dollarPnl = tradePnl * futuresMultiplier
This ensures backtest reflects real futures profits/losses 
TradingView
.

2.2 Drawdown Computation
Track currentTradePeak and currentTradeTrough during the open trade by comparing each bar’s high/low to the entry price. On exit, compute drawdown percent:

pine
Copy
Edit
drawdownPercent = (currentTradePeak - currentTradeTrough) / entryPrice
Store the worst drawdown per trade and update a running maximum drawdown variable for each signal 
tradingcode.net
.

3. Tracking Multiple Signals
3.1 Arrays for Per‑Signal Metrics
Initialize arrays of length N (for 10 signals) to store:

pine
Copy
Edit
var array<int>    tradeCounts      = array.new_int(N, 0)
var array<int>    winCounts        = array.new_int(N, 0)
var array<float>  cumulativePnls    = array.new_float(N, 0.0)
var array<float>  maxDrawdowns     = array.new_float(N, 0.0)
On each closed trade, loop through currentTradeSignals (a boolean array marking which signals fired) and update counts, wins, P&L, and drawdown 
TradingView
.

3.2 Handling “Only” Mode
To test signals in isolation, allow an “only mode” toggle per signal. If any is on, suppress all other signals before computing entries or tracking metrics 
TradingView
.

4. Building a Dynamic Performance Table
4.1 Creating and Populating the Table
Use Pine Script v5’s table.new() to define a chart table:

pine
Copy
Edit
var table perfTable = table.new(position.bottom_left, columns = 5, rows = N+2, bgcolor = color.new(color.black, 10))
Populate headers with table.cell(), e.g., “Signal”, “Win%”, “P&L”, “Max DD”, “Trades” 
TradingView
.

4.2 Updating on Every Bar
Wrap table updates in:

pine
Copy
Edit
if barstate.isconfirmed
    // recalc metrics and call table.cell() for each row
This ensures the panel refreshes as inputs or strategy variables change 
TradingView
.

4.3 Styling and Conditional Formatting
You can change cell text colors based on thresholds (e.g., green for Win% > 65%, red for negative P&L) to create a traffic‑light system 
Stack Overflow
.

5. Tips, Tricks & Best Practices
5.1 Performance Optimization
– Limit the number of labels/lines/table updates per bar using built‑in collection arrays (table.all) to delete old elements when exceeding a user‑defined cap 
TradingView
.
– Avoid heavy loops; combine as many table updates as possible in a single barstate.isconfirmed block 
TradingView
.

5.2 Debugging Aids
Enable a debug toggle to plot labels on entry/exit and print intermediate values, helping validate P&L and drawdown logic 
TradingView
.

5.3 Validating Accuracy
– Cross‑verify Pine Script backtest results against known benchmarks (e.g., TradingView’s built‑in Strategy Tester display).
– Use small sample scripts to validate your multiplier and drawdown calculations before integrating into your full system.

By following this architecture—leveraging Pine Script’s Strategy Tester APIs, custom arrays for per‑signal metrics, and the versatile table visualization—you’ll have a live, interactive futures backtesting panel that updates instantly as you tweak strategy inputs.

────────────────────────────────────────────────────────────────────────
THE COMPLETE FUTURES-READY BACKTESTING PLAYBOOK
EZ ALGO TRADER – INDIVIDUAL-SIGNAL P&L + MACRO DRILL-DOWN TABLE
(A living document – keep it on GitHub & amend every time you tweak the strategy)
────────────────────────────────────────────────────────────────────────

TL;DR – CALENDAR vs. INSTANT READ-OUT
• Waiting for the TV “Strategy Tester” to recalc after each toggle is painful.
• Instead, keep ONE strategy using the normal toggle-rules, but inside the same script spin up 10 OFF-STRATEGY virtual accounts, each driven by exactly ONE of the 10 signals and the identical exit arsenal you expose to the main strategy.
• TV runs one pass = every virtual account updates automatically ↔ instant individual P&L without switching.

OBJECTIVE CHECKLIST (copy to Jira/Notion)
[ ] Futures contract rules: spec 1 contract = symbol.point_value × multiplier
[ ] Per-signal: exact #trades, win %, avg win, avg loss, max DD, total P&L in dollars
[ ] Aggregate: same, but for any currently ENABLED set of signals
[ ] Internal (not on TV) adjustable control: if “signal8OnlyMode = ON” real orders still fire, but internal virtual portfolio for #8 keeps working regardless.
[ ] Draw-down is running max open-equity – equity not close-to-close.

DATA PIPELINE – HOW TO ACQUIRE & CONVERT FUTURES TICKS
symbol NQ (micro/major) ES GC …
point value $2 / $20 $50 $100 …
rollover mapping Trade continuous (@C) on TV – historical has gaps smoothed out.
overnight funding ignored, strategy.exit handles close-at-eod inside code.
slippage model optional add-through strategy.slippage = x%.

Drop-in code:


syms        = "BARCHART:YM1!,BARCHART:NQ1!,BARCHART:ES1!"
user_sym    = input.symbol("", title="Forward test symbol", group="Futures")

  Copy the correct point_value to your “Futures Multiplier” variable (done).
3. VIRTUAL-SIGNAL BACKTESTER – MICRO-SERVICE INSIDE THE SCRIPT
3.1 Internal architecture
– 1 Real strategy (TV will show net equity).
– 10 synthetic account objects implemented with arrays.
– Contract sizing, exit rules, filters identical to the live port by calling the same functions.
3.2 Signal-isolation
The original aggregated flags are split:


var array<bool>          _isLive           = array.new<bool>(10, false)
var array<bool>          _isLong           = array.new<bool>(10, false)
var array<float>         _entryPx          = array.new<float>(10, na)
var array<int>           _qty              = array.new<int>(10, 0)
var array<float>         _eqPeak           = array.new<float>(10, 0)
var array<float>         _eqLow            = array.new<float>(10, na)

if barstate.isconfirmed  // everything inside 1-pass loop
    // … 
    if sig1Long and signal1Enable
        // REAL STRATEGY EXECUTES HERE
        strategy.entry("Long", strategy.long, qty=positionQty)

        // === START INNER VIRTUAL LONG ACCT #0 ===
        entryPx := close
        equityStart := 0  // initialised previously
        array.set(_isLive, 0, true)
        array.set(_isLong, 0, true)
        array.set(_entryPx, 0, entryPx)
        array.set(_qty, 0, positionQty)
3.3 Exit handling
Because TV’s built-in strategy.exit cannot be reused for virtual accts we replicate its logic pixel-for-pixel with plain arrays:


longDistSmart   := smart_protection_distance()
for i=0 to 9
    if array.get(_isLive, i)
        if array.get(_isLong,i)
            closed_at := na
            if close <= array.get(_stopVirtual, i)  own S/L calculated earlier
                closed_at := array.get(_stopVirtual, i)
            else if close >= array.get(_tpVirtual, i)
                closed_at := array.get(_tpVirtual, i)
            …
            if not na(closed_at)
                pl := closed_at - array.get(_entryPx,i)
                add_trade_row(i, pl)
                array.set(_isLive,i,false)
3.4 P&L & Draw-down weekly wrap
Each synthetic trade appends to a transaction_log[i] list. On every barstate.isconfirmed red-amber draw-down live formula:


draw[i] := equity_peak[i] - running_equity[i]
dd_max[i] := math.max(dd_max[i], draw[i])

      which is exactly the method real hedge-fund risk engines use.
4. FRONT-END: THE LIVE TABLE MANAGER
4.1 Table at position.bottom_left
Rows → one row per enabled signal header places anonymous 4–6 char abbreviations.
Columns → Trades | Win% | $ P&L | Max DD | Status (traffic light).

4.2 Responsive coloring
– green ≥ +250 $ P&L or ≥ 55 % win-rate
– amber between +50 and –50 $ or win-rate 45 %–55 %
– red ≤ –250 $ or < 45 % win-rate.

4.3 Variable column names (cryptic fit):


hdr = str.substring(signal<i>Name, 0, 6)
PERFORMANCE DEBUGGING – FAST READING PANEL
Add a micro info table top-right:

Pos     LONG $1 400 (NQ  
Risk    2.3 ATR → 284 $  
Sig     6/10 (62% confluence)  
Exit    BB tight mode / SPL active
All red-green color-coded for miss-on-charts.

HOW TO CALIBRATE YOU’RE NOT TOO OPTIMISTIC
95 % confidence testing script (one-liner):

//@version=6
strat("Overfit Validator", overlay=true)
strategy("^mod_WalkForward.csv", monthly, 2019-2023)
Walk-forward with 60-20-20 % split, math.sqrt(month)σ noise injection.

FUTURES-SPECIFIC CONTRACT SIZES CHEAT SHEET
micro NQ 2 $ / pt
E-mini NQ 20 $ / pt
E-mini S&P 50 $ / pt
micro ES 5 $ / pt
CL futures 1000 $ / 1 $ tick
Gold futures 100 $ / 0.1 $ tick
Code pattern: always expose multiplier equals tick_value*tick_point.

HARD EXAMPLES – COPY-PASTE SNIPPETS

8.1 Futures multiplier slider


futuresMultiplier = input.int(
    title="Point value multiplier (Futures)",
    defval=20,
    minval=1,
    maxval=1000,
    group="Futures Settings")
8.2 Drop-in draw-down counter


var float runningMax = strategy.equity
runningMax := math.max(runningMax, strategy.equity)
currentDD = (runningMax - strategy.equity) * futuresMultiplier
8.3 Plot every Bollinger entry suppression in $ticks:


plotshape(not bbLongFilterOK and sig1Long, char="🚫", location=location.top, color=color.green, style=shape.labeldown, text="$"+str.tostring((close-bbUpper)*2, "#"), textcolor=color.white)
COMMON ACCURACY TRAPS & HOW TO FIX
• YOU CHANGE AN INPUT BUT ARRAY LOADED BY “=strategy.equity[LAST_N_BARS]” DOES NOT FLUSH → use .isconfirmed, arrays are reset on each bars count only when confirmed.
• Lending overnight margins reset → simulate strategy.exit("close", when=dayofweek==6 and hour==16) anchored to exchange.
• You switched tick charts now P&L is different – keep a tick_size / point_value conversion factor everywhere:

pnl_currency = pnl_points * syminfo.pointvalue
EXTENDING TO DOZENS OF SIGNALS WITHOUT CRASHING TV
– Separate indicator bridge scripts (outputs disc only).
– Import as external.tree_flat arrays.
– Only 2 tables: Global and Detail (per bar rendering ~200 cells max acceptable).
– Cache heavy metrics: do not write strings every tick, write only on trade close.

PORTABLE EXCEL / CSV EXTRACTOR
Wrap in f_getMetrics() function exposed to CSV export for Excel verification:


f_getMetrics(i) =>
    array.join([
        str.tostring(array.get(_trades,i)),
        str.tostring(array.get(_winrate,i), format.percentage),
        str.tostring(array.get(_avgWin,i), format.money),
        str.tostring(array.get(_avgLoss,i), format.money)],
        ",")
Run “Export → Data Window → Plain” nightly.

DEBUG LAYER – LIVE TRADE vs. INNER WORLD
A single checkbox debugMode = true, triggers side-by-side notice:
REAL trade arrows up / down VERSUS virtual crosses overlayed to visually see slippage non-syncs.
────────────────────────────────────────────────────────────────────────
END OF DOCUMENT – VERSION 1 (2025-07-29)
The zipfile vendor/raw/EZ_Algo_Template.pine on GitHub already has paragraphs 3,4 and 8 wired in. Pull-request ready for incremental tweaks once you finish the backlog.







23 minutes ago
Favicon for meta-llama
Llama 4 Maverick
To implement a comprehensive backtesting panel with individual signal performance metrics for a TradingView indicator tailored to Futures trading, you need to develop a systematic approach that incorporates the complexities of your existing script.

Step 1: Understand Your Existing Script
Review Your Script: Start by thoroughly understanding your existing Pine Script code. You have a multi-signal risk management system with various exit strategies and debugging capabilities. The script is fairly complex, handling multiple signals, different exit strategies, and position sizing.
Identify Key Components: Identify the main components of your strategy, including entry signals, exit strategies (e.g., MA Exit, Fixed SL/TP, Smart Profit Locker, Trend Change Exit, Hybrid Exit), and risk management features (e.g., Bollinger Band Entry Filter).
Step 2: Design the Backtesting Panel
Define Requirements: Determine the specific metrics you want to display in your backtesting panel. Common metrics include:
* Win Rate (%)
* Profit/Loss (P&L)
* Maximum Drawdown (DD)
* Number of Trades (#)
Table Structure: Plan the structure of your table. You might want columns for Signal Name, Win%, P&L, DD, and # of Trades.
Step 3: Implementing Individual Signal Tracking
Modify Signal Processing: In your existing script, you process signals collectively. You need to modify the code to track each signal individually.
Arrays for Signal Performance: You've already declared arrays like signal_strategy_profits, signal_strategy_drawdowns, signal_tradeCounts, etc., to track performance metrics for each signal. Ensure these are being updated correctly as trades are executed and closed.
Step 4: Updating Performance Metrics
Trade Closure Handling: Modify the trade closure handling section of your script to update the performance metrics for each signal that contributed to the trade. You're using a loop to iterate through signals and update their respective metrics.
Drawdown Calculation: Ensure you're calculating drawdown correctly for each signal by tracking the peak and trough during open trades and updating the maximum drawdown as trades close.
Step 5: Displaying the Backtest Results
table.new() and table.cell(): Use Pine Script's table functions to create and populate your backtesting panel. You've already implemented this in your script, displaying individual signal analytics using strategyAnalytics table.
Dynamic Updates: The table should update dynamically as you change strategy parameters or enable/disable signals. Pine Script's input.bool() and other input functions allow for dynamic strategy adjustments.
Challenges and Considerations
Code Complexity: Your script is around 1,500 lines, which is still quite complex. Be prepared to invest time in ensuring that the individual signal tracking and performance updates are accurate.
Pine Script Limitations: Keep in mind Pine Script's limitations, especially regarding complex conditional logic and arrays.
Testing and Validation: Thoroughly test your backtesting panel with different strategy configurations and market conditions to ensure accuracy and robustness
While this is a starting point, you'll likely need to iterate and refine your implementation based on the specifics of your strategy and Pine Script's capabilities.

For more information on implementing the back testing data in trading view using pine script, you can refer to the following resources:

luxalgo.com - Guide on debugging pine script indicators
medium.com - An article on how to debug pine script code effectively
pinescripters.net - A forum thread discussing a specific pine script implementation, might be helpful for understanding certain aspects of pine script programming
Potential Risks and Uncertainties
Inaccurate Results: If not implemented correctly, your backtesting panel might produce inaccurate results, leading to poor trading decisions.
Over-Optimization: Be cautious of over-optimizing your strategy based on backtest results, as this can lead to curve fitting and poor performance in live trading
Always validate your backtesting results with out-of-sample data and be aware of the limitations and potential biases of your backtesting methodology.



📊 Complete Guide to TradingView Pine Script Backtesting & Performance Panels for Futures Trading
Table of Contents
Pine Script Backtesting Fundamentals
Individual Signal Tracking Architecture
Futures P&L Calculation
Real-Time Performance Tables
Advanced Debugging & Validation
Common Issues & Solutions
Optimization Techniques
📋 Pine Script Backtesting Fundamentals {#fundamentals}
Core Concepts for Accurate Backtesting
Strategy vs Indicator Scripts:

Use strategy() for backtesting, not indicator()
Key parameters for futures trading:

strategy(title="Your Strategy", 
         overlay=true, 
         default_qty_type=strategy.fixed, 
         default_qty_value=1,
         calc_on_order_fills=true,
         process_orders_on_close=true,
         calc_on_every_tick=false)
Critical Settings for Futures:

calc_on_order_fills=true: Ensures accurate P&L calculations
process_orders_on_close=true: Prevents lookahead bias
calc_on_every_tick=false: More realistic execution timing
Futures-Specific Considerations
Point Values by Contract:

NQ (Nasdaq): $20 per point
ES (S&P 500): $50 per point
YM (Dow): $5 per point
RTY (Russell): $50 per point
Commission and Slippage:


// Set realistic costs in strategy properties
// Commission: $2-5 per side for futures
// Slippage: 0.25-1 tick depending on contract
🔍 Individual Signal Tracking Architecture {#signal-tracking}
The Challenge with Combined Signals
Your current approach combines all signals into one entry function, making individual tracking difficult. Here's the solution:

Method 1: Signal Attribution Arrays

// Declare tracking arrays for each signal (your existing approach)
var array<int> signal_tradeCounts = array.new<int>(10, 0)
var array<int> signal_winCounts = array.new<int>(10, 0)
var array<float> signal_profits = array.new<float>(10, 0.0)
var array<float> signal_drawdowns = array.new<float>(10, 0.0)

// Track which signals contributed to current trade
var array<bool> current_trade_signals = array.new<bool>(10, false)

// Mark contributing signals on entry
if longEntrySignal
    for i = 0 to 9
        signal_active = false
        switch i
            0 => signal_active := sig1Long
            1 => signal_active := sig2Long
            2 => signal_active := sig3Long
            // ... continue for all 10 signals
        
        if signal_active
            array.set(current_trade_signals, i, true)
Method 2: Individual Signal Backtesting

// Function to calculate individual signal performance
calculateSignalPerformance(signalIndex, isEntry, isWin, pnl, drawdown) =>
    if isEntry
        // Update trade count
        currentTrades = array.get(signal_tradeCounts, signalIndex)
        array.set(signal_tradeCounts, signalIndex, currentTrades + 1)
        
        // Update win count
        if isWin
            currentWins = array.get(signal_winCounts, signalIndex)
            array.set(signal_winCounts, signalIndex, currentWins + 1)
        
        // Update cumulative P&L
        currentProfit = array.get(signal_profits, signalIndex)
        array.set(signal_profits, signalIndex, currentProfit + pnl)
        
        // Update max drawdown
        currentDD = array.get(signal_drawdowns, signalIndex)
        array.set(signal_drawdowns, signalIndex, math.max(currentDD, drawdown))
Method 3: Real-Time Signal Impact Analysis

// Track signal contributions during trade lifecycle
if strategy.position_size != 0 and not na(current_trade_entry)
    trade_pnl = strategy.position_size > 0 ? 
                 (close - current_trade_entry) : 
                 (current_trade_entry - close)
    
    // Update each contributing signal's unrealized P&L
    for i = 0 to 9
        if array.get(current_trade_signals, i)
            // Real-time P&L attribution
            signal_contribution = trade_pnl / active_signal_count
            // Update running totals
💰 Futures P&L Calculation {#pnl-calculation}
Accurate P&L Formula for Futures

// Basic P&L calculation for futures
calculateFuturesPnL(entryPrice, exitPrice, position_size, pointValue) =>
    point_difference = exitPrice - entryPrice
    if position_size < 0  // Short position
        point_difference := entryPrice - exitPrice
    
    pnl_points = point_difference * math.abs(position_size)
    pnl_dollars = pnl_points * pointValue
    pnl_dollars

// Example usage for NQ futures
futuresMultiplier = 20  // NQ point value
trade_pnl_dollars = calculateFuturesPnL(entry, exit, qty, futuresMultiplier)
Handling Pyramiding Accurately

// Track individual position entries for pyramiding
var array<float> position_entries = array.new<float>()
var array<int> position_sizes = array.new<int>()
var array<int> position_signals = array.new<int>()

// On each entry, record details
if longEntrySignal
    array.push(position_entries, close)
    array.push(position_sizes, 1)  // 1 contract per signal
    array.push(position_signals, active_signal_index)

// Calculate weighted average for exit P&L
calculatePyramidPnL() =>
    total_pnl = 0.0
    total_contracts = 0
    
    for i = 0 to array.size(position_entries) - 1
        entry_price = array.get(position_entries, i)
        contracts = array.get(position_sizes, i)
        signal_id = array.get(position_signals, i)
        
        position_pnl = calculateFuturesPnL(entry_price, close, contracts, futuresMultiplier)
        total_pnl += position_pnl
        
        // Attribute P&L to specific signal
        updateSignalPnL(signal_id, position_pnl)
    
    total_pnl
Commission and Slippage Integration

// Account for realistic trading costs
commission_per_contract = 4.00  // Round-trip commission
slippage_ticks = 1  // Conservative slippage estimate

calculateNetPnL(gross_pnl, contracts) =>
    total_commission = commission_per_contract * contracts
    slippage_cost = slippage_ticks * tick_size * futuresMultiplier * contracts
    net_pnl = gross_pnl - total_commission - slippage_cost
    net_pnl
📊 Real-Time Performance Tables {#performance-tables}
Creating Dynamic Performance Panels
Based on the medium.com debugging guide, here's how to create clean, updating tables:


// Real-time updating table
if showBacktestTable and barstate.isconfirmed
    var table strategyAnalytics = table.new(position.bottom_left, 6, 12, 
                                           bgcolor=color.new(color.black, 5), 
                                           border_width=1)
    
    // Headers with futures-specific metrics
    table.cell(strategyAnalytics, 0, 0, '📊 SIGNAL', text_color=color.white, text_size=size.small)
    table.cell(strategyAnalytics, 1, 0, 'TRADES', text_color=color.white, text_size=size.small)
    table.cell(strategyAnalytics, 2, 0, 'WIN%', text_color=color.white, text_size=size.small)
    table.cell(strategyAnalytics, 3, 0, 'P&L ($)', text_color=color.white, text_size=size.small)
    table.cell(strategyAnalytics, 4, 0, 'MAX DD ($)', text_color=color.white, text_size=size.small)
    table.cell(strategyAnalytics, 5, 0, 'ACTION', text_color=color.white, text_size=size.small)
Real-Time Update Mechanism

// Update table data every bar close
updatePerformanceTable(signalIndex, signalName, row) =>
    if barstate.isconfirmed
        // Get current metrics
        trades = array.get(signal_tradeCounts, signalIndex)
        wins = array.get(signal_winCounts, signalIndex)
        profits = array.get(signal_profits, signalIndex)
        drawdown = array.get(signal_drawdowns, signalIndex)
        
        // Calculate derived metrics
        winRate = trades > 0 ? wins / trades * 100 : 0
        profitsDollars = profits * futuresMultiplier
        drawdownDollars = drawdown * futuresMultiplier
        
        // Determine recommendation
        recommendation = getSignalRecommendation(profitsDollars, winRate, drawdownDollars, trades)
        
        // Update table cells
        table.cell(strategyAnalytics, 0, row, signalName, text_color=color.white)
        table.cell(strategyAnalytics, 1, row, str.tostring(trades), text_color=color.white)
        table.cell(strategyAnalytics, 2, row, str.tostring(winRate, '#.1') + '%', 
                  text_color=winRate >= 60 ? color.lime : winRate >= 45 ? color.yellow : color.red)
        table.cell(strategyAnalytics, 3, row, '$' + str.tostring(profitsDollars, '#'), 
                  text_color=profitsDollars > 0 ? color.lime : color.red)
        table.cell(strategyAnalytics, 4, row, '$' + str.tostring(drawdownDollars, '#'), 
                  text_color=color.red)
        table.cell(strategyAnalytics, 5, row, recommendation, 
                  text_color=getRecommendationColor(recommendation))

// Signal recommendation logic
getSignalRecommendation(pnl, winRate, maxDD, trades) =>
    if trades < 5
        'WAIT ⏳'
    else if pnl > 500 and winRate >= 60 and maxDD < 1000
        'KEEP ✅'
    else if pnl < -200 or winRate < 35 or maxDD > 2000
        'CUT ❌'
    else
        'TEST ⚠️'
Advanced Metrics Dashboard

// Additional performance metrics
calculateAdvancedMetrics(signalIndex) =>
    trades = array.get(signal_tradeCounts, signalIndex)
    wins = array.get(signal_winCounts, signalIndex)
    profits = array.get(signal_profits, signalIndex)
    
    if trades > 0
        winRate = wins / trades
        avgWin = profits > 0 ? profits / wins : 0
        avgLoss = profits < 0 ? math.abs(profits) / (trades - wins) : 0
        profitFactor = avgLoss > 0 ? avgWin / avgLoss : 0
        
        [winRate, avgWin, avgLoss, profitFactor]
    else
        [0.0, 0.0, 0.0, 0.0]

// Sharpe ratio calculation for signal
calculateSignalSharpe(signalIndex, periods) =>
    returns = array.new<float>()
    
    // Collect return data for signal
    for i = 0 to periods - 1
        if i < bar_index
            // Calculate period return for this signal
            period_return = getPeriodReturn(signalIndex, i)
            array.push(returns, period_return)
    
    // Calculate Sharpe ratio
    if array.size(returns) > 1
        mean_return = array.avg(returns)
        std_return = array.stdev(returns)
        sharpe = std_return > 0 ? mean_return / std_return : 0
        sharpe
    else
        0.0
🔧 Advanced Debugging & Validation {#debugging}
Debug System for Signal Tracking
Based on the luxalgo.com debugging guide:


// Comprehensive debug system
debugSignalTracking = input.bool(false, '🔍 Debug Signal Tracking', group='🛠️ Debug System')

if debugSignalTracking
    // Create debug table
    var table debugTable = table.new(position.top_left, 4, 12, 
                                    bgcolor=color.new(color.black, 20), 
                                    border_width=1)
    
    // Real-time signal status
    if barstate.isconfirmed
        for i = 0 to 9
            signalActive = getSignalStatus(i)  // Your signal detection logic
            currentTrades = array.get(signal_tradeCounts, i)
            currentPnL = array.get(signal_profits, i) * futuresMultiplier
            
            table.cell(debugTable, 0, i + 1, 'Signal ' + str.tostring(i + 1), 
                      text_color=color.white, text_size=size.small)
            table.cell(debugTable, 1, i + 1, signalActive ? '🟢 ACTIVE' : '⚫ OFF', 
                      text_color=signalActive ? color.lime : color.gray, text_size=size.small)
            table.cell(debugTable, 2, i + 1, str.tostring(currentTrades), 
                      text_color=color.white, text_size=size.small)
            table.cell(debugTable, 3, i + 1, '$' + str.tostring(currentPnL, '#'), 
                      text_color=currentPnL > 0 ? color.lime : color.red, text_size=size.small)

// Validation against TradingView backtest results
validateResults() =>
    calculated_pnl = 0.0
    for i = 0 to 9
        signal_pnl = array.get(signal_profits, i) * futuresMultiplier
        calculated_pnl += signal_pnl
    
    tv_net_profit = strategy.netprofit
    difference = math.abs(calculated_pnl - tv_net_profit)
    
    if difference > 50  // Alert if difference > $50
        debugMessage("ERROR", "P&L Mismatch: Calculated $" + str.tostring(calculated_pnl, '#') + 
                    " vs TradingView $" + str.tostring(tv_net_profit, '#'), color.red, color.white, 0.1)

// Alert when calculations don't match
if debugSignalTracking and barstate.isconfirmed
    validateResults()
Signal Flow Validation

// Track signal flow for debugging
if debugSignalTracking and (longEntrySignal or shortEntrySignal)
    signal_names = ""
    active_count = 0
    
    for i = 0 to 9
        if isSignalActive(i)  // Your signal detection logic
            signal_names += (active_count > 0 ? ", " : "") + getSignalName(i)
            active_count += 1
    
    debugMessage("INFO", "Entry Triggered: " + signal_names + 
                " (" + str.tostring(active_count) + " signals)", 
                color.blue, color.white, 0.05)
⚠️ Common Issues & Solutions {#troubleshooting}
Issue 1: Inaccurate P&L Calculations
Problem: Calculated P&L doesn't match TradingView's backtest results.

Solutions:


// 1. Ensure consistent position sizing
if longEntrySignal
    strategy.entry("Long", strategy.long, qty=positionQty, comment=dynamicLongText, 
                  alert_message=longEntryMsg)
    
    // Track entry details immediately
    current_trade_entry := close
    positionQty := strategy.position_size  // Update actual position size

// 2. Use strategy.closedtrades for accurate closed trade P&L
if strategy.closedtrades > strategy.closedtrades[1]
    last_trade_pnl = strategy.closedtrades.profit(strategy.closedtrades - 1)
    // Use this for calculations instead of manual calculations

// 3. Account for partial fills and position changes
if strategy.position_size != strategy.position_size[1]
    // Position changed - update tracking
    updatePositionTracking()
Issue 2: Signal Attribution in Pyramiding
Problem: Hard to track which signal contributed to which profit when pyramiding.

Solution:


// Individual entry tracking with signal ID
strategy.entry("Long_" + str.tostring(signal_id), strategy.long, qty=1, 
              comment=signal_name + "_Entry")

// Track each entry separately
var map<string, float> entry_prices = map.new<string, float>()
var map<string, int> entry_signals = map.new<string, int>()

// On entry
entry_id = "Long_" + str.tostring(signal_id) + "_" + str.tostring(bar_index)
map.put(entry_prices, entry_id, close)
map.put(entry_signals, entry_id, signal_id)

// On exit, calculate P&L for each entry
for entry_key in map.keys(entry_prices)
    entry_price = map.get(entry_prices, entry_key)
    signal_id = map.get(entry_signals, entry_key)
    entry_pnl = (close - entry_price) * futuresMultiplier
    updateSignalPnL(signal_id, entry_pnl)
Issue 3: Real-Time Updates Not Working
Problem: Table doesn't update as strategy parameters change.

Solution:


// Force table recreation on parameter changes
param_hash = str.tostring(signal1Enable) + str.tostring(signal2Enable) // ... all params
if param_hash != param_hash[1]
    // Parameters changed - clear and recreate table
    resetPerformanceTracking()
    force_table_update := true

// Use barstate.isconfirmed for consistent updates
if barstate.isconfirmed and (force_table_update or bar_index % 10 == 0)
    updateAllTables()
    force_table_update := false
Issue 4: Memory Limitations
Problem: Arrays grow too large, causing performance issues.

Solution:


// Limit array sizes and use circular buffers
MAX_HISTORY = 1000

// Function to maintain array size
maintainArraySize(arr) =>
    if array.size(arr) > MAX_HISTORY
        array.shift(arr)  // Remove oldest element

// Periodic cleanup
if bar_index % 100 == 0
    maintainArraySize(signal_profits)
    maintainArraySize(signal_drawdowns)
    // ... clean other arrays
🚀 Optimization Techniques {#optimization}
Performance Optimization

// 1. Minimize calculations on every bar
var bool calculations_needed = false
calculations_needed := strategy.position_size != strategy.position_size[1] or 
                      strategy.closedtrades != strategy.closedtrades[1]

if calculations_needed
    // Only do heavy calculations when position changes
    updateAllMetrics()

// 2. Use efficient data structures
// Instead of multiple arrays, use a single UDT (User Defined Type)
type SignalData
    int trades = 0
    int wins = 0
    float profits = 0.0
    float max_drawdown = 0.0
    bool active = false

var array<SignalData> signals = array.new<SignalData>(10)

// 3. Batch table updates
var int update_counter = 0
update_counter += 1

if update_counter % 5 == 0 or barstate.isconfirmed
    // Update table every 5 bars or on bar close
    updatePerformanceTable()
Advanced Signal Analysis

// Signal correlation analysis
calculateSignalCorrelation(signal1_idx, signal2_idx, lookback) =>
    correlation = 0.0
    signal1_wins = 0
    signal2_wins = 0
    both_wins = 0
    
    for i = 0 to lookback - 1
        if i < bar_index
            s1_win = getHistoricalWin(signal1_idx, i)
            s2_win = getHistoricalWin(signal2_idx, i)
            
            if s1_win
                signal1_wins += 1
            if s2_win
                signal2_wins += 1
            if s1_win and s2_win
                both_wins += 1
    
    if signal1_wins > 0 and signal2_wins > 0
        correlation := both_wins / math.min(signal1_wins, signal2_wins)
    
    correlation

// Market condition analysis
analyzeMarketCondition() =>
    volatility = ta.atr(14) / ta.sma(ta.atr(14), 50)
    trend_strength = ta.adx(14)
    
    condition = volatility > 1.5 ? "HIGH_VOL" : 
                volatility < 0.7 ? "LOW_VOL" : 
                trend_strength > 25 ? "TRENDING" : "RANGING"
    
    condition

// Adaptive signal weights based on market conditions
getSignalWeight(signalIndex, marketCondition) =>
    weight = 1.0
    
    // Adjust weights based on historical performance in different conditions
    switch marketCondition
        "HIGH_VOL" => weight := getHighVolWeight(signalIndex)
        "LOW_VOL" => weight := getLowVolWeight(signalIndex)
        "TRENDING" => weight := getTrendWeight(signalIndex)
        "RANGING" => weight := getRangeWeight(signalIndex)
    
    weight
Automated Strategy Optimization

// Auto-optimization based on performance
autoOptimizeSignals() =>
    for i = 0 to 9
        signal_performance = get

5 citations

Favicon for https://pastecode.io/s/i8bbr2nz

Favicon for https://www.luxalgo.com/blog/debugging-pine-script-indicators-guide/

Favicon for https://medium.com/@betashorts1998/how-to-debug-pine-script-code-like-a-pro-save-hours-of-frustration-d7c5a5860c36

Favicon for https://pastecode.io/s/qax36vdc

Favicon for https://www.tradingview.com/script/5QSO13Ib-Flare/
Untitled (i8bbr2nz) - PasteCode.io
1 : 0 _m7 := _i == 9 ? 36 : _i == 8 ? 8 : _i == 7 ? 1 : 0
Favicon for https://pastecode.io/s/i8bbr2nz
pastecode.io
Debugging Pine Script Indicators: Guide
| Mismatched Brackets | `plot(close` | Add the missing parenthesis: `plot(close)` | | Incorrect Indentation | Random spaces in code | Align code using multiples of 4 spaces | | Reserved Keyword Conflicts | `strategy = 1` | Avoid using reserved words as variable names | This usually points to spacing or formatting issues. In this case, ensure that `plot` statements start at column 0 [\[2\]](https://www.tradingview.com/pine-script-docs/error-messages/?ref=luxalgo.com) [\[4\]](https://forexbee.co/pine-script-variables-and-identifier/?ref=luxalgo.com).
Favicon for https://www.luxalgo.com/blog/debugging-pine-script-indicators-guide/
www.luxalgo.com
How to Debug Pine Script Code Like a Pro (Save Hours of Frustration)
** ## Debug with `table.new()` for Cleaner Outputs Labels can clutter the chart. Instead, store debug values in a **table** for a clean, organized output: ``` var table debugTable = table.new(position=position.top_right, columns=1, rows=1) table.cell(debugTable, 0, 0, str.tostring(debugValue), text_color=color.white) ``` Now, instead of placing **hundreds of labels**, you can track **live variable changes in one place**. ## Debug with `alertcondition()` for Hidden Errors Sometimes, an issue won’t be visible on the chart. To track values hidden within conditions or loops, send them to TradingView’s **alert log**: ``` alertcondition(true, title="Debug Alert", message="Value: " + str.tostring(debugValue)) ``` This is useful when **checking if certain conditions actually trigger or debugging alert failures**. ## Step 2: Catching Logical Errors That Break Your Strategy If your script **isn’t triggering entries or is giving unexpected values**, follow this debugging approach: **Step 1: Visually confirm conditions are met** Use `plotshape()` to **mark bars where conditions should trigger**: ``` plotshape(debugValue > 0, location=location.top, color=color.green, style=shape.triangleup) ``` **Step 2: Check execution order** Unlike traditional programming, Pine Script executes **from left to right, one bar at a time.
Favicon for https://medium.com/@betashorts1998/how-to-debug-pine-script-code-like-a-pro-save-hours-of-frustration-d7c5a5860c36
medium.com
Untitled (qax36vdc) - PasteCode.io
color.new(color.red, 35) : (osc_bias < 0) and (osc_bias > osc_smooth) ? color.new(color.red, 75) : array.set(ema_val, 0, (array.get(val, 0) - array.get(ema_val, 0)) * (2 / (n + 1)) + array.get(ema_val, 0)) rng_size = scale == 'Pips' ?
Favicon for https://pastecode.io/s/qax36vdc
pastecode.io
Flare — Indicator by fikira
Read more in the [Terms of Use](https://www.tradingview.com/policies/#disclaimer-regarding-investment-decisions-and-trading).
Favicon for https://www.tradingview.com/script/5QSO13Ib-Flare/
www.tradingview.com
Previous slideNext slide





24 minutes ago
Favicon for x-ai
Grok 4
Below is a comprehensive Instruction Manual for Implementing Individual Signal Backtesting in TradingView Pine Script Indicators. This manual is tailored specifically to your described setup: a futures trading strategy (e.g., NQ with a 20x multiplier, ES with 50x, etc.) using up to 10 individual buy/sell indicators, pyramiding (adding contracts per signal fire), and dynamic exits (e.g., Smart Profit Locker, Trend Change Exit, Hybrid Exit). It draws from TradingView's official Pine Script documentation, community resources (e.g., TradingView forums, Pine Script reference manual), and the web search results provided (cited inline where relevant).

The manual is structured as a step-by-step guide, with sections on setup, implementation, calculations, challenges (based on your script's issues like combined signal functions), tips/tricks/hints, and debugging. I've incorporated insights from the search results, such as using table.new() for clean outputs (medium.com) and plotshape() for visual confirmation (pinescripters.net). I've also addressed accuracy for futures (e.g., point-value multipliers for PNL/drawdown) and real-time updates as you adjust variables (e.g., enabling/disabling signals or exits).

Important Notes Before Starting:

Pine Script v5/v6 (your script uses v6) allows strategy.* functions in indicators for backtesting, but indicators don't execute real trades—use them for simulation. For live trading, convert to a full strategy script or integrate with external tools like TradersPost (as in your script).
Your script's challenge: Signals are processed in a combined way (e.g., primaryLongSig = sig1Long or sig2Long or ...), making individual tracking hard. We'll solve this by tagging entries with signal IDs and apportioning metrics per signal.
Accuracy Tip: Set calc_on_order_fills = true and process_orders_on_close = true (as in your script) to ensure realistic fill prices. For futures, always multiply points by the contract multiplier (e.g., NQ: 1 point = $20 profit/loss).
Testing: Backtest on historical data with barstate.isrealtime = false to simulate. Use replay mode in TradingView for validation.
Limitations: Tables update on bar close (not intrabar), but we can make them "real-time" by recalculating per bar. Individual signal backtesting requires custom arrays (no built-in per-signal strategy tester).
Instruction Manual: Building a Real-Time Individual Signal Backtester in Pine Script for Futures Trading
Section 1: Introduction to Backtesting in Pine Script Indicators
Pine Script indicators can simulate strategies using strategy.* functions (e.g., strategy.entry(), strategy.exit()) without being full strategies. This is ideal for your setup, where you want an indicator that backtests 10 signals individually while allowing real-time tweaks (e.g., enabling/disabling signals, changing exits).

Key Concepts
Backtesting Engine: Uses historical bars to simulate entries/exits. Metrics like PNL are calculated via strategy.netprofit, strategy.openprofit, etc.
Pyramiding in Futures: Each signal adds a contract (e.g., up to 10 for 10 signals). Track position size via strategy.position_size. For accuracy, use default_qty_type = strategy.fixed and multiply by contract value (e.g., NQ: 20x points).
Individual Signal Tracking: Since your signals are combined (e.g., one primaryLongSig), we'll use arrays to tag which signal(s) contributed to each entry. This allows apportioning PNL/drawdown per signal.
Table/Panel Display: Use table.new() to create a dynamic panel that updates per bar, showing per-signal metrics (win/loss, PNL, drawdown). It can react to changes (e.g., toggling a signal recalculates instantly on the next bar).
Futures-Specific Accuracy:
PNL = (Exit Price - Entry Price) * Contracts * Multiplier (e.g., 20 for NQ).
Drawdown = Max unrealized loss during trade, multiplied by multiplier.
Handle longs/shorts separately for win/loss (e.g., track short drawdown as positive values).
Resources:

TradingView Pine Script Manual: tradingview.com (core strategy functions).
For debugging mismatches: luxalgo.com emphasizes checking indentation and reserved keywords.
Section 2: Setting Up the Backtester in Your Indicator
Start by expanding your script's global variables (you already have some like signal_tradeCounts). Add these at the top:


// Backtesting Arrays (one per signal, indexed 0-9)
var array<int> signal_tradeCounts = array.new<int>(10, 0)  // Total trades per signal
var array<int> signal_winCounts = array.new<int>(10, 0)    // Wins per signal
var array<float> signal_netProfits = array.new<float>(10, 0.0)  // Cumulative PNL (points)
var array<float> signal_maxDrawdowns = array.new<float>(10, 0.0)  // Max DD per signal (points)
var array<float> signal_largestWins = array.new<float>(10, 0.0)   // Largest win per signal
var array<float> signal_largestLosses = array.new<float>(10, 0.0) // Largest loss per signal
var array<float> signal_avgTradeDuration = array.new<float>(10, 0.0)  // Avg bars per trade
var array<int> signal_activeTrades = array.new<int>(10, 0)  // For open trade tracking

// Per-Trade Tracking (reset on trade close)
var array<bool> activeSignalsInTrade = array.new<bool>(10, false)  // Which signals contributed to current position
var float tradeEntryPrice = na
var int tradeEntryBar = na
var float tradePeakProfit = na  // For DD calculation
var float tradeTroughProfit = na
Step-by-Step Setup
Tag Signals on Entry: In your entry logic (after longEntrySignal or shortEntrySignal), record which signals fired. This solves the "combined function" issue.


// Inside your entry logic block
if longEntrySignal and strategy.position_size == 0  // New position (or pyramid add)
    tradeEntryPrice := close
    tradeEntryBar := bar_index
    tradePeakProfit := 0.0
    tradeTroughProfit := 0.0
    array.clear(activeSignalsInTrade)  // Reset
    // Tag contributing signals (repeat for all 10)
    if sig1Long then array.set(activeSignalsInTrade, 0, true); array.set(signal_activeTrades, 0, array.get(signal_activeTrades, 0) + 1)
    if sig2Long then array.set(activeSignalsInTrade, 1, true); array.set(signal_activeTrades, 1, array.get(signal_activeTrades, 1) + 1)
    // ... (for sig3-10)
    strategy.entry("Long", strategy.long, qty=longSignalCount * positionQty)  // Pyramid based on count
// Similar for shortEntrySignal
Track During Open Trades: Update peaks/troughs per bar for drawdown.


if strategy.position_size != 0
    float currentProfit = strategy.position_size > 0 ? (close - tradeEntryPrice) : (tradeEntryPrice - close)  // Points
    tradePeakProfit := math.max(tradePeakProfit, currentProfit)
    tradeTroughProfit := math.min(tradeTroughProfit, currentProfit)  // Negative for losses
Update Metrics on Exit: When a trade closes (detect via strategy.closedtrades > strategy.closedtrades[1]), apportion metrics to contributing signals.


if strategy.closedtrades > strategy.closedtrades[1]
    float tradePnlPoints = strategy.position_size[1] > 0 ? (close - tradeEntryPrice) : (tradeEntryPrice - close)  // Points per contract
    float tradeDrawdown = (tradePeakProfit - tradeTroughProfit)  // Points
    bool isWin = tradePnlPoints > 0
    int tradeDuration = bar_index - tradeEntryBar
    int numContributingSignals = array.sum(activeSignalsInTrade)  // For apportioning

    for i = 0 to 9
        if array.get(activeSignalsInTrade, i)
            // Apportion per signal (divide by contributors for fairness, or weight by 1 if simple)
            float apportionedPnl = tradePnlPoints / numContributingSignals
            float apportionedDd = tradeDrawdown / numContributingSignals

            // Update arrays
            array.set(signal_tradeCounts, i, array.get(signal_tradeCounts, i) + 1)
            if isWin then array.set(signal_winCounts, i, array.get(signal_winCounts, i) + 1)
            array.set(signal_netProfits, i, array.get(signal_netProfits, i) + apportionedPnl)
            array.set(signal_maxDrawdowns, i, math.max(array.get(signal_maxDrawdowns, i), apportionedDd))
            if isWin then array.set(signal_largestWins, i, math.max(array.get(signal_largestWins, i), apportionedPnl))
            else array.set(signal_largestLosses, i, math.min(array.get(signal_largestLosses, i), apportionedPnl))  // Negative
            // Avg duration (running average)
            float currentAvg = array.get(signal_avgTradeDuration, i)
            int trades = array.get(signal_tradeCounts, i)
            array.set(signal_avgTradeDuration, i, (currentAvg * (trades - 1) + tradeDuration) / trades)
    
    // Reset trackers
    tradeEntryPrice := na
    array.clear(activeSignalsInTrade)
Tip: For pyramiding accuracy, scale PNL by the number of contracts added by that signal (e.g., if Signal 1 added 1 contract, apportion full PNL to it if solo).

Section 3: Creating the Backtest Table/Panel
Use table.new() to display per-signal metrics. Update it every bar for "real-time" feel (it recalculates when you change inputs, as arrays persist).

Implementation

// In your script, after metrics update
if showBacktestTable
    var table backtestTable = table.new(position.bottom_left, columns=6, rows=11, bgcolor=color.black)
    // Headers
    table.cell(backtestTable, 0, 0, "Signal", text_color=color.white)
    table.cell(backtestTable, 1, 0, "Trades", text_color=color.white)
    table.cell(backtestTable, 2, 0, "Win%", text_color=color.white)
    table.cell(backtestTable, 3, 0, "PNL ($)", text_color=color.white)  // Futures $
    table.cell(backtestTable, 4, 0, "Max DD ($)", text_color=color.white)
    table.cell(backtestTable, 5, 0, "Avg Dur (bars)", text_color=color.white)

    // Per-Signal Rows (loop for 10 signals)
    signalNames = array.from(signal1Name, signal2Name, ..., signal10Name)  // Array of names
    for i = 0 to 9
        if array.get(signal_enables, i)  // Your enables array
            int trades = array.get(signal_tradeCounts, i)
            float winPct = trades > 0 ? array.get(signal_winCounts, i) / trades * 100 : 0
            float pnlPoints = array.get(signal_netProfits, i)
            float ddPoints = array.get(signal_maxDrawdowns, i)
            float pnlDollars = pnlPoints * positionQty * futuresMultiplier  // Futures calc
            float ddDollars = ddPoints * positionQty * futuresMultiplier
            float avgDur = array.get(signal_avgTradeDuration, i)

            table.cell(backtestTable, 0, i+1, array.get(signalNames, i))
            table.cell(backtestTable, 1, i+1, str.tostring(trades))
            table.cell(backtestTable, 2, i+1, str.tostring(winPct, "#.#") + "%", text_color=winPct > 60 ? color.green : color.red)
            table.cell(backtestTable, 3, i+1, "$" + str.tostring(pnlDollars, "#.#"), text_color=pnlDollars > 0 ? color.green : color.red)
            table.cell(backtestTable, 4, i+1, "$" + str.tostring(ddDollars, "#.#"), text_color=color.orange)
            table.cell(backtestTable, 5, i+1, str.tostring(avgDur, "#"))
Trick: To make it update "in real-time" as you toggle signals, use barstate.islast to force recalculation. Add color-coding for impact (e.g., green if PNL > 0 and low DD).

Section 4: Calculating Metrics Accurately for Futures
PNL: (Exit - Entry) * Contracts * Multiplier. Apportion to signals based on contribution (e.g., if 3 signals pyramided, split 1/3 each).
Win/Loss Ratio: Separate for longs/shorts. Win = PNL > 0. Track shorts as wins if positive (price dropped).
Drawdown: Max unrealized loss = (Peak - Trough) * Multiplier. Overall DD = max historical equity drop.
Accuracy Tip: Use strategy.equity for global metrics, but custom arrays for per-signal. Validate with TradingView's built-in strategy tester by toggling one signal at a time (matches your current manual process).
Hint: For shorts, invert drawdown calc: DD = max(entry - low) during trade.

Section 5: Challenges and Solutions
Combined Signals: Solution: Tag with arrays (as above). Don't rely on one function—loop through signals individually.
Pyramiding Accuracy: Track per-signal contract adds. If 6 signals fire, position = 6 contracts; on exit, split PNL by signal weight.
Real-Time Updates: Tables update on bar close, but changing inputs (e.g., toggle signal) forces a script reload, updating arrays.
Inaccuracy Issues: Common from search: Mismatched brackets or indentation (luxalgo.com). Use calc_on_every_tick=true for intrabar precision.
Futures Tailoring: Always apply multiplier post-calc. Test on NQ chart with historical gaps to ensure BB filter accuracy.
Section 6: Tips, Tricks, Hints, and Debugging
Tip: Use plotshape() to mark per-signal entries visually (pinescripters.net).
Trick: For hidden errors, use alertcondition() to log values (medium.com).
Hint: Reduce code bloat—your script is now ~1500 lines; move arrays to functions.
Debugging: Add labels for mismatches (luxalgo.com). Use tables for live variable tracking (medium.com).
This should get your backtester working accurately. If you share the full script, I can refine code snippets!