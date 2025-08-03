EZ Algo Trader - Trend-Riding System Documentation
Overview
The Trend-Riding System is a revolutionary exit management overlay that overrides normal profit-taking mechanisms when strong trending conditions are detected. Instead of using traditional price-based stops, it uses signal-driven exits - waiting for opposite signals from the primary indicators to close positions.
Core Philosophy
"Let Winners Run in Obvious Trends" - The system identifies when multiple directional bias filters agree strongly, then switches from aggressive profit-taking to trend-following mode, allowing profitable trades to capture extended moves.

System Architecture
1. Activation Requirements
Filter Consensus Requirements

Activation Mode Options:

All Filters: All enabled directional bias filters must agree
Majority Filters: More than 50% of enabled filters must agree
Custom Count: User-defined minimum number of agreeing filters



Directional Bias Filters (6 Total)

RBW Filter (Relative Bandwidth): Volatility-based directional bias
Hull Suite: Hull Moving Average trend direction
SuperTrend: ATR-based trend following
Quadrant (Nadaraya-Watson): Kernel regression trend
MTF ZigZag: Multi-timeframe pivot analysis
Reversal Probability: Statistical reversal likelihood (optional)

Trend Strength Validation

Minimum Trend Bars: Consecutive bars all filters must agree (default: 3)
Reversal Probability Check: Optional requirement for low reversal probability
Maximum Hold Duration: Safety limit (default: 50 bars)

2. Signal-Driven Exit System
Exit Signal Sources (Configurable)

Signal 1 (LuxAlgo): Primary exit signal source
Signal 2 (UTBot): Secondary exit signal
Signal 3 (VIDYA): Additional exit confirmation
Signal 4 (KyleAlgo): Extra exit signal
Signal 5 (Wonder): Final exit option

Exit Logic

Long Positions: Wait for SHORT signals from selected indicators
Short Positions: Wait for LONG signals from selected indicators
Minimum Exit Signals: User-configurable confluence requirement (default: 1)

3. Safety Net Systems
Safety Net 1: Trend Break Detection
pinetrendConsensusBroken = not strongTrendDetected

Exits immediately if filter consensus breaks
Prevents being stuck in sideways markets

Safety Net 2: Maximum Hold Duration
pinemaxHoldSafetyTriggered = bar_index - trendRidingStartBar >= trendMaxHold

Forces exit after maximum bars (default: 50)
Prevents overnight risk accumulation

Safety Net 3: Catastrophic Stop Loss
pinecatastrophicStopDistance = normalStopDistance * catastrophicStopMultiplier

Uses 3x normal stop distance as absolute safety
Final protection against major adverse moves


Implementation Details
Variable Declarations
pine// Core state variables
var int trendRidingBarsActive = 0
var bool inTrendRidingMode = false  
var int trendRidingStartBar = 0

// Hybrid exit mode variables
var bool inHybridExitMode = false
var int hybridModeStartBar = 0
Activation Logic
pine// Calculate trend strength
trendStrengthScore = math.max(bullishVotes, bearishVotes)

// Determine activation threshold
trendActivationThreshold = trendActivationMode == 'All Filters' ? enabledFilters : 
  trendActivationMode == 'Majority Filters' ? math.ceil(enabledFilters / 2.0) : trendMinFilters

// Check activation conditions
strongTrendDetected = enabledFilters > 0 and 
  (bullishVotes >= trendActivationThreshold or bearishVotes >= trendActivationThreshold) and
  reversalProbOK

// Track consecutive trend bars
if strongTrendDetected
    trendRidingBarsActive := trendRidingBarsActive + 1
else
    trendRidingBarsActive := 0

// Activate trend-riding mode
if trendRidingEnable and not inTrendRidingMode and 
   trendRidingBarsActive >= trendMinBars and strategy.position_size != 0
    inTrendRidingMode := true
    trendRidingStartBar := bar_index
Exit Interception Logic
pine// Block standard exits during trend-riding
standardExitBlocked := inTrendRidingMode

// Smart Profit Locker modification
if smartProfitEnable and strategy.position_size != 0 and 
   not (inTrendRidingMode and not inHybridExitMode)
    // Normal Smart Profit Locker logic
Signal-Based Exit Detection
pine// Count opposite signals for exit
oppositeSignalsCount = 0

// Check each enabled signal source
if useExitSignal1 and signal1Enable
    if strategy.position_size > 0 and sig1Short
        oppositeSignalsCount := oppositeSignalsCount + 1
    if strategy.position_size < 0 and sig1Long  
        oppositeSignalsCount := oppositeSignalsCount + 1

// Trigger exit when threshold met
signalBasedExitTriggered = oppositeSignalsCount >= minOppositeSignals

Hybrid Exit Mode Integration
Purpose
Reduces profit giveback when exit indicators fire during trend-riding by switching to tighter Smart Profit Locker settings.
Activation

Triggered by exit indicator voting system
Uses binary signal counting + reversal probability
Threshold: 2+ exit indicators (configurable)

Behavior Changes
pineif inHybridExitMode
    // HYBRID MODE: Use TIGHT trailing stops
    smartDistance := smartDistance * 1.0  // 1x ATR (tight)
    smartOffset := smartDistance * 0.50   // 50% offset (aggressive)
else
    // NORMAL MODE: Use regular settings
    smartOffset := smartDistance * math.max(smartProfitOffset, 0.01)

Configuration Parameters
Activation Settings

Trend-Riding Enable: trendRidingEnable (default: false)
Activation Mode: trendActivationMode (All Filters/Majority/Custom)
Min Filters: trendMinFilters (default: 4)
Min Trend Bars: trendMinBars (default: 3)
Max Hold Bars: trendMaxHold (default: 50)

Exit Signal Selection

Use Exit Signal 1-5: Individual toggles for each signal source
Min Exit Signals: minOppositeSignals (default: 1)
Trend Break Safety: enableTrendBreakSafety (default: true)

Safety Settings

Catastrophic Stop: enableCatastrophicStop (default: true)
Cat Stop Multiplier: catastrophicStopMultiplier (default: 3.0)


Expected Benefits
Performance Improvements

Extended Winners: Captures larger moves in strong trends
Reduced Profit Giveback: Hybrid mode protects gains
High Win Rate Preservation: Only activates in obvious trends
Risk Management: Multiple safety nets prevent disasters

Visual Feedback

'T' Character: Shows when trend-riding is active
Debug Labels: Detailed activation/deactivation logging
Purple Indicators: Trend-riding mode status markers


Key Success Factors

Filter Quality: Directional bias filters must be properly configured
Signal Reliability: Exit signals must be accurate reversal indicators
Parameter Tuning: Activation thresholds balanced for frequency vs. accuracy
Safety Compliance: All safety nets must be enabled and tested

Troubleshooting Notes

System requires at least one directional bias filter enabled
Activation only occurs when position is already open
Debug logging essential for validation and optimization
Regular parameter adjustment based on market conditions recommended