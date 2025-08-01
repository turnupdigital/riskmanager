# EZAlgoTrader Entry Signals Reorganization Plan

## Current Layout (6 rows per signal)
Each signal currently uses 6 rows with the following layout:

```
Row 1: [✓ Signal 1] [Long Source] [Short Source]
Row 2: [Name Field] 
Row 3: [Usage Dropdown]
Row 4: [✓ Only Mode]
Row 5: [✓ Use as Trend Indicator]
Row 6: [✓ Required for Trend]
```

## New Proposed Layout (3 rows per signal)
Reorganize to use columns more efficiently and reduce to 3 rows:

```
Row 1: [✓ Signal 1] [Name Field] [Usage Dropdown]
Row 2: [Long Source] [Short Source]
Row 3: [✓ Required for Trend] [✓ Only Mode] [✓ Use as Trend]
```

## Implementation Details

### Row 1 - Basic Configuration
- **Column 1**: Signal enable checkbox (existing: `inline='sig1'`)
- **Column 2**: Signal name input (move from separate line to `inline='sig1'`)
- **Column 3**: Usage dropdown (move from separate line to `inline='sig1'`)

### Row 2 - Source Configuration  
- **Column 1**: Long source picker (existing: `inline='sig1'`)
- **Column 2**: Short source picker (existing: `inline='sig1'`)

### Row 3 - Advanced Options
- **Column 1**: Required for Trend checkbox (new inline group: `inline='sig1opts'`)
- **Column 2**: Only Mode checkbox (move to `inline='sig1opts'`)
- **Column 3**: Use as Trend checkbox (move to `inline='sig1opts'`)

## Text Optimizations for Row 3
To fit all three checkboxes in one row, we'll shorten the labels:

- **Current**: "⭐ Required for Trend" → **New**: "⭐ Required"
- **Current**: "Only" → **Keep**: "Only" 
- **Current**: "Use as Trend Indicator" → **New**: "Trend"

## Benefits
1. **Space Efficiency**: Reduces from 6 rows to 3 rows per signal (50% reduction)
2. **Visual Clarity**: Groups related options logically
3. **Better UX**: More compact, easier to scan
4. **Consistency**: All 10 signals will follow the same pattern

## Implementation Strategy
1. Update Signal 1 as the template
2. Apply the same pattern to Signals 2-10
3. Test compilation after each signal
4. Verify all functionality remains intact

## Code Changes Required
- Move `signal1Name` to `inline='sig1'`
- Move `signal1Usage` to `inline='sig1'`
- Create new inline group `inline='sig1opts'` for Row 3 checkboxes
- Shorten checkbox labels for better fit
- Repeat pattern for all 10 signals
