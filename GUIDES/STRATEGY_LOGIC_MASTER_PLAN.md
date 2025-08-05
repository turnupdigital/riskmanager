# STRATEGY LOGIC MASTER PLAN
## EZ ALGO HEDGE FUND - COMPLETE BOOLEAN LOGIC & FILTER CHAIN DOCUMENTATION

> **PURPOSE**: This is the definitive reference for how the strategy makes trading decisions. Use this to understand, debug, and explain the complete logical flow to future AI models.

---

## 🔍 **PROGRAMMING TERMS & CONCEPTS**

### **1. BOOLEAN LOGIC / BOOLEAN ALGEBRA**
- **TRUE/FALSE values only** - No maybe, no partial, no gray area
- **Binary decisions** - 1 or 0, on or off, allow or block
- **Logical operations** - AND, OR, NOT combining true/false values

### **2. CONDITIONAL STATEMENTS / PREDICATES**
- **IF/THEN/ELSE logic** - "IF condition is true, THEN do action"
- **Predicates** - Functions that return true/false (e.g., `sigCountLong > 0`)
- **Guards** - Conditions that must pass before proceeding

### **3. LOGICAL GATES (Computer Science)**
- **AND Gate** - ALL inputs must be TRUE for output to be TRUE
- **OR Gate** - ANY input can be TRUE for output to be TRUE  
- **NOT Gate** - Inverts the input (TRUE becomes FALSE)

### **4. FILTER CHAIN / DECISION TREE**
- **Sequential filtering** - Each step eliminates possibilities
- **Cascading logic** - Output of one step becomes input of next
- **Early termination** - First FALSE stops the entire chain

---

## 🎯 **COMPLETE LONG ENTRY LOGIC FLOW**

### **STEP 1: SIGNAL GENERATION (OR GATE)**
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

HUMAN INPUT: Okay so what's going to end up happening in this section and and I'm not sure how far removed we are from this what I'm looking for is the signal comes in from the buy / sell indicator and it gets past along as long as it gets through some filters. I'm almost sure that that's what's going on in this section with this the Boolean variables but if it's not then you got to let me know.I just don't want things to get over complicated I know I know you know how to do a lot of fancy things with code but this one essentially is just a pass through but what is passing through is a set of filters.

DECISION GATE:
IF sigCountLong > 0 THEN proceed to STEP 2
ELSE block trade (no signals active)
```

### **STEP 2: STEP CHANNEL FILTER (AND GATE)**
```
BOOLEAN VARIABLES:
├── stepChannelEnable (user setting)
├── stepChannelLongOK (channel permission)
├── allowLongs (master permission)
├── stepChannelLower (calculated boundary)
└── stepChannelState ("Range", "Momentum Up", "Momentum Down")

LOGICAL CONDITIONS:
├── stepChannelLongOK = NOT stepChannelEnable OR (close > stepChannelLower)
└── allowLongs = stepChannelLongOK

DECISION GATE:
IF allowLongs = TRUE THEN proceed to STEP 3
ELSE block trade (Step Channel says NO)

HUMAN EXPLANATION:
- If Step Channel disabled → always allow
- If Step Channel enabled → only allow when price above lower boundary
- Yellow candles (Range) → blocks trades
- Green candles (Momentum Up) → allows long trades

HUMAN INPUT: Okay so this section is a very big concern because this is completely wrong. I don't feel like I'm being heard regarding the step channel the AI coding agents just keep doing whatever they feel like doing it's getting really frustrating especially with regard to the step channel because I've had this conversation multiple times I am have a grateful that we found this new way of communicating that allows me to see all of the logic and now now the pr.oblems are obvious The step channel being one of the major problems. 

Okay so first of all we are using it in completely the wrong way or not in the way that I intended it and we have to remember that this is my strategy I'm not doing whatever garbage generative AI comes up with cuz generative AI comes up with generally very very bad ideas and every now and then you'll come up with something Brilliant. 

Okay so first of all the step channel doesn't block all trades there's no setting in which will it will block all trades the step channel is for identifying trend versus scalp mode.... And scout mode in trend mode the differences the exit type and the exit strategy. The step channel is our secret sauce for determining when the best times to get out quickly with our 70 to 80% when rate strategy. Versus the times where it would be crazy to get out with a small scout profit because price is clearly going in our direction.

The way this is worded it seems like you're blocking all trades because of some setting in the step channel and that's not what the step channel is supposed to be doing the step channel is identifying scalp mode and it's identifying trend mode so it's just controlling the exit type. That's the primary thing that it's doing as a secondary option we can also use it for directional bias but this is an additional option not as primary intention it's primary intention is to identify when is the time to scout versus when is a time to hold on and ride the Trend That's it no directional bias it can go long if prices below the channel it can go short if prices above the channel I don't need your fucking help, Stop making decisions about my strategy for me. I just need you to do what I ask.

So the step channel needs to have two options turn on step channel if step channel is turned on which it should be turned on by default it's telling the system now's the time to scout okay hold on for the trend now it's time to scout okay hold on for the trend.

And as a separate option used for directional bias as an option that's off by default because we have an entire section dedicated to directional bias

So clearly this logic has to be completely redone then we're going to be doing it in in the new MD file in which we're planning out all the logic you're not going to be making any changes until I approve the plan so it has to be a separate file because this is the current and then the new file we will see the future

So indirectional bias so if the directional bias mode is on an active it's going to block one side it's either going to block shorts or it's going to block longs at no point is it blocking all trades and that's one big concern that I have with the logic in this section is because it seems that it's blocking all trades at some point
```






### **STEP 3: DIRECTIONAL BIAS FILTER (COMPLEX AND GATE)**
```
BOOLEAN VARIABLES:
├── directionalBiasEnable (user setting)
├── hullEnable, trendStrengthEnable, quadrantEnable, adaptiveSTEnable, volumaticEnable, smoothHAEnable
├── hullBullish, trendStrengthBullish, quadrantBullish, adaptiveSTBullish, volumaticBullish, smoothHABullish
├── enabledFilters (count of enabled bias indicators)
├── bullishVotes (count of bullish indicators)
├── bearishVotes (count of bearish indicators)
├── biasConfluence ("Any", "Majority", "All")
└── longDirectionalBias (final bias decision)

LOGICAL CONDITIONS:
├── enabledFilters = (hullEnable ? 1 : 0) + (trendStrengthEnable ? 1 : 0) + ... + (smoothHAEnable ? 1 : 0)
├── bullishVotes = (hullEnable AND hullBullish ? 1 : 0) + (trendStrengthEnable AND trendStrengthBullish ? 1 : 0) + ...
├── bearishVotes = (hullEnable AND NOT hullBullish ? 1 : 0) + (trendStrengthEnable AND NOT trendStrengthBullish ? 1 : 0) + ...
└── longDirectionalBias = NOT directionalBiasEnable OR enabledFilters = 0 OR
    (biasConfluence = "Any" AND bullishVotes > 0) OR
    (biasConfluence = "Majority" AND bullishVotes > bearishVotes) OR
    (biasConfluence = "All" AND bearishVotes = 0)

DECISION GATE:
IF longDirectionalBias = TRUE THEN proceed to STEP 4
ELSE block trade (Directional Bias says NO)

HUMAN EXPLANATION:
- If Directional Bias disabled → always allow
- If enabled → depends on confluence mode:
  * "Any" → at least 1 bullish indicator required
  * "Majority" → more bullish than bearish indicators required
  * "All" → NO bearish indicators allowed (most restrictive)

HUMAN INPUT: The strategy currently is not really taking trade so I cannot verify 100% that it's working and all this stuff seems like a bunch of gibberish. But directional bias should be pretty straightforward easy to understand.

So what what we're connecting is a lot of trend indicators and the trend indicator say okay we're in an uptrend okay we're in a downtrend and they're all pretty much you know pretty close to being about the same in terms of when the market is up trending some of them hold on for a little longer before changing the trend others change the trend they're a little bit more sensitive and you can adjust the sensitivity but also how they're drawing the chart we can use it for other things other than identifying the trend so for example we're going to be using the smooth hike and Ashley and the whole suite for exits whenever the candles start touching those areas.

In this section which is directional bias we're just looking to block one side of the trait we're either looking to block the longs or block the short we're only going to choose to scout trend whatever we're only going to do it in the direction that the the trend indicators that we choose say price is: So if the indicator says that we're in an uptrend and we're only taking longs if they indicator says that we're in a downtrend then we're only taking shorts

Very straightforward now this section is a little tricky for me to debug because as of yet I do not have a way to verify that the indicator is actually connected it would be great to have a little window I've just been scared to ask for it because of the added complexity so it'd be cool to have like a little window or a little table that would let me know when an indicator one of the trend indicators is connected and then show me whether that indicator is long or short it's based on the last signal so it should be able to look like even if I if I connect it if I load up the chart when we're mid trend I still want my strategy to know what the last signal was so I don't want to load it up on the chart and then have to wait for a trend change in order to be sort of in sync with what's going on in the market now I don't know if that's what's happening it's kind of hard to say but it kind of feels like it is and there's no way for really for me to verify it because I have no way to confirm verify 100% that the indicator is connected but yeah I mean this I see in quotations that complex and gate I don't know what that means but I don't really feel like this is too complex you know you look hate is it green or red is this indicator green or red it's green okay we're only taking long we block all shorts that's it

Also we have to remove the the option because it just doesn't make any sense it's either has to be all or majority if we do any then we're just you know there's going to be some periods where we don't block anything and I guess I mean that doesn't make any sense to me so we do either any I mean I'm sorry we do either all or majority that's that's the option in the drop-down I mean for like how the confluence is created now I'm hoping that the confluence logic isn't too complex because it really doesn't need to be you know we have three then it has to be two out of three or all three



```

### **STEP 4: CVD FILTER (AND GATE)**
```
BOOLEAN VARIABLES:
├── cvdEnable (user setting)
├── cvdTrendSrc (external indicator connection)
├── cvdTrendMode (final CVD decision)
└── cvdValue (CVD numerical value)

LOGICAL CONDITIONS:
├── cvdTrendMode = NOT cvdEnable OR (cvdTrendSrc > 0.5)
└── (cvdTrendSrc comes from external "CVD Strategy Export" indicator)

DECISION GATE:
IF cvdTrendMode = TRUE THEN proceed to STEP 5
ELSE block trade (CVD says NO - insufficient volume delta)

HUMAN EXPLANATION:
- If CVD disabled → always allow
- If CVD enabled → external indicator must signal "trend mode allowed"
- CVD indicator checks if |volume delta| > threshold
- If volume delta too low → forces scalp mode → blocks trend entries
```

### **STEP 5: FINAL CALCULATION (AND GATE)**
```
BOOLEAN VARIABLES:
├── baseLongWithBias (calculated result)
├── longEntrySignal (final entry trigger)
└── finalLongEntry (execution signal)

LOGICAL CONDITIONS:
├── baseLongWithBias = primaryLongSig AND longDirectionalBias AND allowLongs
├── longEntrySignal = baseLongWithBias
└── finalLongEntry = longEntrySignal

DECISION GATE:
IF finalLongEntry = TRUE THEN EXECUTE LONG TRADE
ELSE wait for next bar

HUMAN INPUT: Once again I see that you're trying to force directional bias when they're when it's not supposed to exist do not give the cvd indicator any directional bias this will not have any directional bias this indicator is supposed to be doing one job and one job only and again it's not blocking any trades this is trend mode or scout mode this is controlling the exit type only the exit type it will never have any directional bias so it's not allow long allow short it's allowed trade I mean and that it's not even allowed long or short it's scalp or it's trend and in order for it to be one or the other there are other things that also have to align so this indicator is supposed to be doing its job independently and it can do its job with or without any of the other indicators that were working on but it's only supposed to be identifying one thing and right now it's not identifying the right thing it's saying something about long I mean maybe you have to do both and you have to but I'm only seeing long it's only allowing longs there is no directional bias I even see long directional bias this is not supposed to have any directional bias no directional bias in the cvd the cvd has no directional bias it either allows trend or scalp this is a severe severe problem And it is very far removed from anything that we have ever discussed it needs to be corrected immediately
```

---

## 🔻 **COMPLETE SHORT ENTRY LOGIC FLOW**

### **STEP 1: SIGNAL GENERATION (OR GATE)**
```
BOOLEAN VARIABLES:
├── sig1Short, sig2Short, sig3Short, ..., sig10Short (individual signals)
├── sigCountShort (count of active signals)
└── primaryShortSig (any signal active)

LOGICAL CONDITIONS:
├── sig1Short = signal1Enable AND (signal1ShortSrc ≠ close) AND ta.change(signal1ShortSrc) > 0
├── sig2Short = signal2Enable AND (signal2ShortSrc ≠ close) AND ta.change(signal2ShortSrc) > 0
├── ... (repeat for sig3Short through sig10Short)
├── sigCountShort = (sig1Short ? 1 : 0) + (sig2Short ? 1 : 0) + ... + (sig10Short ? 1 : 0)
└── primaryShortSig = sig1Short OR sig2Short OR sig3Short OR ... OR sig10Short

DECISION GATE:
IF sigCountShort > 0 THEN proceed to STEP 2
ELSE block trade (no signals active)
```

### **STEP 2: STEP CHANNEL FILTER (AND GATE)**
```
BOOLEAN VARIABLES:
├── stepChannelShortOK (channel permission)
└── allowShorts (master permission)

LOGICAL CONDITIONS:
├── stepChannelShortOK = NOT stepChannelEnable OR (close < stepChannelUpper)
└── allowShorts = stepChannelShortOK

DECISION GATE:
IF allowShorts = TRUE THEN proceed to STEP 3
ELSE block trade (Step Channel says NO)

HUMAN EXPLANATION:
- If Step Channel disabled → always allow
- If Step Channel enabled → only allow when price below upper boundary
- Yellow candles (Range) → blocks trades
- Red candles (Momentum Down) → allows short trades
```

### **STEP 3: DIRECTIONAL BIAS FILTER (COMPLEX AND GATE)**
```
BOOLEAN VARIABLES:
└── shortDirectionalBias (final bias decision)

LOGICAL CONDITIONS:
└── shortDirectionalBias = NOT directionalBiasEnable OR enabledFilters = 0 OR
    (biasConfluence = "Any" AND bearishVotes > 0) OR
    (biasConfluence = "Majority" AND bearishVotes > bullishVotes) OR
    (biasConfluence = "All" AND bullishVotes = 0)

DECISION GATE:
IF shortDirectionalBias = TRUE THEN proceed to STEP 4
ELSE block trade (Directional Bias says NO)

HUMAN EXPLANATION:
- Same logic as long bias but inverted
- "Any" → at least 1 bearish indicator required
- "Majority" → more bearish than bullish indicators required  
- "All" → NO bullish indicators allowed (most restrictive)
```

### **STEP 4: CVD FILTER (AND GATE)**
```
SAME AS LONG - CVD doesn't distinguish direction
cvdTrendMode applies to both long and short trades
```

### **STEP 5: FINAL CALCULATION (AND GATE)**
```
BOOLEAN VARIABLES:
├── baseShortWithBias (calculated result)
├── shortEntrySignal (final entry trigger)
└── finalShortEntry (execution signal)

LOGICAL CONDITIONS:
├── baseShortWithBias = primaryShortSig AND shortDirectionalBias AND allowShorts
├── shortEntrySignal = baseShortWithBias
└── finalShortEntry = shortEntrySignal

DECISION GATE:
IF finalShortEntry = TRUE THEN EXECUTE SHORT TRADE
ELSE wait for next bar
```

---

## 🚨 **COMPLETE FILTER CHAIN SECURITY CHECKPOINT**

```
TRADE SIGNAL ENTERS SYSTEM
    ↓
[CHECKPOINT 1: SIGNAL GENERATION]
├── Long: sigCountLong > 0? 
├── Short: sigCountShort > 0?
└── IF NO → ⏳ WAITING FOR SIGNALS (block trade)
    ↓ YES
[CHECKPOINT 2: STEP CHANNEL FILTER]  
├── Long: allowLongs = true?
├── Short: allowShorts = true?
└── IF NO → 🚫 BLOCKED: STEP CHANNEL RANGE (block trade)
    ↓ YES
[CHECKPOINT 3: DIRECTIONAL BIAS FILTER]
├── Long: longDirectionalBias = true?
├── Short: shortDirectionalBias = true?
└── IF NO → 🚫 BLOCKED: DIRECTIONAL BIAS (block trade)
    ↓ YES
[CHECKPOINT 4: CVD FILTER]
├── Both: cvdTrendMode = true?
└── IF NO → 🚫 BLOCKED: CVD THRESHOLD (block trade)
    ↓ YES
[CHECKPOINT 5: FINAL CALCULATION]
├── Long: baseLongWithBias = primaryLongSig AND longDirectionalBias AND allowLongs
├── Short: baseShortWithBias = primaryShortSig AND shortDirectionalBias AND allowShorts
└── IF TRUE → ✅ TRADE EXECUTES
```

---

## 🔧 **DEBUGGING QUESTIONS FOR EACH CHECKPOINT**

### **CHECKPOINT 1: SIGNAL GENERATION**
- Are external indicators connected to signal inputs?
- Are signal inputs set to external plot points (not default "close")?
- Are the external indicators actually generating signals?
- What are the current values of sigCountLong and sigCountShort?

### **CHECKPOINT 2: STEP CHANNEL FILTER**
- Is stepChannelEnable = true or false?
- What is the current stepChannelState? ("Range", "Momentum Up", "Momentum Down")
- What are the current values of stepChannelUpper and stepChannelLower?
- What are the current values of allowLongs and allowShorts?

### **CHECKPOINT 3: DIRECTIONAL BIAS FILTER**
- Is directionalBiasEnable = true or false?
- How many bias indicators are enabled? (enabledFilters count)
- What is the biasConfluence setting? ("Any", "Majority", "All")
- What are the current bullishVotes and bearishVotes counts?
- What are the current values of longDirectionalBias and shortDirectionalBias?

### **CHECKPOINT 4: CVD FILTER**
- Is cvdEnable = true or false?
- Is cvdTrendSrc connected to external "CVD Strategy Export" indicator?
- What threshold is set in the CVD indicator?
- What is the current value of cvdTrendMode?
- What is the current cvdValue?

### **CHECKPOINT 5: FINAL CALCULATION**
- What are the current values of baseLongWithBias and baseShortWithBias?
- What are the current values of longEntrySignal and shortEntrySignal?

---

## 🎯 **MOST LIKELY CAUSES OF "3 TRADES IN 3 MONTHS"**

### **RANKED BY PROBABILITY:**

1. **CVD Filter Too Restrictive** (90% likely)
   - CVD threshold set too high
   - cvdTrendMode = false most of the time
   - External CVD indicator not connected properly

2. **Step Channel Always in Range Mode** (70% likely)
   - Step Channel too sensitive (length too low)
   - Market mostly ranging, rarely breaking out
   - stepChannelState = "Range" blocks all trades

3. **Directional Bias Set to "All" Mode** (60% likely)
   - biasConfluence = "All" with multiple indicators enabled
   - Requires ALL indicators to agree (extremely restrictive)
   - One disagreeing indicator blocks all trades

4. **External Indicators Not Connected** (40% likely)
   - Signal sources still set to default "close"
   - External indicators not properly linked
   - No actual signals being generated

---

## 📚 **TECHNICAL VOCABULARY REFERENCE**

- **Boolean Logic** - True/false decision making
- **Filter Chain** - Sequential conditions that must all pass
- **Decision Tree** - Branching logic based on conditions  
- **Logical Gates** - AND/OR/NOT operations
- **Predicates** - Conditions that return true/false
- **Confluence** - Multiple indicators agreeing
- **Gating Logic** - Using conditions to allow/block actions
- **Early Termination** - Stopping at first failed condition
- **Cascading Logic** - Output of one step feeds next step
- **Security Checkpoint** - Each filter validates before proceeding

---

## 🚀 **USAGE INSTRUCTIONS**

**To Debug Trade Blocking:**
1. Check each checkpoint in order
2. Identify which checkpoint is saying "NO"
3. Examine the boolean variables for that checkpoint
4. Adjust settings or fix connections as needed

**To Discuss Logic with AI:**
"Show me the boolean variables and filter chain logic for [specific checkpoint]"

**To Add New Filters:**
1. Insert new checkpoint in appropriate location
2. Define boolean variables and logical conditions
3. Update this master plan documentation
4. Test with temporary bypass first

This document is the **SINGLE SOURCE OF TRUTH** for strategy logic. Keep it updated with any changes!