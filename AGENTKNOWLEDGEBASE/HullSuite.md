# HullSuite.pine - Hull Moving Average Indicator

## Overview
Hull Suite by InSilico - Advanced Hull Moving Average indicator with multiple variations and enhanced visualization options.

**Pine Script Version**: v6  
**Type**: Indicator (overlay=true)

## Key Features
- Three Hull MA variations: HMA, EHMA, THMA
- Multi-timeframe support
- Band visualization option
- Trend-based coloring
- Candle coloring based on Hull trend
- Alert conditions for trend changes

## Input Parameters

### Core Settings
- **Source**: Price source (default: close)
- **Hull Variation**: HMA, THMA, or EHMA
- **Length**: Period length
  - 55: For swing entry signals
  - 180-200: For floating support/resistance levels
- **Length Multiplier**: Multiplier for viewing higher timeframes (default: 1.0)

### Multi-Timeframe Settings
- **Show Hull MA from X timeframe**: Enable higher timeframe analysis
- **Higher Timeframe**: Timeframe selection (default: 240 minutes)

### Visual Settings
- **Color Hull according to trend**: Enable trend-based coloring
- **Color candles based on Hull's Trend**: Enable candle coloring
- **Show as a Band**: Display as band instead of single line
- **Line Thickness**: Line width (default: 1)
- **Band Transparency**: Transparency level (default: 40)

## Hull MA Variations

### 1. HMA (Hull Moving Average)
```pine
HMA(_src, _length) =>
    ta.wma(2 * ta.wma(_src, _length / 2) - ta.wma(_src, _length), math.round(math.sqrt(_length)))
```
- Standard Hull Moving Average
- Reduces lag while maintaining smoothness
- Best for general trend following

### 2. EHMA (Exponential Hull Moving Average)
```pine
EHMA(_src, _length) =>
    ta.ema(2 * ta.ema(_src, _length / 2) - ta.ema(_src, _length), math.round(math.sqrt(_length)))
```
- Uses EMA instead of WMA in calculation
- More responsive to recent price changes
- Good for faster trend detection

### 3. THMA (Triangular Hull Moving Average)
```pine
THMA(_src, _length) =>
    ta.wma(ta.wma(_src, _length / 3) * 3 - ta.wma(_src, _length / 2) - ta.wma(_src, _length), _length)
```
- Uses triangular weighting
- Smoother than standard HMA
- Better for filtering noise

## Technical Implementation

### Mode Selection
```pine
Mode(modeSwitch, src, len) =>
    modeSwitch == 'Hma' ? HMA(src, len) : 
    modeSwitch == 'Ehma' ? EHMA(src, len) : 
    modeSwitch == 'Thma' ? THMA(src, len / 2) : na
```

### Multi-Timeframe Integration
```pine
_hull = Mode(modeSwitch, src, int(length * lengthMult))
HULL = useHtf ? request.security(syminfo.ticker, htf, _hull) : _hull
```

### Trend Detection
- **MHULL**: Current Hull value (HULL[0])
- **SHULL**: Hull value 2 bars ago (HULL[2])
- **Trend**: Bullish when HULL > HULL[2], Bearish when HULL < HULL[2]

### Color Coding
```pine
hullColor = switchColor ? (HULL > HULL[2] ? #00ff00 : #ff0000) : #ff9800
```
- Green (#00ff00): Bullish trend
- Red (#ff0000): Bearish trend
- Orange (#ff9800): Neutral (when trend coloring disabled)

## Alert Conditions
1. **Hull trending up**: Triggered when MHULL crosses above SHULL
2. **Hull trending down**: Triggered when SHULL crosses above MHULL

## Usage in EZAlgoTrader Integration

### Directional Bias Logic
```pine
hullBullish := not na(hullMA) and hullMA > hullMA[2]
```

### Integration Points
- Used as one of the Extended Bias Filters
- Provides trend direction for confluence logic
- Contributes to trend-riding mode activation
- Toggle: `📈 Hull Suite`

## Recommended Settings

### For Swing Trading
- Length: 55
- Mode: HMA
- Show as Band: Enabled
- Trend Coloring: Enabled

### For Scalping
- Length: 21-34
- Mode: EHMA
- Higher Timeframe: Enabled (240m)
- Show as Band: Disabled

### For Support/Resistance
- Length: 180-200
- Mode: THMA
- Length Multiplier: 1.0-1.5
- Show as Band: Enabled

## Performance Characteristics
- Low lag compared to traditional moving averages
- Good trend following capabilities
- Effective noise filtering
- Suitable for multiple timeframes
- Works well in trending markets

## Visual Output
- Main Hull line (MHULL)
- Secondary Hull line for band (SHULL) - optional
- Trend-based coloring
- Optional candle coloring
- Configurable line thickness and transparency
