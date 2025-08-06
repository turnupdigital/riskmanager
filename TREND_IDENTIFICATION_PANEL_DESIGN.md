# 🎯 TREND IDENTIFICATION PANEL DESIGN

## 📋 **PROPOSED INPUT PANEL LAYOUT**

```pine
// ═══════════════════ TREND IDENTIFICATION ═══════════════════
// Step Channel-based trend/scalp mode detection system

// ─────────────────── MASTER CONTROLS ───────────────────
stepChannelEnable = input.bool(true, 'Enable Trend Identification', 
    group='Trend Identification', 
    tooltip='Enable Step Channel-based trend/scalp mode switching')

showTrendDebug = input.bool(false, 'Show Debug Info', 
    group='Trend Identification', 
    tooltip='Display trend detection debug information on chart')

// ─────────────────── STEP CHANNEL CORE SETTINGS ───────────────────
stepChannelLength = input.int(3, 'Length', minval=1, maxval=10, 
    group='Trend Identification', inline='sc1',
    tooltip='Pivot Length - Number of bars for pivot high/low detection')

stepChannelMultiplier = input.float(1.0, 'Multiplier', minval=0.1, maxval=2.0, step=0.1, 
    group='Trend Identification', inline='sc1',
    tooltip='Bands Multiplier - ATR multiplier for upper/lower bands')

stepChannelAtrLength = input.int(200, 'ATR Length', minval=5, maxval=500, 
    group='Trend Identification', inline='sc2',
    tooltip='ATR period for band calculations (original uses 200)')

// ─────────────────── CONFIRMATION SETTINGS ───────────────────
confirmationMethod = input.string('ATR Distance', 'Confirmation Method', 
    options=['ATR Distance', 'Bar Count', 'Combined', 'Momentum'], 
    group='Trend Identification',
    tooltip='Method to confirm trend breakouts and prevent false signals')

// ATR Distance Confirmation
atrConfirmMultiplier = input.float(0.5, 'ATR Confirmation', minval=0.1, maxval=2.0, step=0.1, 
    group='Trend Identification', inline='conf1',
    tooltip='ATR multiplier beyond breakout level for confirmation (0.5 = half ATR)')

// Bar Count Confirmation  
barCountConfirm = input.int(3, 'Bar Count', minval=1, maxval=10, 
    group='Trend Identification', inline='conf1',
    tooltip='Number of consecutive bars required for trend confirmation')

// Combined Method Settings
combinedAtrMult = input.float(0.3, 'Combined ATR', minval=0.1, maxval=1.0, step=0.1, 
    group='Trend Identification', inline='conf2',
    tooltip='ATR multiplier for combined method (lower than pure ATR)')

combinedBarCount = input.int(2, 'Combined Bars', minval=1, maxval=5, 
    group='Trend Identification', inline='conf2',
    tooltip='Bar count for combined method (lower than pure bar count)')

// Momentum Confirmation
momentumThreshold = input.float(0.25, 'Momentum Threshold', minval=0.1, maxval=1.0, step=0.05, 
    group='Trend Identification', inline='conf3',
    tooltip='ATR multiplier for momentum confirmation threshold')

momentumLookback = input.int(2, 'Momentum Bars', minval=1, maxval=5, 
    group='Trend Identification', inline='conf3',
    tooltip='Number of bars for momentum calculation')

// ─────────────────── VISUAL SETTINGS ───────────────────
showStepChannelLines = input.bool(true, 'Show Channel Lines', 
    group='Trend Identification', inline='vis1',
    tooltip='Display Step Channel upper/lower lines and midline')

colorStepCandles = input.bool(true, 'Color Candles', 
    group='Trend Identification', inline='vis1',
    tooltip='Color candles based on Step Channel state (Green/Red/Orange)')

showConfirmationLevels = input.bool(false, 'Show Confirmation Levels', 
    group='Trend Identification', inline='vis2',
    tooltip='Display confirmation threshold levels as dotted lines')

showModeBackground = input.bool(true, 'Mode Background', 
    group='Trend Identification', inline='vis2',
    tooltip='Color chart background based on current mode (SCALP/TREND)')

// ─────────────────── COLOR SETTINGS (EXACT MATCH TO ORIGINAL) ───────────────────
trendUpColor = input.color(color.rgb(26, 190, 127), 'Momentum Up Color', 
    group='Trend Identification', inline='colors',
    tooltip='Color for momentum up candles, lines, and background')

trendDownColor = input.color(color.rgb(202, 38, 65), 'Momentum Down Color', 
    group='Trend Identification', inline='colors',
    tooltip='Color for momentum down candles, lines, and background')

scalpColor = input.color(color.orange, 'Range Color', 
    group='Trend Identification', inline='colors',
    tooltip='Color for range/scalp mode candles, lines, and background')
```

---

## 🎨 **VISUAL MOCKUP**

### **Panel Section: "Trend Identification"**

```
┌─────────────────────────────────────────────────────────┐
│ Trend Identification                                    │
├─────────────────────────────────────────────────────────┤
│ ☑ Enable Trend Identification                           │
│ ☐ Show Debug Info                                      │
├─────────────────────────────────────────────────────────┤
│ Length: [3    ] Multiplier: [1.0  ]                    │
│ ATR Length: [200  ]                                    │
├─────────────────────────────────────────────────────────┤
│ Confirmation Method: [ATR Distance        ▼]           │
│ ATR Confirmation: [0.5 ] Bar Count: [3   ]            │
│ Combined ATR: [0.3] Combined Bars: [2    ]            │
│ Momentum Threshold: [0.25] Momentum Bars: [2]         │
├─────────────────────────────────────────────────────────┤
│ ☑ Show Channel Lines  ☑ Color Candles                 │
│ ☐ Show Confirmation   ☑ Mode Background               │
├─────────────────────────────────────────────────────────┤
│ Momentum Up: [■] Momentum Down: [■] Range: [■]        │
└─────────────────────────────────────────────────────────┘
```

---

## 🔧 **DYNAMIC INPUT BEHAVIOR**

### **Confirmation Method Dropdown:**
- **"ATR Distance"** → Only shows ATR Confirmation input
- **"Bar Count"** → Only shows Bar Count input  
- **"Combined"** → Shows Combined ATR + Combined Bars
- **"Momentum"** → Shows Momentum Threshold + Momentum Bars

### **Visual Settings:**
- All visual elements can be toggled independently
- Colors are fully customizable
- Debug info shows/hides based on toggle

---

## 📊 **EXPECTED USER EXPERIENCE**

### **Simple Setup:**
1. **Enable Trend Identification** ✅
2. **Choose Confirmation Method** (ATR Distance recommended)
3. **Adjust confirmation level** (0.5 ATR default)
4. **Done!** - Everything else uses smart defaults

### **Advanced Tuning:**
- **Aggressive:** ATR 0.2, Bar Count 1
- **Balanced:** ATR 0.5, Bar Count 3 (default)
- **Conservative:** ATR 1.0, Bar Count 5

### **Visual Feedback:**
- **Real-time candle coloring** (Green/Red/Orange) - exact match to original
- **Channel lines** showing breakout levels (midline + upper/lower bands)
- **Background coloring** based on current mode (SCALP/TREND)
- **Confirmation levels** (optional dotted lines)
- **Professional indicator appearance** - no icons or childish elements

---

## 🎯 **IMPLEMENTATION NOTES**

### **EXACT VISUAL MATCHING REQUIREMENTS:**
- **Must overlay perfectly** with original Step Channel indicator
- **Identical calculations:** Same pivot logic, ATR(200), exact band calculations
- **Identical visuals:** Same candle coloring, line styles, colors
- **No visual differences** when both indicators are loaded
- **Professional appearance:** Clean lines, proper colors, no decorative elements

### **Smart Defaults (Match Original):**
- Length: 3 (pivot detection)
- Multiplier: 1.0 (band width) 
- ATR Length: 200 (matches original)
- Confirmation method: "ATR Distance"
- All visual elements enabled for immediate feedback

### **User Control:**
- **Nothing hardcoded** - everything is configurable
- **Tooltip explanations** for each setting
- **Logical grouping** with inline layouts
- **Professional naming** - no emoji clutter

### **Performance:**
- Calculations only run when enabled
- Visual elements can be disabled to reduce overhead
- Debug info is optional

---

## 🤔 **QUESTIONS FOR YOU:**

1. **Default Confirmation Method:** Start with "ATR Distance" or give choice?
2. **Visual Complexity:** Show all visual options or keep it simpler?
3. **Color Customization:** Full color control or use fixed colors?
4. **Debug Level:** What debug info do you want to see?

**Does this layout look good to you?** Should I adjust anything before implementing?