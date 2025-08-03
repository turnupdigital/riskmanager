# EZAlgoTrader Panel Redesign Plan
## Major Architecture Overhaul - Plot Point Driven Design

---

## 🚨 **CRITICAL PROTECTION RULES**
**THE CORE STRATEGY LOGIC MUST REMAIN UNTOUCHED**
- This is a 70-80% winning strategy
- All additions are enhancements, not replacements
- No simplification of existing logic
- Protect the core with our lives

### **ABSOLUTELY PROTECTED COMPONENTS:**
1. **Step Channel Logic**: MUST remain calculated internally - not straightforward like other indicators, working extremely well
2. **Multi-Signal Entry System**: All features (dropdown, "only" button, etc.) must work as expected
3. **Core Trading Logic**: Entry/exit decisions, signal processing, profit management
4. **Moving Average Crossover Exit**: Can remain calculated internally (simple enough)

### **REQUIRED PLOT POINTS:**
- **Adaptive SuperTrend Volatility Number (1/2/3)**: Must be available as plot point for future strategy use

### **DEFAULT SETTINGS:**
- **Smart Profit Locker**: ON by default (ATR=5, pullback=0.1)
- **MA Exit**: OFF by default
- **Multi-Signal Usage**: Maintain existing dropdown functionality with trend exit option

### **USER WORKFLOW REQUIREMENTS:**
- **Typical Usage**: 5-7 active entry signals (out of 10 available)
- **Signal Management**: Frequent replacement/review of signals as better ones are found
- **Chart Loading**: Strategy loaded on safe chart with pre-configured buy/sell signals
- **Panel Quality**: Enterprise-level, pixel-perfect layout and user-friendliness
- **Logic Integrity**: What user sees on chart must match what strategy acts on

---

## 📋 **EXECUTIVE SUMMARY**

This plan outlines a comprehensive redesign of the EZAlgoTrader user panel and underlying architecture. The primary goal is to move from direct indicator implementations to a plot-point-driven architecture while reorganizing the user interface for better usability and space optimization.

### **Key Objectives:**
1. **Preserve Core Strategy**: Maintain all existing profitable logic
2. **Plot Point Architecture**: Move to external indicator connections
3. **Panel Reorganization**: Optimize space and improve UX
4. **Functionality Verification**: Ensure all critical features work

---

## 🔍 **CURRENT STATE ANALYSIS**

### **Issues Identified:**
- Panel is in "pretty bad shape" with poor organization
- Direct indicator implementations causing accuracy concerns
- Redundant/broken features cluttering the interface
- Inefficient use of panel space
- Missing functionality in some sections

### **Working Components to Preserve:**
- Multi-signal dropdown usage functionality (CRITICAL)
- Core trading logic and entry/exit systems
- Smart Profit Locker
- Fixed stop loss and take profit (levels 1, 2, 3)
- Mason filter functionality

---

## 📐 **DETAILED SECTION-BY-SECTION REDESIGN**

### **1. MULTI-SIGNAL ENTRY SECTION**
**Status:** ✅ Enhanced organization provided by user
**Action:** Implement improved 3-row layout per signal
**Priority:** CRITICAL - All features must work as expected

**New Layout Structure (per signal):**
```
Row 1: [Enable] [Name] [Usage Dropdown]
Row 2: [Long Source] [Short Source]
Row 3: [⭐ Required] [Only] [Trend]
```

**Features to Preserve:**
- ✅ **Usage Dropdown**: Long Only, Short Only, All Entries, Observe, **+ Trend Exit** (new option)
- ✅ **"Only" Button**: Individual signal testing
- ✅ **Required Feature**: Signal must be active for trend mode
- ✅ **Trend Feature**: Include in trend majority calculation
- ✅ **Plot Point Connections**: Long/Short source inputs
- ✅ **Dropdown Logic**: All existing dropdown functionality must work - no dummy features

**Critical Dropdown Requirements:**
- **Trend Exit Option**: Add to usage dropdown for trend-specific exits (won't interrupt scalper)
- **All Logic Functional**: Every dropdown option must have working backend logic
- **No Dummy Features**: If it's in the dropdown, it must work properly

**User-Provided Code Reference:** Complete 10-signal implementation provided in plan document

### **2. FLIP FLOP FILTER**
**Status:** ❌ DELETE COMPLETELY
**Action:** Remove both panel controls and underlying code
**Reason:** Replaced by better-working alternative

### **3. MASON FILTER**
**Status:** ✅ Verify functionality
**Action:** Ensure Mason filter is working correctly
**Priority:** HIGH

### **4. STOP LOSS & TAKE PROFIT**
**Status:** ✅ Verify functionality
**Action:** Ensure fixed SL/TP and levels 1, 2, 3 are working
**Priority:** HIGH

### **5. SMART PROFIT LOCKER**
**Status:** ✅ Verify functionality
**Action:** Ensure Smart Profit Locker is working correctly
**Priority:** HIGH

### **6. BACKTESTING PANELS**
**Status:** ❌ DELETE COMPLETELY
**Action:** Remove backtesting panels (no backtester in indicator)
**Reason:** Unnecessary clutter

---

## 🎯 **TREND EXIT MANAGEMENT SECTION**
**Current Name:** "Trend Exit Filters"
**New Name:** "Trend Exit Management"

### **Current Issues:**
- "Adaptive ST 3 Hold" appears twice
- "Hold on 3" and "Hold on 1" confusing labels
- Poor space utilization

### **Reorganization Plan:**

#### **MA Cross Section Reorganization:**
```
Row 1: [Show MA] [Fast MA] 
Row 2: [MA Type] [Slow MA]
Row 3: [MA Crossover Hold] [Fast MA Title & Checkbox]
```

#### **Exit Logic Enhancement:**
**Add two new inputs:**
- Long Exit Signal Source (plot point input)
- Short Exit Signal Source (plot point input)

**Keep existing trend exit filters as-is** (will be modified later but structure remains)

---

## 🎯 **DIRECTIONAL BIAS SECTION OVERHAUL**

### **REMOVE COMPLETELY:**
- "Use Legacy Trend System" option
- "Bias Source" dropdown
- "Secondary Bias Source" dropdown
- All related confluence logic

### **RATIONALE:**
Bias will be determined by active trend indicators in the new Trend Indicator Section below.

---

## 📊 **NEW TREND INDICATOR SECTION**
**Replaces:** "Extended Bias Filters"
**New Name:** "Trend Indicators"

### **Architecture:** Multi-Signal Style Layout
**Format per indicator:**
```
[Indicator Name] [Long Plot Source] [Short Plot Source] [Usage Dropdown]
```

### **Usage Options:**
- "Exit Only" 
- "Trend Change Exit"
- "Directional Bias"
- "Observe"

### **Space Optimization:**
- 2-3 column layout for maximum efficiency
- Side-by-side arrangement where possible
- Consistent with multi-signal section design

### **Indicators to Include:**
1. **Hull Suite** - Two plot points (Long/Short) → Remove internal calculation code
2. **SuperTrend** - Two plot points (Long/Short) → Remove internal calculation code
3. **Quadrant NW** - Two plot points (Long/Short) → Remove internal calculation code
4. **Adaptive SuperTrend** - Two plot points (Long/Short) + Volatility Number (1/2/3) → Remove internal calculation code
5. **Volumatic VIDYA** - Two plot points (Long/Short) → Remove internal calculation code
6. **Smooth Heiken Ashi** - Two plot points (Long/Short) → Remove internal calculation code
7. **Step Channel** - KEEP INTERNAL CALCULATION (absolutely protected)
8. **MA Crossover Exit** - KEEP INTERNAL CALCULATION (simple enough, working well)

### **Code Removal Strategy:**
- **CAN REMOVE:** All trend indicator calculation code (Hull, SuperTrend, Quadrant, Adaptive, Volumatic, Smooth HA)
- **MUST KEEP:** Step Channel logic, MA crossover logic, core strategy logic
- **REPLACE WITH:** Plot point inputs for external connections

---

## 🔧 **TECHNICAL IMPLEMENTATION STRATEGY**

### **Phase 1: Cleanup & Verification**
1. Remove flip flop filter (code + panel)
2. Remove backtesting panels
3. Verify Mason filter functionality
4. Verify SL/TP levels 1, 2, 3
5. Verify Smart Profit Locker
6. **CRITICAL:** Verify multi-signal usage dropdown functionality

### **Phase 2: Panel Reorganization**
1. Rename "Trend Exit Filters" → "Trend Exit Management"
2. Fix duplicate "Adaptive ST 3 Hold" entries
3. Reorganize MA Cross section layout
4. Add Long/Short exit signal inputs
5. Remove directional bias legacy options

### **Phase 3: Trend Indicator Section**
1. Replace "Extended Bias Filters" with "Trend Indicators"
2. Implement multi-signal style layout
3. Add plot point inputs for each indicator
4. Add usage dropdowns
5. Optimize space with 2-3 column layout

### **Phase 4: Plot Point Architecture**
1. Create external indicator connections
2. Remove direct implementations where appropriate
3. Maintain accuracy through external sources
4. Test all connections thoroughly

---

## ⚠️ **RISK MITIGATION**

### **Core Strategy Protection:**
- No changes to entry/exit logic without explicit approval
- Maintain all existing profitable algorithms
- Add features, don't replace them
- Extensive testing before deployment

### **Functionality Verification:**
- Test every dropdown and input
- Verify all calculations remain identical
- Ensure backward compatibility
- Document all changes

### **Rollback Plan:**
- Maintain backup of current working version
- Document all changes for easy reversal
- Test incrementally, not all at once

---

## 📋 **IMPLEMENTATION CHECKLIST**

### **Pre-Implementation:**
- [ ] User approval of this plan
- [ ] Backup current working version
- [ ] Document current state thoroughly

### **Phase 1 - Cleanup:**
- [ ] Remove flip flop filter (code + panel)
- [ ] Remove backtesting panels
- [ ] Verify Mason filter works
- [ ] Verify SL/TP levels work
- [ ] Verify Smart Profit Locker works
- [ ] **CRITICAL:** Verify usage dropdown functionality

### **Phase 2 - Panel Reorganization:**
- [ ] Rename section headers
- [ ] Fix duplicate entries
- [ ] Reorganize MA Cross layout
- [ ] Add exit signal inputs
- [ ] Remove legacy bias options

### **Phase 3 - Trend Section:**
- [ ] Create new Trend Indicators section
- [ ] Implement multi-signal layout
- [ ] Add plot point inputs
- [ ] Add usage dropdowns
- [ ] Optimize space utilization

### **Phase 4 - Testing:**
- [ ] Test all inputs and dropdowns
- [ ] Verify core strategy unchanged
- [ ] Test plot point connections
- [ ] Performance testing
- [ ] User acceptance testing

---

## 🎯 **SUCCESS CRITERIA**

1. **Core Strategy Preserved:** All existing profitable logic intact
2. **Improved UX:** Better organized, more intuitive panel
3. **Plot Point Architecture:** External connections working perfectly
4. **Space Optimized:** Efficient use of panel real estate
5. **Full Functionality:** All features working as intended
6. **User Satisfaction:** Meets all specified requirements

---

## 📝 **NOTES FOR IMPLEMENTATION**

- This is a major overhaul requiring careful, incremental implementation
- Each phase should be tested thoroughly before proceeding
- User approval required before any code changes
- Core strategy protection is non-negotiable
- Focus on enhancement, not replacement


ENTRIES INPUT DESIGN

THIS IS MUCH BETER FOR ME. PLEASE MAKE SURE THAT ALL SECTION OF TH ENTRIES WORK LIKE THE DROP DOWN AND THE ONLY BUTTON.

// Row 1: Enable + Name + Usage
signal1Enable = input.bool(true, 'Signal 1', inline='sig1', group='2️⃣ Entry Signals', tooltip='Primary signal source')
signal1Name = input.string('LuxAlgo', 'Name', inline='sig1', group='2️⃣ Entry Signals')
signal1Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig1', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal1LongSrc = input.source(close, 'Long', inline='sig1src', group='2️⃣ Entry Signals', tooltip='Connect to: LuxAlgo Long, UTBot Long, or any Long signal plot')
signal1ShortSrc = input.source(close, 'Short', inline='sig1src', group='2️⃣ Entry Signals', tooltip='Connect to: LuxAlgo Short, UTBot Short, or any Short signal plot')
// Row 3: Advanced Options
signal1RequiredForTrend = input.bool(false, '⭐ Required', inline='sig1opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal1OnlyMode = input.bool(false, 'Only', inline='sig1opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal1TrendEnable = input.bool(false, 'Trend', inline='sig1opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')
    
// Signal 2
// Row 1: Enable + Name + Usage
signal2Enable = input.bool(false, 'Signal 2', inline='sig2', group='2️⃣ Entry Signals', tooltip='Secondary signal source')
signal2Name = input.string('UTBot', 'Name', inline='sig2', group='2️⃣ Entry Signals')
signal2Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig2', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal2LongSrc = input.source(close, 'Long', inline='sig2src', group='2️⃣ Entry Signals', tooltip='Connect to: UTBot Long, MACD Long, or any Long signal plot')
signal2ShortSrc = input.source(close, 'Short', inline='sig2src', group='2️⃣ Entry Signals', tooltip='Connect to: UTBot Short, MACD Short, or any Short signal plot')
// Row 3: Advanced Options
signal2RequiredForTrend = input.bool(false, '⭐ Required', inline='sig2opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal2OnlyMode = input.bool(false, 'Only', inline='sig2opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal2TrendEnable = input.bool(false, 'Trend', inline='sig2opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')
    
// Signal 3
// Row 1: Enable + Name + Usage
signal3Enable = input.bool(false, 'Signal 3', inline='sig3', group='2️⃣ Entry Signals', tooltip='Third signal source')
signal3Name = input.string('RSI', 'Name', inline='sig3', group='2️⃣ Entry Signals')
signal3Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig3', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal3LongSrc = input.source(close, 'Long', inline='sig3src', group='2️⃣ Entry Signals', tooltip='Connect to: RSI Long, Stoch Long, or any Long signal plot')
signal3ShortSrc = input.source(close, 'Short', inline='sig3src', group='2️⃣ Entry Signals', tooltip='Connect to: RSI Short, Stoch Short, or any Short signal plot')
// Row 3: Advanced Options
signal3RequiredForTrend = input.bool(false, '⭐ Required', inline='sig3opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal3OnlyMode = input.bool(false, 'Only', inline='sig3opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal3TrendEnable = input.bool(false, 'Trend', inline='sig3opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 4
// Row 1: Enable + Name + Usage
signal4Enable = input.bool(false, 'Signal 4', inline='sig4', group='2️⃣ Entry Signals', tooltip='Fourth signal source')
signal4Name = input.string('MACD', 'Name', inline='sig4', group='2️⃣ Entry Signals')
signal4Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig4', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal4LongSrc = input.source(close, 'Long', inline='sig4src', group='2️⃣ Entry Signals', tooltip='Connect to: MACD Long, CCI Long, or any Long signal plot')
signal4ShortSrc = input.source(close, 'Short', inline='sig4src', group='2️⃣ Entry Signals', tooltip='Connect to: MACD Short, CCI Short, or any Short signal plot')
// Row 3: Advanced Options
signal4RequiredForTrend = input.bool(false, '⭐ Required', inline='sig4opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal4OnlyMode = input.bool(false, 'Only', inline='sig4opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal4TrendEnable = input.bool(false, 'Trend', inline='sig4opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 5
// Row 1: Enable + Name + Usage
signal5Enable = input.bool(false, 'Signal 5', inline='sig5', group='2️⃣ Entry Signals', tooltip='Fifth signal source')
signal5Name = input.string('Stoch', 'Name', inline='sig5', group='2️⃣ Entry Signals')
signal5Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig5', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal5LongSrc = input.source(close, 'Long', inline='sig5src', group='2️⃣ Entry Signals', tooltip='Connect to: Stoch Long, Williams Long, or any Long signal plot')
signal5ShortSrc = input.source(close, 'Short', inline='sig5src', group='2️⃣ Entry Signals', tooltip='Connect to: Stoch Short, Williams Short, or any Short signal plot')
// Row 3: Advanced Options
signal5RequiredForTrend = input.bool(false, '⭐ Required', inline='sig5opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal5OnlyMode = input.bool(false, 'Only', inline='sig5opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal5TrendEnable = input.bool(false, 'Trend', inline='sig5opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 6
// Row 1: Enable + Name + Usage
signal6Enable = input.bool(false, 'Signal 6', inline='sig6', group='2️⃣ Entry Signals', tooltip='Sixth signal source')
signal6Name = input.string('CCI', 'Name', inline='sig6', group='2️⃣ Entry Signals')
signal6Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig6', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal6LongSrc = input.source(close, 'Long', inline='sig6src', group='2️⃣ Entry Signals', tooltip='Connect to: CCI Long, Williams Long, or any Long signal plot')
signal6ShortSrc = input.source(close, 'Short', inline='sig6src', group='2️⃣ Entry Signals', tooltip='Connect to: CCI Short, Williams Short, or any Short signal plot')
// Row 3: Advanced Options
signal6RequiredForTrend = input.bool(false, '⭐ Required', inline='sig6opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal6OnlyMode = input.bool(false, 'Only', inline='sig6opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal6TrendEnable = input.bool(false, 'Trend', inline='sig6opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 7
// Row 1: Enable + Name + Usage
signal7Enable = input.bool(false, 'Signal 7', inline='sig7', group='2️⃣ Entry Signals', tooltip='Seventh signal source')
signal7Name = input.string('ADX', 'Name', inline='sig7', group='2️⃣ Entry Signals')
signal7Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig7', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal7LongSrc = input.source(close, 'Long', inline='sig7src', group='2️⃣ Entry Signals', tooltip='Connect to: ADX Long, MFI Long, or any Long signal plot')
signal7ShortSrc = input.source(close, 'Short', inline='sig7src', group='2️⃣ Entry Signals', tooltip='Connect to: ADX Short, MFI Short, or any Short signal plot')
// Row 3: Advanced Options
signal7RequiredForTrend = input.bool(false, '⭐ Required', inline='sig7opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal7OnlyMode = input.bool(false, 'Only', inline='sig7opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal7TrendEnable = input.bool(false, 'Trend', inline='sig7opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 8
// Row 1: Enable + Name + Usage
signal8Enable = input.bool(false, 'Signal 8', inline='sig8', group='2️⃣ Entry Signals', tooltip='Eighth signal source')
signal8Name = input.string('MFI', 'Name', inline='sig8', group='2️⃣ Entry Signals')
signal8Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig8', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal8LongSrc = input.source(close, 'Long', inline='sig8src', group='2️⃣ Entry Signals', tooltip='Connect to: MFI Long, OBV Long, or any Long signal plot')
signal8ShortSrc = input.source(close, 'Short', inline='sig8src', group='2️⃣ Entry Signals', tooltip='Connect to: MFI Short, OBV Short, or any Short signal plot')
// Row 3: Advanced Options
signal8RequiredForTrend = input.bool(false, '⭐ Required', inline='sig8opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal8OnlyMode = input.bool(false, 'Only', inline='sig8opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal8TrendEnable = input.bool(false, 'Trend', inline='sig8opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 9
// Row 1: Enable + Name + Usage
signal9Enable = input.bool(false, 'Signal 9', inline='sig9', group='2️⃣ Entry Signals', tooltip='Ninth signal source')
signal9Name = input.string('OBV', 'Name', inline='sig9', group='2️⃣ Entry Signals')
signal9Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig9', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal9LongSrc = input.source(close, 'Long', inline='sig9src', group='2️⃣ Entry Signals', tooltip='Connect to: OBV Long, Custom Long, or any Long signal plot')
signal9ShortSrc = input.source(close, 'Short', inline='sig9src', group='2️⃣ Entry Signals', tooltip='Connect to: OBV Short, Custom Short, or any Short signal plot')
// Row 3: Advanced Options
signal9RequiredForTrend = input.bool(false, '⭐ Required', inline='sig9opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal9OnlyMode = input.bool(false, 'Only', inline='sig9opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal9TrendEnable = input.bool(false, 'Trend', inline='sig9opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')

// Signal 10
// Row 1: Enable + Name + Usage
signal10Enable = input.bool(false, 'Signal 10', inline='sig10', group='2️⃣ Entry Signals', tooltip='Tenth signal source')
signal10Name = input.string('Custom', 'Name', inline='sig10', group='2️⃣ Entry Signals')
signal10Usage = input.string('All Entries', 'Usage', options=['Long Only', 'Short Only', 'All Entries', 'Observe'], inline='sig10', group='2️⃣ Entry Signals')
// Row 2: Long + Short Sources
signal10LongSrc = input.source(close, 'Long', inline='sig10src', group='2️⃣ Entry Signals', tooltip='Connect to: Custom Long, External Long, or any Long signal plot')
signal10ShortSrc = input.source(close, 'Short', inline='sig10src', group='2️⃣ Entry Signals', tooltip='Connect to: Custom Short, External Short, or any Short signal plot')
// Row 3: Advanced Options
signal10RequiredForTrend = input.bool(false, '⭐ Required', inline='sig10opts', group='2️⃣ Entry Signals', tooltip='This signal MUST be active for trend mode activation')
signal10OnlyMode = input.bool(false, 'Only', inline='sig10opts', group='2️⃣ Entry Signals', tooltip='Make this the ONLY active signal (for individual testing)')
signal10TrendEnable = input.bool(false, 'Trend', inline='sig10opts', group='2️⃣ Entry Signals', tooltip='Include this signal in trend majority calculation')
    
   
smartProfitEnable := input.bool(true, 'Enable Smart Profit Locker', group='3️⃣ Exit Systems › SPL', tooltip='Smart trailing stop that locks in profits')
smartProfitType := input.string('ATR', 'Distance Type', options=['ATR', 'Points', 'Percent'], group='3️⃣ Exit Systems › SPL')
smartProfitVal := input.float(2.0, 'Distance Value', step=0.1, minval=0.1, group='3️⃣ Exit Systems › SPL')
smartProfitOffset := input.float(0.1, 'Trail Offset', step=0.01, minval=0.01, group='3️⃣ Exit Systems › SPL')

fixedEnable := input.bool(false, 'Enable Fixed SL/TP', group='3️⃣ Exit Systems › Fixed', tooltip='Traditional stop-loss and take-profit levels')
fixedStop := input.float(1.5, 'Stop Loss (ATR)', step=0.1, minval=0.1, group='3️⃣ Exit Systems › Fixed')
tp1Enable := input.bool(false, 'Enable Take Profit', group='3️⃣ Exit Systems › Fixed')
tp1Size := input.float(2.0, 'Take Profit (ATR)', step=0.1, minval=0.1, group='3️⃣ Exit Systems › Fixed')

maExitOn := input.bool(false, 'Enable MA Exit', group='3️⃣ Exit Systems › MA', tooltip='Exit when price crosses moving average')
maLen := input.int(21, 'MA Length', minval=1, group='3️⃣ Exit Systems › MA')
maType := input.string('EMA', 'MA Type', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA'], group='3️⃣ Exit Systems › MA')

bbExitTightness := input.float(0.5, 'BB Exit Tightness', step=0.1, minval=0.1, maxval=1.0, group='3️⃣ Exit Systems › BB', tooltip='How much to tighten SPL when BB exit triggers')
    

**END OF PLAN**

*This document serves as the master blueprint for the EZAlgoTrader panel redesign. No implementation should begin without user approval of this plan.*
