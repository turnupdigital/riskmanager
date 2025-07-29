1. Core Pine Script Label Functions & Styles 12




Bar coloring
The barcolor() function colors bars on the main chart, regardless of whether the script is running in the main chart pane or a separate pane.

The function’s signature is:

barcolor(color, offset, editable, show_last, title, display) → void
The coloring can be conditional because the color parameter accepts “series color” arguments.

The following script renders inside and outside bars in different colors:

image

Pine Script®
Copied
//@version=6
indicator("barcolor example", overlay = true)
isUp = close > open
isDown = close <= open
isOutsideUp = high > high[1] and low < low[1] and isUp
isOutsideDown = high > high[1] and low < low[1] and isDown
isInside = high < high[1] and low > low[1]
barcolor(isInside ? color.yellow : isOutsideUp ? color.aqua : isOutsideDown ? color.purple : na)
Note that:

The na value leaves bars as is.
In the barcolor() call, we use embedded ?: ternary operator expressions to select the color.
label.new(): The primary function for dynamic labels. Supports:

style: Use labelStyle with strings like:

"Arrow Down", "Arrow Up", "Circle", "Cross", "Diamond", "Flag", "Square", "Triangle Up" 1.

"Label Center", "Label Upper Right", etc., for text-only labels.

size: size.tiny, size.small, size.normal, size.large, size.huge.

textcolor & color: Control text/background colors.

textalign: Alignment within the label.

tooltip: Hover text for additional data.

Dynamic content: Embed series values using str.tostring() (e.g., "High: " + str.tostring(high))2.

Example Snippet:

pine
label.new(bar_index, high, "High: " + str.tostring(high), 
          style=label.style_label_down, color=color.red, 
          textcolor=color.white, size=size.normal)
2. Other Text/Shape Display Methods 2
plotshape():

Better for multi-line text vs. plotchar().

Use location.abovebar, location.belowbar for positioning.

Shapes: shape.arrowup, shape.circle, shape.xcross.

plotchar():

Limited to single characters (e.g., ▲, ▼ Unicode arrows).

plotarrow():

Displays up/down arrows sized proportionally to a value.

Tables: For static text (e.g., performance stats) that doesn’t move with chart scrolling.

3. Positioning & Advanced Control 12
position: Use position.bottom_center, position.top_right, etc.

Offset: Dynamic xloc.bar_index and yloc.price to anchor labels to bars.

Libraries:

ObjectHelpersLibrary: Simplifies updating labels/boxes/lines (e.g., set(label, text="New text"))1.

StyleLibrary: Converts strings to style constants (e.g., labelStyle("Circle")).

4. Manual Drawing Tools 34
Price Note Tool:

Add text labels anchored to price levels.

Customize background/border colors, fonts, and slopes (hold Shift for 45° lines).

Pre/Post-Market Labels:

Enable via Price Scale → Context Menu → Labels → Pre/Post-Market9.

5. Design Best Practices 26
Limit Labels: Max 500 per script to avoid clutter.

Color Contrast: Use dark text on light backgrounds (or vice versa).

Unicode Symbols: Emojis (e.g., ⚡, 🔥) or arrows (🠇, 🠅) for visual cues.

Hybrid Approach: Combine Pine Script labels with manual drawings for complex layouts.

Key Resources:
Official Docs:
Text and Shapes Guide 2
Label Style Reference 1

Community Scripts:
Search TradingView for "EMA/SMA Ribbon Pro" or "Stacked Labels" for real-world examples 6.

AI Prompt Tip:

"Generate Pine Script code for a TradingView label with [description]. Use style=label.style_diamond, size=size.large, and dynamic price text."

Common Pitfalls to Fix "Ugly" Charts:
Overlapping Labels: Use label.delete() or label.set_x() with offset logic.

Inconsistent Styling: Define color/size variables upfront (e.g., bullColor = #00FF00).

Static Text: Use str.tostring(close) for real-time price updates.

Unreadable Positions: Anchor to yloc.abovebar if labels obscure candles.

label.new(): The primary function for dynamic labels. Supports:

style: Use labelStyle with strings like:

"Arrow Down", "Arrow Up", "Circle", "Cross", "Diamond", "Flag", "Square", "Triangle Up" 1.

"Label Center", "Label Upper Right", etc., for text-only labels.

size: size.tiny, size.small, size.normal, size.large, size.huge.

textcolor & color: Control text/background colors.

textalign: Alignment within the label.

tooltip: Hover text for additional data.

Dynamic content: Embed series values using str.tostring() (e.g., "High: " + str.tostring(high))2.

Example Snippet:

pine
label.new(bar_index, high, "High: " + str.tostring(high), 
          style=label.style_label_down, color=color.red, 
          textcolor=color.white, size=size.normal)
2. Other Text/Shape Display Methods 2
plotshape():

Better for multi-line text vs. plotchar().

Use location.abovebar, location.belowbar for positioning.

Shapes: shape.arrowup, shape.circle, shape.xcross.

plotchar():

Limited to single characters (e.g., ▲, ▼ Unicode arrows).

plotarrow():

Displays up/down arrows sized proportionally to a value.

Tables: For static text (e.g., performance stats) that doesn’t move with chart scrolling.

3. Positioning & Advanced Control 12
position: Use position.bottom_center, position.top_right, etc.

Offset: Dynamic xloc.bar_index and yloc.price to anchor labels to bars.

Libraries:

ObjectHelpersLibrary: Simplifies updating labels/boxes/lines (e.g., set(label, text="New text"))1.

StyleLibrary: Converts strings to style constants (e.g., labelStyle("Circle")).

4. Manual Drawing Tools 34
Price Note Tool:

Add text labels anchored to price levels.

Customize background/border colors, fonts, and slopes (hold Shift for 45° lines).

Pre/Post-Market Labels:

Enable via Price Scale → Context Menu → Labels → Pre/Post-Market9.

5. Design Best Practices 26
Limit Labels: Max 500 per script to avoid clutter.

Color Contrast: Use dark text on light backgrounds (or vice versa).

Unicode Symbols: Emojis (e.g., ⚡, 🔥) or arrows (🠇, 🠅) for visual cues.

Hybrid Approach: Combine Pine Script labels with manual drawings for complex layouts.

Key Resources:
Official Docs:
Text and Shapes Guide 2
Label Style Reference 1

Community Scripts:
Search TradingView for "EMA/SMA Ribbon Pro" or "Stacked Labels" for real-world examples 6.

AI Prompt Tip:

"Generate Pine Script code for a TradingView label with [description]. Use style=label.style_diamond, size=size.large, and dynamic price text."

Common Pitfalls to Fix "Ugly" Charts:
Overlapping Labels: Use label.delete() or label.set_x() with offset logic.

Inconsistent Styling: Define color/size variables upfront (e.g., bullColor = #00FF00).

Static Text: Use str.tostring(close) for real-time price updates.

Unreadable Positions: Anchor to yloc.abovebar if labels obscure candles.


Library "StyleLibrary"
A small library of Pine Script functions that return built-in style variables.

method sizeStyle(size)
  Takes a `string` that returns the corresponding built-in size style variable.
  Namespace types: series string, simple string, input string, const string
  Parameters:
    size (string): A `string` representing a built-in size style: `"Tiny"`, `"Small"`, `"Normal"`, `"Large"`,
`"Huge"`, `"Auto"`.
  Returns: The respective built-in size style variable.

method sizeStyle(size)
  Takes a `sizeStyle` that returns the corresponding built-in size style variable.
  Namespace types: series sizeStyle
  Parameters:
    size (series sizeStyle): A `sizeStyle` representing a built-in size style variable.
  Returns: The respective built-in size style variable.

method lineStyle(style)
  Takes a `string` that returns the corresponding built-in line style variable.
  Namespace types: series string, simple string, input string, const string
  Parameters:
    style (string): A `string` representing a built-in line style: `"Dashed"`, `"Dotted"`, `"Solid"`.
  Returns: The respective built-in line style variable.

method lineStyle(style)
  Takes a `lineStyle` that returns the corresponding built-in line style variable.
  Namespace types: series lineStyle
  Parameters:
    style (series lineStyle): A `lineStyle` representing a built-in line style variable.
  Returns: The respective built-in line style variable.

method labelStyle(style)
  Takes a `string` that returns the corresponding built-in label style variable.
  Namespace types: series string, simple string, input string, const string
  Parameters:
    style (string): A `string` representing a built-in label style:
`"Arrow Down"`, `"Arrow Up"`, `"Circle"`, `"Cross"`, `"Diamond"`, `"Flag"`,
`"Label Center"`, `"Label Down"`, `"Label Left"`, `"Label Lower Left"`,
`"Label Lower Right"`, `"Label Right"`, `"Label Up"`, `"Label Upper Left"`,
`"Label Upper Right"`, `"None"`, `"Square"`, `"Text Outline"`, `"Triangle Down"`,
`"Triangle Up"`, `"XCross"`.
  Returns: The respective built-in label style variable.

method labelStyle(style)
  Takes a `labelStyle` that returns the corresponding built-in label style variable.
  Namespace types: series labelStyle
  Parameters:
    style (series labelStyle): A `labelStyle` representing a built-in label style variable.
  Returns: The respective built-in label style variable.

method fontStyle(font)
  Takes a `string` that returns the corresponding built-in font style variable.
  Namespace types: series string, simple string, input string, const string
  Parameters:
    font (string): A `string` representing a built-in font style: `"Default"`, `"Monospace"`.
  Returns: The respective built-in font style variable.

method positionStyle(position)
  Takes a `string` that returns the corresponding built-in position style variable.
  Namespace types: series string, simple string, input string, const string
  Parameters:
    position (string): A `string` representing a built-in position style:
`"Bottom Center", `"Bottom Left", `"Bottom Right", `"Middle Center", `"Middle Left",
`"Middle Right", `"Top Center", `"Top Left", `"Top Right".
  Returns: The respective built-in position style variable.

method displayStyle(display)
  Takes a `simple string` that returns the corresponding built-in display style variable.
  Namespace types: simple string, input string, const string
  Parameters:
    display (simple string): A `simple string` representing a built-in display style: `"All"`, `"Data Window"`,
`"None"`, `"Pane"`, `"Price Scale"`, `"Status Line"`.
  Returns: The respective built-in display style variable.
Jan 8
Release Notes
v2
6️⃣ Update StyleLibrary for Pine Script™ v6.
🆕 Enumerations for styling the box border, extend, font, position, text align, and text wrap parameters of drawings.
🆕 Function methods for styling with strings or enumerations.
🏫 A "Usage" section at the bottom of the function preview hover with an small example of how to use the library.
🧵 All string parameters are no longer case sensitive.
🧾 A "Used in..." section in the function preview hover to see which built-in functions have parameters that use a given styling function's output.
🪂 Calling a StyleLibrary function using a string method with a misspelt or empty string will fallback on and return the same default styles used in corresponding built-in functions.
📜 Specifying MIT License. StyleLibrary v1 had no license.
🔄 Remove "Style" from the name of all functions.
Mar 1
Release Notes
v3

Added:
method plot(style)
  Takes a const string input and matches a built-in plot style.

Used in the `style` parameter for
[plot()](tradingview.com/pine-script-reference/v6/
  Namespace types: simple plotStyleEnum
  Parameters:
    style (simple plotStyleEnum): A `simple plotStyleEnum` (or input) representing a built-in plot style variable.
  Returns: A built-in `plot` style constant.

**Usage**
```
plotInput = input.enum(sty.plotStyleEnum.solid, 'Plot Style')
newPlotStyle = sty.plot(plotStyleInput)
```
Mar 1
Release Notes
v4

Updated:
method plot(style)
  Takes a const string input and matches a built-in plot style.

Used in the `style` parameter for
[plot()](tradingview.com/pine-script-reference/v6/
  Namespace types: simple plotEnum
  Parameters:
    style (simple plotEnum): A `simple plotEnum` (or input) representing a built-in plot style variable.
  Returns: A built-in `plot` style constant.

**Usage**
```
plotInput = input.enum(sty.plotEnum.solid, 'Plot Style')
newPlotStyle = sty.plot(plotStyleInput)
```
Mar 1
Release Notes
v5

Added:
method plotStyle(style)
  Takes a const string input and matches a built-in plot style.

Used in the `style` parameter for
[plot()](tradingview.com/pine-script-reference/v6/
  Namespace types: simple plotEnum
  Parameters:
    style (simple plotEnum): A `simple plotEnum` (or input) representing a built-in plot style variable.
  Returns: A built-in `plot` style constant.

**Usage**
```
plotInput = input.enum(sty.plotEnum.solid, 'Plot Style')
newPlotStyle = sty.plot(plotStyleInput)
```

Removed:
method plot(style)
  Takes a const string input and matches a built-in plot style.

Used in the `style` parameter for
[plot()](tradingview.com/pine-script-reference/v6/
Mar 30
Release Notes
v6
Replace Size() with sizeConst() (returns a built-in string const size style variable) and sizeNumeric() (returns an int for size parameters and can be used for custom sizing). Also updated textFormat() and added another method to it for bools instead of strings.

method sizeConst(style)
  Takes a `sizeEnum` and returns the corresponding built-in size `const string` style variable.
  Namespace types: series sizeEnum
  Parameters:
    style (series sizeEnum): A `sizeEnum` representing a built-in size style variable.
  Returns: The respective built-in size style variable.

method sizeNumeric(style, sizes)
  Takes a `sizeEnum` and returns an `int` numeric size.
  Namespace types: series sizeEnum
  Parameters:
    style (series sizeEnum): A `sizeEnum` representing a built-in or custom size.
    sizes (sizeNumericType): A `sizeNumericType` that holds custom numeric size `int` values for each of the built-in
sizes and a custom numeric size.
  Returns: An `int` representing a numeric size for `box`, `table`, or `label` text.

sizeNumericType
  A type for custom numeric size values.
  Fields:
    tiny (series int): Numeric size of the tiny size. Using `7` for labels, `8` for boxes and tables is recommended. Default is `8`.
    small (series int): Numeric size of the small size. Using `10` for labels, boxes, and tables is recommended. Default is `10`.
    normal (series int): Numeric size of the normal size. Using `12` for labels, or `14` for boxes and tables is recommended. Default is `14`.
    large (series int): Numeric size of the large size. Using `18` for labels, or `20` for boxes and tables is recommended. Default is `20`.
    huge (series int): Numeric size of the huge size. Using `24` for labels, or `36` for boxes and tables is recommended. Default is `36`.
    auto (series int): Numeric size of the auto size. Using `0` for labels, boxes, and tables is recommended. Default is `0`.
    custom (series int): Numeric size of a custom size. Default is `14`.

Updated:
method textFormat(bold, italic)
  Takes two `simple bool` inputs and returns the corresponding built-in text format style variable.
  Namespace types: simple bool, input bool, const bool
  Parameters:
    bold (simple bool): A `simple bool` representing the `text.format_bold` built-in text format style.
    italic (simple bool): A `simple bool` representing the `text.format_italic` built-in text format style.

Strings are not case sensitive. Default is `''`.
  Returns: The respective built-in text format style variable. When both inputs are `false`,
`textFormat()` returns `text.format_none`.

Removed:
method Size(style)
May 14
Release Notes
v7

Both plotStyle() and textFormat() functions and their methods are commented out until a future update. The plot(style) parameter not requires an input plot_style value and text_formatting parameters now require a const text_format value, which these functions cannot return. The enum plotEnum was kept since it can still be used when creating input plot_style variables.

Removed:
method plotStyle(style)

method textFormat(bold, italic)
May 20
Release Notes
v8

New textFormat() function with three methods for formatting text of labels, boxes, and table cells.

Added:
method textFormat(formatLabel, bold, italic)
  Takes two `simple bool` inputs and applies the corresponding built-in text format style to a `label`.
  Namespace types: series table
  Parameters:
    formatLabel (label): The 'label' to apply the new text formatting onto.
    bold (simple bool): A `simple bool` representing the `text.format_bold` built-in text format style.
    italic (simple bool): A `simple bool` representing the `text.format_italic` built-in text format style.
  Returns: Applies the respective built-in text format style. When both inputs are `false`,
`textFormat()` returns `text.format_none`.

method textFormat(formatBox, bold, italic)
  Takes two `simple bool` inputs and applies the corresponding built-in text format style to a `box`.
  Namespace types: series table
  Parameters:
    formatLabel (label): The 'box' to apply the new text formatting onto.
    bold (simple bool): A `simple bool` representing the `text.format_bold` built-in text format style.
    italic (simple bool): A `simple bool` representing the `text.format_italic` built-in text format style.
  Returns: Applies the respective built-in text format style. When both inputs are `false`,
`textFormat()` returns `text.format_none`.

method textFormat(formatTable, column, row, bold, italic)
  Takes two `simple bool` inputs and applies the corresponding built-in text format style to a cell in a `table`.
  Namespace types: series table
  Parameters:
    formatTable (table): The 'table' to apply the new text formatting onto.
    column (int): A `series int` to select the zero-indexed column of the table cell to apply text formatting onto.
    row (int): A `series int` to select the zero-indexed row of the table cell to apply text formatting onto.
    bold (simple bool): A `simple bool` representing the `text.format_bold` built-in text format style.
    italic (simple bool): A `simple bool` representing the `text.format_italic` built-in text format style.
  Returns: Applies the respective built-in text format style. When both inputs are `false`,
`textFormat()` returns `text.format_none`.