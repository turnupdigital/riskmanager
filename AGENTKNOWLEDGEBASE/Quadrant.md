# Quadrant.pine - Nadaraya-Watson Rational Quadratic Kernel

## Overview
Nadaraya-Watson: Rational Quadratic Kernel (Non-Repainting) by jdehorty - Advanced kernel regression indicator for smooth trend estimation and signal generation.

**Pine Script Version**: v6  
**Type**: Indicator (overlay=true)  
**License**: Mozilla Public License 2.0  
**Key Feature**: Non-repainting implementation

## Key Features
- Non-repainting Nadaraya-Watson regression
- Rational Quadratic Kernel implementation
- Smooth trend estimation with minimal lag
- Crossover-based signal generation
- Customizable color schemes and smoothing
- Alert conditions for trend changes

## Input Parameters

### Core Settings
- **Source**: Price source (default: close)
- **Lookback Window (h)**: Number of bars for estimation (default: 8.0, min: 3.0)
  - Recommended range: 3-50
  - Sliding window of most recent historical bars
- **Relative Weighting (r)**: Time frame weighting (default: 8.0, step: 0.25)
  - Recommended range: 0.25-25
  - Lower values: longer time frames have more influence
  - Higher values: approaches Gaussian kernel behavior
- **Start Regression at Bar**: Bar index to start regression (default: 25)
  - Recommended range: 5-25
  - Omits volatile initial bars for better fit

### Color Settings
- **Smooth Colors**: Uses crossover mechanism for color determination
- **Lag**: Lag for crossover detection (default: 2, range: 1-2)
- **Bullish Color**: Color for bullish trend (default: #3AFF17)
- **Bearish Color**: Color for bearish trend (default: #FD1707)

## Technical Implementation

### Kernel Regression Function
```pine
kernel_regression(_src, _size, _h) =>
    float _currentWeight = 0.
    float _cumulativeWeight = 0.
    for i = 0 to _size + x_0 by 1
        y = _src[i]
        w = math.pow(1 + math.pow(i, 2) / (math.pow(_h, 2) * 2 * r), -r)
        _currentWeight := _currentWeight + y * w
        _cumulativeWeight := _cumulativeWeight + w
    _currentWeight / _cumulativeWeight
```

### Dual Estimation System
- **yhat1**: Primary kernel regression estimate
- **yhat2**: Secondary estimate with lag adjustment for crossover detection

### Signal Generation

#### Rate of Change Signals
- **wasBearish**: yhat1[2] > yhat1[1]
- **wasBullish**: yhat1[2] < yhat1[1]
- **isBearish**: yhat1[1] > yhat1
- **isBullish**: yhat1[1] < yhat1
- **isBearishChange**: Transition from bullish to bearish
- **isBullishChange**: Transition from bearish to bullish

#### Crossover Signals
- **isBullishCross**: ta.crossover(yhat2, yhat1)
- **isBearishCross**: ta.crossunder(yhat2, yhat1)
- **isBullishSmooth**: yhat2 > yhat1
- **isBearishSmooth**: yhat2 < yhat1

### Color Logic
```pine
color colorByCross = isBullishSmooth ? c_bullish : c_bearish
color colorByRate = isBullish ? c_bullish : c_bearish
color plotColor = smoothColors ? colorByCross : colorByRate
```

## Alert System

### Alert Conditions
1. **Bearish Color Change**: When trend turns bearish
2. **Bullish Color Change**: When trend turns bullish

### Alert Logic
```pine
bool alertBullish = smoothColors ? isBearishCross : isBearishChange
bool alertBearish = smoothColors ? isBullishCross : isBullishChange
```

### Alert Messages
- "Nadaraya-Watson: {{ticker}} ({{interval}}) turned Bearish ▼"
- "Nadaraya-Watson: {{ticker}} ({{interval}}) turned Bullish ▲"

## Usage in EZAlgoTrader Integration

### Directional Bias Logic
```pine
quadrantBullish := not na(quadrantValue) and quadrantValue > quadrantValue[1]
```

### Integration Points
- Used as one of the Extended Bias Filters
- Provides smooth trend direction for confluence logic
- Contributes to trend-riding mode activation
- Toggle: `📊 Quadrant NW`

### Key Variables
- `quadrantValue`: The kernel regression estimate (yhat1)
- `quadrantBullish`: Boolean trend state based on slope

## Mathematical Foundation

### Rational Quadratic Kernel
The indicator uses a Rational Quadratic Kernel function:
```
w = (1 + i²/(h² * 2 * r))^(-r)
```

Where:
- **i**: Distance from current bar
- **h**: Lookback window (bandwidth parameter)
- **r**: Relative weighting parameter

### Kernel Properties
- **Smoothness**: Provides smooth estimates with minimal noise
- **Adaptability**: Adjusts to local data characteristics
- **Non-parametric**: No assumptions about underlying data distribution
- **Non-repainting**: Uses only confirmed historical data

## Parameter Optimization

### Conservative Settings (Smoother, Less Sensitive)
- Lookback Window: 12-20
- Relative Weighting: 10-15
- Lag: 2
- Good for: Trend following, reducing false signals

### Aggressive Settings (More Responsive)
- Lookback Window: 5-8
- Relative Weighting: 3-6
- Lag: 1
- Good for: Scalping, early signal detection

### Balanced Settings (Default)
- Lookback Window: 8
- Relative Weighting: 8
- Lag: 2
- Good for: Most trading applications

## Performance Characteristics

### Strengths
- Non-repainting signals
- Smooth trend estimation
- Minimal lag compared to moving averages
- Adaptive to market conditions
- Strong mathematical foundation
- Works well in trending and ranging markets

### Considerations
- More complex than simple moving averages
- Requires parameter tuning for optimal performance
- May be slower to react in very fast markets
- Computational intensity higher than basic indicators

## Visual Output
- **Main Plot**: Kernel regression estimate line
- **Color Coding**: 
  - Green: Bullish trend
  - Red: Bearish trend
- **Line Width**: 2 pixels for clear visibility
- **Hidden Plot**: Alert stream for external use (-1/0/1 values)

## Best Practices
1. Start with default parameters and adjust based on market characteristics
2. Use smooth colors for cleaner visual signals
3. Combine with other indicators for confirmation
4. Consider market volatility when setting lookback window
5. Test different lag settings for optimal crossover timing
6. Use alerts for timely signal notifications

## Integration Notes
- Fully compatible with multi-timeframe analysis
- No repainting issues (safe for backtesting and live trading)
- Provides both trend direction and strength information
- Suitable for automated trading systems
- Works well with other kernel-based or regression indicators
