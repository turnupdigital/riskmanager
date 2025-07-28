# SuperTrend.pine - SuperTrend Indicator

## Overview
SuperTrend indicator - ATR-based trend following indicator that provides clear buy/sell signals with dynamic support and resistance levels.

**Pine Script Version**: v6  
**Type**: Indicator (overlay=true)  
**Features**: Timeframe gaps support

## Key Features
- ATR-based dynamic support/resistance levels
- Clear trend direction signals
- Background coloring for trend visualization
- Alert conditions for trend changes
- Built using Pine Script's native `ta.supertrend()` function

## Input Parameters

### Core Settings
- **ATR Length**: Period for ATR calculation (default: 10, minimum: 1)
- **Factor**: Multiplier for ATR (default: 3.0, minimum: 0.01, step: 0.01)

## Technical Implementation

### SuperTrend Calculation
```pine
[supertrend, direction] = ta.supertrend(factor, atrPeriod)
supertrend := barstate.isfirst ? na : supertrend
```

### Direction Values
- **direction < 0**: Uptrend (bullish)
- **direction > 0**: Downtrend (bearish)

### Visual Components
1. **Up Trend Line**: Green line when direction < 0
2. **Down Trend Line**: Red line when direction > 0
3. **Background Fill**: 
   - Green background (90% transparency) during uptrends
   - Red background (90% transparency) during downtrends

## Signal Logic

### Trend Identification
- **Uptrend**: When price is above SuperTrend line (direction < 0)
- **Downtrend**: When price is below SuperTrend line (direction > 0)

### Trend Changes
- **Bullish Signal**: Direction changes from > 0 to < 0
- **Bearish Signal**: Direction changes from < 0 to > 0

## Alert Conditions

### Available Alerts
1. **Downtrend to Uptrend**: Triggered when direction[1] > direction
2. **Uptrend to Downtrend**: Triggered when direction[1] < direction  
3. **Trend Change**: Triggered on any trend direction change

### Alert Messages
- "The Supertrend value switched from Downtrend to Uptrend"
- "The Supertrend value switched from Uptrend to Downtrend"
- Generic trend change message

## Usage in EZAlgoTrader Integration

### Directional Bias Logic
```pine
supertrendBullish := not na(supertrendValue) and supertrendDirection < 0
```

### Integration Points
- Used as one of the Extended Bias Filters
- Provides clear trend direction for confluence logic
- Contributes to trend-riding mode activation
- Toggle: `🔥 SuperTrend`

### Key Variables
- `supertrendValue`: The SuperTrend line value
- `supertrendDirection`: Direction indicator (-1 for up, 1 for down)
- `supertrendBullish`: Boolean trend state

## Parameter Optimization

### Conservative Settings (Less Signals, More Reliable)
- ATR Length: 14-21
- Factor: 3.5-4.0
- Good for: Swing trading, trend following

### Aggressive Settings (More Signals, More Noise)
- ATR Length: 7-10
- Factor: 2.0-2.5
- Good for: Scalping, short-term trading

### Balanced Settings (Default)
- ATR Length: 10
- Factor: 3.0
- Good for: Most trading styles

## Performance Characteristics
- **Strengths**:
  - Clear trend identification
  - Dynamic support/resistance levels
  - Low lag compared to moving averages
  - Works well in trending markets
  - Built-in volatility adjustment via ATR

- **Weaknesses**:
  - Can whipsaw in sideways markets
  - Late signals in fast-moving markets
  - May give false signals during consolidation

## Visual Output
- **Green Line**: SuperTrend support level during uptrends
- **Red Line**: SuperTrend resistance level during downtrends
- **Green Background**: Uptrend periods (90% transparency)
- **Red Background**: Downtrend periods (90% transparency)
- **Body Middle**: Hidden reference line for background fills

## Best Practices
1. Use higher Factor values in volatile markets
2. Combine with other indicators for confirmation
3. Avoid trading during sideways/consolidating markets
4. Consider market volatility when setting parameters
5. Use alerts for timely trend change notifications

## Integration Notes
- Compatible with multi-timeframe analysis
- Works well with other trend-following indicators
- Suitable for automated trading systems
- Provides clear binary signals (trend up/down)
- No repainting issues (uses confirmed bars)
