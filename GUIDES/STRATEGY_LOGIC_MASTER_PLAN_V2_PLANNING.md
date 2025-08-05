# STRATEGY LOGIC MASTER PLAN V2 - PLANNING DOCUMENT
## EZ ALGO HEDGE FUND - CORRECTED BOOLEAN LOGIC & FILTER CHAIN REDESIGN

> **PURPOSE**: This is the planning document for the corrected strategy logic based on user feedback. This shows what the logic WILL BE after we fix the major issues identified in V1.

---

## 🚨 **CRITICAL ISSUES IDENTIFIED IN V1 THAT MUST BE FIXED**

### **ISSUE 1: Step Channel Misuse**
- **WRONG**: Step Channel blocking all trades based on price position
- **CORRECT**: Step Channel identifies Trend vs Scalp mode (controls exit type only)
- **WRONG**: Always applies directional bias
- **CORRECT**: Optional directional bias setting (off by default)

### **ISSUE 2: CVD Directional Bias Error**
- **WRONG**: CVD has directional bias (longDirectionalBias, shortDirectionalBias)
- **CORRECT**: CVD only identifies Trend vs Scalp mode (no directional bias ever)
- **WRONG**: CVD blocks trades based on direction
- **CORRECT**: CVD works independently, only controls exit strategy

### **ISSUE 3: Directional Bias Overcomplicated**
- **WRONG**: "Any" confluence option exists
- **CORRECT**: Only "All" or "Majority" options
- **MISSING**: Debug window to show indicator connections and current signals

### **ISSUE 4: CRITICAL - Opposite Signals Blocked for Exits**
- **WRONG**: Directional bias completely blocks opposite signals
- **CRITICAL PROBLEM**: Removes opposite signal as exit mechanism
- **CORRECT**: Opposite signals should pass through as EXIT SIGNALS when in position
- **MISSING**: Option to allow opposite signals as exits (should be ON by default)
- **IMPACT**: Strategy loses a key exit layer in multi-exit system
- **CLARIFICATION**: Directional bias converts one direction from entry+reversal to exit-only

### **ISSUE 5: CRITICAL - Mode Transition Problem (COMPLETELY MISSING LOGIC)**
- **SCENARIO 1**: Enter trade in Scout Mode → Mode changes to Trend → What happens to exit strategy?
- **SCENARIO 2**: Enter trade in Trend Mode → Mode changes to Scout → What happens to exit strategy?
- **SCENARIO 3**: Scout → Trend → Scout flip-flopping → How are exits handled?
- **CURRENT STATE**: NO LOGIC EXISTS for handling mode transitions during active trades
- **CRITICAL PROBLEM**: Strategy doesn't know which exit method to use when mode changes
- **MISSING**: Mode transition rules for existing positions
---

## 🎯 **CORRECTED LONG ENTRY LOGIC FLOW**

### **STEP 1: SIGNAL GENERATION (OR GATE) - UNCHANGED**
```
BOOLEAN VARIABLES:
├── sig1Long, sig2Long, sig3Long, ..., sig10Long (individual signals)
├── sigCountLong (count of active signals)
└── primaryLongSig (any signal active)

LOGICAL CONDITIONS:
├── sig1Long = signal1Enable AND (signal1LongSrc ≠ close) AND ta.change(signal1LongSrc) > 0
├── sig2Long = signal2Enable AND (signal2LongSrc ≠ close) AND ta.change(signal2LongSrc) > 0
├── ... (repeat for sig3Long through sig10Long)
├── sigCountLong = (sig1Long ? 1 : 0) + (sig2Long ? 1 : 0) + ... + (sig10Long ? 1 : 0)
└── primaryLongSig = sig1Long OR sig2Long OR sig3Long OR ... OR sig10Long

DECISION GATE:
IF sigCountLong > 0 THEN proceed to STEP 2
ELSE block trade (no signals active)

STATUS: ✅ CORRECT - No changes needed
```

### **STEP 2: STEP CHANNEL TREND/SCALP MODE DETECTION - MAJOR REDESIGN**
```
BOOLEAN VARIABLES:
├── stepChannelEnable (user setting - DEFAULT: TRUE)
├── stepChannelDirectionalBias (user setting - DEFAULT: FALSE)
├── stepChannelState ("Range", "Momentum Up", "Momentum Down")
├── stepChannelUpper (calculated boundary)
├── stepChannelLower (calculated boundary)
├── stepChannelTrendMode (trend vs scalp decision)
└── stepChannelLongAllowed (directional bias decision - OPTIONAL)

LOGICAL CONDITIONS:
├── stepChannelTrendMode = NOT stepChannelEnable OR (stepChannelState ≠ "Range")
└── stepChannelLongAllowed = NOT stepChannelDirectionalBias OR (close > stepChannelLower)

PRIMARY PURPOSE - MODE DETECTION:
- Green candles (Momentum Up) → Trend Mode (hold for bigger profits)
- Red candles (Momentum Down) → Trend Mode (hold for bigger profits)  
- Yellow candles (Range) → Scalp Mode (quick exits for 70-80% win rate)

SECONDARY PURPOSE - DIRECTIONAL BIAS (OPTIONAL):
- IF stepChannelDirectionalBias = TRUE:
  * Above stepChannelLower → Allow Longs
  * Below stepChannelUpper → Allow Shorts
- IF stepChannelDirectionalBias = FALSE:
  * Always allow both directions

DECISION GATE:
IF stepChannelDirectionalBias = FALSE THEN proceed to STEP 3
ELSE IF stepChannelLongAllowed = TRUE THEN proceed to STEP 3
ELSE block trade (Step Channel Directional Bias says NO)

STATUS: 🔥 MAJOR REDESIGN REQUIRED
```

### **STEP 3: DIRECTIONAL BIAS FILTER - REDESIGNED WITH EXIT LOGIC**
```
BOOLEAN VARIABLES:
├── directionalBiasEnable (user setting)
├── oppositeSignalExitsEnable (user setting - DEFAULT: TRUE)
├── hullEnable, trendStrengthEnable, quadrantEnable, adaptiveSTEnable, volumaticEnable, smoothHAEnable
├── hullBullish, trendStrengthBullish, quadrantBullish, adaptiveSTBullish, volumaticBullish, smoothHABullish
├── enabledFilters (count of enabled bias indicators)
├── bullishVotes (count of bullish indicators)
├── bearishVotes (count of bearish indicators)
├── biasConfluence ("Majority", "All") - REMOVED "Any" option
├── longDirectionalBias (final bias decision for ENTRIES)
├── shortDirectionalBias (final bias decision for ENTRIES)
├── longExitFromShort (opposite signal exit for long positions)
└── shortExitFromLong (opposite signal exit for short positions)

LOGICAL CONDITIONS FOR ENTRIES:
├── enabledFilters = (hullEnable ? 1 : 0) + (trendStrengthEnable ? 1 : 0) + ... + (smoothHAEnable ? 1 : 0)
├── bullishVotes = (hullEnable AND hullBullish ? 1 : 0) + (trendStrengthEnable AND trendStrengthBullish ? 1 : 0) + ...
├── bearishVotes = (hullEnable AND NOT hullBullish ? 1 : 0) + (trendStrengthEnable AND NOT trendStrengthBullish ? 1 : 0) + ...
├── longDirectionalBias = NOT directionalBiasEnable OR enabledFilters = 0 OR
│   (biasConfluence = "Majority" AND bullishVotes > bearishVotes) OR
│   (biasConfluence = "All" AND bearishVotes = 0)
└── shortDirectionalBias = NOT directionalBiasEnable OR enabledFilters = 0 OR
    (biasConfluence = "Majority" AND bearishVotes > bullishVotes) OR
    (biasConfluence = "All" AND bullishVotes = 0)

LOGICAL CONDITIONS FOR EXITS (CRITICAL FIX):
├── longExitFromShort = oppositeSignalExitsEnable AND directionalBiasEnable AND 
│   strategy.position_size > 0 AND primaryShortSig
└── shortExitFromLong = oppositeSignalExitsEnable AND directionalBiasEnable AND 
    strategy.position_size < 0 AND primaryLongSig

ENTRY DECISION GATE:
IF longDirectionalBias = TRUE THEN proceed to STEP 4 (for long entries)
ELSE block long entry (Directional Bias says NO LONGS)

IF shortDirectionalBias = TRUE THEN proceed to STEP 4 (for short entries)  
ELSE block short entry (Directional Bias says NO SHORTS)

EXIT DECISION GATE (NEW CRITICAL LOGIC):
IF longExitFromShort = TRUE THEN execute exit from long position
IF shortExitFromLong = TRUE THEN execute exit from short position

HUMAN REQUIREMENTS:
- Green indicator → Only take longs (block short ENTRIES, allow short EXITS)
- Red indicator → Only take shorts (block long ENTRIES, allow long EXITS)
- Opposite signals MUST pass through as exit signals when in position
- Must work even when loaded mid-trend
- Need debug table showing: indicator name, connected status, current signal (bull/bear)
- oppositeSignalExitsEnable should be ON by default

STATUS: 🔥 MAJOR REDESIGN REQUIRED - ADD EXIT LOGIC
```

### **STEP 4: CVD TREND/SCALP MODE FILTER - MAJOR REDESIGN**
```
BOOLEAN VARIABLES:
├── cvdEnable (user setting)
├── cvdTrendSrc (external indicator connection)
├── cvdTrendMode (trend vs scalp decision - NO DIRECTIONAL BIAS)
└── cvdValue (CVD numerical value for debugging)

LOGICAL CONDITIONS:
├── cvdTrendMode = NOT cvdEnable OR (cvdTrendSrc > 0.5)
└── (cvdTrendSrc comes from external "CVD Strategy Export" indicator)

DECISION GATE:
IF cvdTrendMode = TRUE THEN proceed to STEP 5
ELSE force scalp mode (CVD says insufficient volume delta for trend)

PURPOSE CLARIFICATION:
- CVD NEVER blocks trades based on direction
- CVD ONLY decides: Trend Mode vs Scalp Mode
- High volume delta → Trend Mode (hold for bigger moves)
- Low volume delta → Scalp Mode (quick exits)
- Works independently of all other indicators

STATUS: 🔥 MAJOR REDESIGN REQUIRED - REMOVE ALL DIRECTIONAL BIAS
```

### **STEP 5: FINAL CALCULATION - CORRECTED**
```
BOOLEAN VARIABLES:
├── baseLongWithBias (calculated result)
├── longEntrySignal (final entry trigger)
└── finalLongEntry (execution signal)

LOGICAL CONDITIONS:
├── baseLongWithBias = primaryLongSig AND longDirectionalBias AND 
    (NOT stepChannelDirectionalBias OR stepChannelLongAllowed)
├── longEntrySignal = baseLongWithBias
└── finalLongEntry = longEntrySignal

DECISION GATE:
IF finalLongEntry = TRUE THEN EXECUTE LONG TRADE
ELSE wait for next bar

STATUS: 🔧 MINOR FIXES NEEDED
```

---

## 🔄 **MODE TRANSITION LOGIC (CRITICAL MISSING SECTION)**

### **PROBLEM STATEMENT:**
Current strategy has NO LOGIC for handling mode changes during active trades. This creates undefined behavior when:
- Scout Mode trade → Mode changes to Trend → Which exit strategy applies?
- Trend Mode trade → Mode changes to Scout → Should we exit quickly or hold?
- Rapid Scout ↔ Trend ↔ Scout transitions → Which rules apply?

### **CORRECTED MODE TRANSITION RULES:**

#### **REAL-TIME MODE SWITCHING (DEFAULT BEHAVIOR)**
```
BOOLEAN VARIABLES:
├── currentModeDetection ("Scout" or "Trend") - real-time mode from Step Channel + CVD
├── currentTradingMode ("Scout" or "Trend") - ALWAYS equals currentModeDetection
├── oneWayTrendLock (user setting - DEFAULT: FALSE)
└── manualTrendLocked (boolean - tracks if manually locked)

LOGICAL CONDITIONS (WHEN oneWayTrendLock = FALSE - DEFAULT):
├── currentTradingMode = currentModeDetection (ALWAYS synchronized)
├── IF currentModeDetection = "Scout" THEN use Scout exit strategy
└── IF currentModeDetection = "Trend" THEN use Trend exit strategy

LOGICAL CONDITIONS (WHEN oneWayTrendLock = TRUE - OPTIONAL):
├── IF strategy.position_size = 0 THEN manualTrendLocked = FALSE (reset for new trade)
├── IF currentModeDetection = "Trend" AND NOT manualTrendLocked THEN
│   manualTrendLocked = TRUE (lock to trend mode for this trade)
├── IF manualTrendLocked = TRUE THEN currentTradingMode = "Trend" (forced trend)
└── ELSE currentTradingMode = currentModeDetection (normal behavior)

EXIT STRATEGY SELECTION:
- ALWAYS use currentTradingMode to determine exit strategy
- Scout mode: Quick profits, tight stops, 70-80% win rate targets  
- Trend mode: Bigger targets, trailing stops, ride the move

DEFAULT BEHAVIOR (oneWayTrendLock = FALSE):
- Scout ↔ Trend ↔ Scout: Switches back and forth freely
- Scout → Trend → Scout: Can go from scalp to trend and BACK to scalp
- Strategy adapts in real-time to market conditions
- Maximum responsiveness to changing market structure
- Exit strategy always matches current market mode

OPTIONAL BEHAVIOR (oneWayTrendLock = TRUE):
- Scout → Trend: Locks to trend mode for remainder of trade
- Trend → Scout: IGNORED (stays locked in trend mode)
- Scout → Trend → Scout: BLOCKED (no return to scout once trend is detected)
- Only exits through trend exit methods once locked
- Single button to enable this feature
```

---

## 🔻 **CORRECTED SHORT ENTRY LOGIC FLOW**

### **STEP 1: SIGNAL GENERATION - UNCHANGED**
```
Same as long logic but for shorts
STATUS: ✅ CORRECT
```

### **STEP 2: STEP CHANNEL - MAJOR REDESIGN**
```
LOGICAL CONDITIONS:
├── stepChannelTrendMode = NOT stepChannelEnable OR (stepChannelState ≠ "Range")
└── stepChannelShortAllowed = NOT stepChannelDirectionalBias OR (close < stepChannelUpper)

DECISION GATE:
IF stepChannelDirectionalBias = FALSE THEN proceed to STEP 3
ELSE IF stepChannelShortAllowed = TRUE THEN proceed to STEP 3
ELSE block trade (Step Channel Directional Bias says NO)

STATUS: 🔥 MAJOR REDESIGN REQUIRED
```

### **STEP 3: DIRECTIONAL BIAS - SIMPLIFIED**
```
LOGICAL CONDITIONS:
└── shortDirectionalBias = NOT directionalBiasEnable OR enabledFilters = 0 OR
    (biasConfluence = "Majority" AND bearishVotes > bullishVotes) OR
    (biasConfluence = "All" AND bullishVotes = 0)

STATUS: 🔧 MINOR FIXES NEEDED
```

### **STEP 4: CVD FILTER - SAME AS LONG**
```
CVD logic is identical for both directions - NO directional bias
STATUS: 🔥 MAJOR REDESIGN REQUIRED
```

### **STEP 5: FINAL CALCULATION - CORRECTED**
```
LOGICAL CONDITIONS:
├── baseShortWithBias = primaryShortSig AND shortDirectionalBias AND 
    (NOT stepChannelDirectionalBias OR stepChannelShortAllowed)

STATUS: 🔧 MINOR FIXES NEEDED
```

---

## 🎯 **MISSING CRITICAL COMPONENT: TREND MODE ACTIVATION & EXIT LOGIC**

### **TREND MODE MASTER CONTROL**
```
BOOLEAN VARIABLES:
├── trendModeEnable (user setting - MASTER SWITCH - DEFAULT: TRUE)
├── currentTradingMode ("Trend Mode" or "Scalp Mode")
├── stepChannelTrendMode (from Step Channel detection)
├── cvdTrendMode (from CVD detection)
└── finalTrendModeActive (combined decision)

LOGICAL CONDITIONS:
├── finalTrendModeActive = trendModeEnable AND (stepChannelTrendMode OR cvdTrendMode)
└── currentTradingMode = finalTrendModeActive ? "Trend Mode" : "Scalp Mode"

MODE DECISION LOGIC:
IF trendModeEnable = FALSE THEN always use "Scalp Mode" (quick exits)
ELSE IF (stepChannelTrendMode OR cvdTrendMode) THEN use "Trend Mode" (hold for bigger moves)
ELSE use "Scalp Mode" (quick exits)

PURPOSE:
- Master on/off switch for entire trend holding system
- Combines input from Step Channel and CVD detectors
- Controls exit strategy behavior
```

### **EXIT BEHAVIOR DIFFERENCES**
```
SCALP MODE EXIT LOGIC:
├── Quick profit targets (70-80% win rate strategy)
├── Tight stop losses
├── Exit on first sign of reversal
├── Target: Small consistent profits
└── Risk: Lower profit per trade

TREND MODE EXIT LOGIC:  
├── Larger profit targets (ride the trend)
├── Wider stop losses or trailing stops
├── Hold through minor pullbacks
├── Target: Bigger moves, higher profit per trade
└── Risk: Lower win rate but higher reward

ACTUAL EXIT IMPLEMENTATION:
IF currentTradingMode = "Scalp Mode" THEN
    ├── Use scalpExitMethod (quick exits)
    ├── Apply scalpProfitTarget
    └── Apply scalpStopLoss
ELSE IF currentTradingMode = "Trend Mode" THEN
    ├── Use trendExitMethod (hold longer)
    ├── Apply trendProfitTarget  
    └── Apply trendTrailingStop
```

### **TREND MODE ACTIVATION DISPLAY**
```
REQUIRED MASTER CONTROL PANEL:
┌─────────────────────────────────────────────┐
│ TREND MODE MASTER CONTROL                   │
├─────────────────────────────────────────────┤
│ Trend Mode Enable: ✅ ON / ❌ OFF           │
│ Current Trading Mode: TREND MODE / SCALP    │
│                                             │
│ MODE DETECTION INPUTS:                      │
│ ├── Step Channel: TREND ✅ / SCALP ❌       │
│ ├── CVD Filter: TREND ✅ / SCALP ❌         │
│ └── Final Decision: TREND MODE ACTIVE       │
│                                             │
│ EXIT STRATEGY:                              │
│ ├── Method: Trend Trailing / Quick Scalp   │
│ ├── Target: [value] / [value]              │
│ └── Stop: [value] / [value]                │
└─────────────────────────────────────────────┘
```

---

## 🛠️ **NEW FEATURES TO ADD**

### **1. DIRECTIONAL BIAS DEBUG TABLE**
```
REQUIRED TABLE DISPLAY:
┌─────────────────┬──────────┬─────────────┬────────────┐
│ Indicator Name  │ Enabled  │ Connected   │ Signal     │
├─────────────────┼──────────┼─────────────┼────────────┤
│ Hull Suite      │ ✅       │ ✅          │ BULLISH    │
│ Trend Strength  │ ✅       │ ❌          │ N/A        │
│ Quadrant        │ ❌       │ N/A         │ N/A        │
│ Adaptive ST     │ ✅       │ ✅          │ BEARISH    │
│ Volumatic       │ ✅       │ ✅          │ BULLISH    │
│ Smooth HA       │ ✅       │ ✅          │ BULLISH    │
└─────────────────┴──────────┴─────────────┴────────────┘

CONFLUENCE SUMMARY:
- Enabled Filters: 5
- Bullish Votes: 3
- Bearish Votes: 1
- Confluence Mode: Majority
- Long Bias: ✅ ALLOWED (3 > 1)
- Short Bias: ❌ BLOCKED (1 < 3)
```

### **2. STEP CHANNEL SETTINGS PANEL**
```
REQUIRED SETTINGS:
├── stepChannelEnable (checkbox - DEFAULT: TRUE)
├── stepChannelDirectionalBias (checkbox - DEFAULT: FALSE)
├── stepChannelLength (input - DEFAULT: current value)
└── stepChannelMultiplier (input - DEFAULT: current value)

DISPLAY CURRENT STATE:
├── Current Mode: "Trend Mode" or "Scalp Mode"
├── Current State: "Range", "Momentum Up", or "Momentum Down"
├── Upper Boundary: [numerical value]
└── Lower Boundary: [numerical value]
```

### **3. CVD DEBUG INFORMATION**
```
REQUIRED CVD DISPLAY:
├── CVD Enabled: ✅/❌
├── CVD Connected: ✅/❌
├── Current CVD Value: [numerical]
├── CVD Threshold: [numerical]
├── Current Mode: "Trend Mode" or "Scalp Mode"
└── Mode Reason: "High Volume Delta" or "Low Volume Delta"
```

---

## 🚨 **CRITICAL IMPLEMENTATION NOTES**

### **1. STEP CHANNEL PRIORITY**
- Primary job: Trend vs Scalp mode detection
- Secondary job: Optional directional bias (separate setting)
- NEVER blocks all trades by default

### **2. CVD ZERO DIRECTIONAL BIAS**
- CVD has absolutely no directional bias logic
- Only controls exit strategy (trend vs scalp)
- Same logic applies to both longs and shorts

### **3. DIRECTIONAL BIAS SIMPLIFICATION**
- Remove "Any" confluence option
- Only "Majority" and "All" options remain
- Must show debug information for troubleshooting

### **4. MODE DETECTION SYSTEM**
```
TWO INDEPENDENT MODE DETECTION SYSTEMS:

SYSTEM 1: STEP CHANNEL MODE
├── Input: Price position relative to channel boundaries
├── Output: "Trend Mode" or "Scalp Mode"
└── Purpose: Controls exit strategy timing

SYSTEM 2: CVD MODE  
├── Input: Volume delta magnitude
├── Output: "Trend Mode" or "Scalp Mode"
└── Purpose: Controls exit strategy based on volume conviction

FINAL MODE DECISION:
IF (stepChannelTrendMode OR cvdTrendMode) THEN "Trend Mode"
ELSE "Scalp Mode"
```

---

## ✅ **IMPLEMENTATION CHECKLIST**

### **PHASE 1: TREND MODE MASTER CONTROL (CRITICAL MISSING PIECE)**
- [ ] Add trendModeEnable master switch (default: true)
- [ ] Implement finalTrendModeActive logic combining Step Channel + CVD
- [ ] Create currentTradingMode variable ("Trend Mode" or "Scalp Mode")
- [ ] Add Trend Mode Master Control Panel display
- [ ] Define exit behavior differences (scalp vs trend methods)

### **PHASE 2: STEP CHANNEL REDESIGN** 
- [ ] Remove directional bias from primary logic
- [ ] Add stepChannelDirectionalBias setting (default: false)
- [ ] Separate trend/scalp detection from directional filtering
- [ ] Add debug display for current mode and boundaries
- [ ] Connect stepChannelTrendMode to master trend activation

### **PHASE 3: CVD REDESIGN**
- [ ] Remove all directional bias logic from CVD
- [ ] Ensure CVD only affects mode detection (not trade blocking)
- [ ] Add CVD debug information display
- [ ] Test CVD independence from other indicators
- [ ] Connect cvdTrendMode to master trend activation

### **PHASE 4: DIRECTIONAL BIAS CLEANUP & EXIT LOGIC FIX**
- [ ] Remove "Any" confluence option
- [ ] Add oppositeSignalExitsEnable setting (default: TRUE)
- [ ] Implement longExitFromShort and shortExitFromLong logic
- [ ] Separate entry blocking from exit allowing
- [ ] Add indicator connection debug table
- [ ] Ensure mid-trend loading works correctly
- [ ] Test majority vs all confluence modes
- [ ] **CRITICAL**: Test opposite signal exits when in directional bias mode

### **PHASE 5: MODE TRANSITION LOGIC IMPLEMENTATION (NEW CRITICAL PHASE)**
- [ ] Ensure currentTradingMode = currentModeDetection (always synchronized by default)
- [ ] Implement real-time mode switching for exit strategies
- [ ] Add oneWayTrendLock setting (default: FALSE)
- [ ] Add manualTrendLocked variable (resets each new trade)
- [ ] Implement optional one-way Scout→Trend locking logic
- [ ] Connect Scout mode to quick exit logic (tight stops, fast profits)
- [ ] Connect Trend mode to trend exit logic (bigger targets, trailing stops)
- [ ] Add mode transition debugging display
- [ ] Test all transition scenarios: Scout→Trend, Trend→Scout, rapid switching
- [ ] Test optional one-way trend lock feature

### **PHASE 6: EXIT SYSTEM INTEGRATION**
- [ ] Implement scout mode exit logic (quick profits, tight stops)
- [ ] Implement trend mode exit logic (bigger targets, trailing stops)
- [ ] Connect currentTradingMode to exit method selection (real-time switching)
- [ ] Test mode switching behavior during live trades
- [ ] Verify immediate exit strategy switching works correctly

### **PHASE 7: FINAL INTEGRATION & TESTING**
- [ ] Update final calculation logic
- [ ] Test complete filter chain with trend mode activation
- [ ] Verify proper exit behavior in both modes
- [ ] Verify no unintended trade blocking
- [ ] Document all debug outputs and control panels

---

## 🎯 **EXPECTED OUTCOME**

### **BEFORE (V1 ISSUES):**
- CVD blocking trades based on direction ❌
- Step Channel always applying directional bias ❌
- No way to debug indicator connections ❌
- "Any" confluence causing confusion ❌
- **CRITICAL**: Directional bias blocks opposite signals completely ❌
- **CRITICAL**: Multi-exit system loses opposite signal exit layer ❌
- **CRITICAL**: NO LOGIC for mode transitions during active trades ❌
- **CRITICAL**: Undefined behavior when Scout→Trend or Trend→Scout ❌
- **NO MASTER TREND MODE ACTIVATION** ❌
- **NO CLEAR EXIT BEHAVIOR LOGIC** ❌
- 3 trades in 3 months due to over-filtering ❌

### **AFTER (V2 FIXES):**
- CVD only controls exit strategy, never blocks direction ✅
- Step Channel primarily detects trend/scalp mode ✅
- Optional Step Channel directional bias (separate setting) ✅
- Debug tables show all indicator states ✅
- Only "Majority" and "All" confluence options ✅
- **CRITICAL**: Opposite signals pass through as exits when in position ✅
- **CRITICAL**: Multi-layered exit system fully preserved ✅
- **CRITICAL**: Mode transition logic handles Scout↔Trend switching ✅
- **CRITICAL**: Real-time mode switching with immediate exit strategy changes ✅
- **oppositeSignalExitsEnable setting (ON by default)** ✅
- **currentTradingMode always synchronized with currentModeDetection (default)** ✅
- **OPTIONAL**: oneWayTrendLock feature (OFF by default) ✅
- **OPTIONAL**: Manual trend locking for big move capture ✅
- **Maximum responsiveness to changing market conditions** ✅
- **NO artificial delays or forced locking mechanisms** ✅
- **MASTER TREND MODE ENABLE SWITCH** ✅
- **CLEAR EXIT LOGIC: Scalp vs Trend behavior** ✅
- **TREND MODE CONTROL PANEL with real-time status** ✅
- Proper trade frequency with corrected filtering ✅

---

This planning document must be approved before any code changes are made to the main strategy file.