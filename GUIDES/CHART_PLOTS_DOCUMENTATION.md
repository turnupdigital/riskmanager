# EZ Algo Trader - Chart Plots Documentation (Updated)

This document lists ALL visual elements currently plotted on the chart by the EZAlgoTrader strategy after cleanup. Use this to understand and manage chart visuals.

## 🔍 **DEBUG & TESTING PLOTS** (Lines 950-953)

### Signal Chain Debug Markers
- **Sig1Long** ('1') - `plotchar` - Yellow tiny '1' at bottom - Signal 1 long trigger debug
- **PrimaryLong** ('●') - `plotchar` - Orange tiny dot at bottom - Primary long signal debug  
- **LongBias** ('B') - `plotchar` - Blue tiny 'B' at bottom - Long directional bias debug
- **LongFinal** ('↑') - `plotchar` - Green small arrow at bottom - Final long entry signal debug

*Note: These are debug plots for troubleshooting signal flow. Can be disabled for cleaner charts.*

## 🎯 **SYSTEM STATUS INDICATORS** (Lines 1014-1015)

### Exit System Status
- **Smart Profit Locker** ('🔒') - `plotchar` - Blue lock on top - Smart profit locker active
- **Trend Change Exit Active** ('📈') - `plotchar` - Green chart icon on top - Trend change exit monitoring active

## 💰 **TRADE MANAGEMENT LINES** (Lines 1049-1050, 1082-1083)

### Fixed SL/TP System
- **Fixed SL** - `plot` - Red dashed line - Fixed stop loss level (when enabled)
- **Fixed TP** - `plot` - Green dashed line - Fixed take profit level (when enabled)

### Dynamic Risk Management  
- **🛑 Stop Loss** - `plot` - Red line (20% transparency, 2px) - Dynamic stop loss level
- **💰 Profit Locker** - `plot` - Green line (20% transparency, 2px) - Smart profit locker level

## 🚀 **ENTRY SIGNAL PLOTS** (Lines 1054-1055)

### Primary Entry Signals
- **BUY** (🔼) - `plotshape` - Green triangles below bars - Long entry signals
- **SELL** (🔽) - `plotshape` - Red triangles above bars - Short entry signals

## 📈 **TECHNICAL INDICATOR LINES** (Line 1074)

### Moving Average Exit System
- **📈 MA Exit Line** - `plot` - Blue line (3px) - Moving average for exit signals

---

## 🧹 **RECENTLY REMOVED PLOTS** (Cleanup History)

### ❌ Removed Yellow Lightning Bolt
- **Hybrid Exit Active** ('⚡') - Previously plotted yellow lightning bolt above chart - REMOVED for cleaner display

### ❌ Removed Reversal Symbols  
- **Trend Change Detected** ('🔄') - Previously plotted red reversal symbol above bars - REMOVED (old exit system)
- **Hybrid: Using Smart Profit Locker** ('🔄') - Previously plotted blue reversal symbol on top - REMOVED (old exit system) 
- **Hybrid: Using Trend Change Exit** ('🔄') - Previously plotted green reversal symbol on top - REMOVED (old exit system)

### ❌ Removed Consolidated Signal Markers
- **Long Signals** ('▲') - Previously plotted green arrows below bars when any long signal active - REMOVED (redundant with main BUY triangles)
- **Short Signals** ('▼') - Previously plotted red arrows above bars when any short signal active - REMOVED (redundant with main SELL triangles)

---

## 🎨 **CHART CLEANUP RECOMMENDATIONS**

### 🟢 **ESSENTIAL PLOTS (Keep These):**
1. **Entry Signals** (Lines 1054-1055) - Core functionality
2. **Dynamic Stop/Profit Lines** (Lines 1082-1083) - Risk management
3. **MA Exit Line** (Line 1074) - Exit system

### 🟡 **DEBUGGING PLOTS (Disable for Clean Chart):**
1. **Signal Debug Markers** (Lines 950-953) - Only needed for troubleshooting
2. **System Status Icons** (Lines 1014-1015) - Visual noise for most users
~~3. **Consolidated Signal Markers** (Lines 1066-1067) - Redundant with main entry signals~~ **REMOVED**

### 🔴 **OPTIONAL PLOTS (User Preference):**
1. **Fixed SL/TP Lines** (Lines 1049-1050) - Only relevant if using fixed levels
2. **Signal Chain Debug** - Useful for development but clutters chart for trading

---

## 🛠️ **QUICK CLEANUP GUIDE**

### For **Minimal Trading Chart:**
Comment out lines: 950-953, 1014-1015, 1049-1050

### For **Balanced Chart:**
Comment out lines: 950-953, 1014-1015

### For **Full Debug Chart:**
Keep all current plots (current state)

---

## 📊 **CURRENT PLOT COUNT**
- **Total Plots**: ~11 visual elements (reduced from 13)
- **Debug Elements**: 6 (can be disabled)
- **Core Trading Elements**: 5 (recommended to keep)

*Last Updated: After removing consolidated signal markers cleanup* 