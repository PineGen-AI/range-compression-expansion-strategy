# Range Compression → Expansion Strategy

## What This Strategy Focuses On

Markets don’t move randomly all the time. They often alternate between quiet phases (low volatility, tight candles) and expansion phases (strong directional moves).

This strategy is built around that idea. It identifies periods where price is compressed into a tight range and waits for a confirmed breakout before entering a trade.

## How It Works

### 1. Detecting Compression

The script measures volatility using ATR.

When ATR drops below its recent average, the market is considered compressed

This usually means price is building up for a move

Instead of relying on indicators like Bollinger Bands, this uses pure volatility contraction + price structure

### 2. Defining the Range

During compression, the script tracks:

Highest high (resistance)

Lowest low (support)

This creates a clear structure zone.

### 3. Expansion Breakout

Trades are triggered when price breaks out of that range:

Break above range → potential bullish expansion

Break below range → potential bearish expansion

Only confirmed breakouts are considered.

### 4. Risk Management

The strategy uses ATR for exits:

Stop-loss adapts to volatility

Take-profit is based on a fixed risk-to-reward ratio

This keeps behavior consistent across different markets.

## Key Characteristics

Focuses on volatility shifts (calm → expansion)

Uses structure instead of indicator-heavy logic

Works on crypto, forex, and stocks

Designed for testing and refinement

## When It May Work Better

Before strong trending moves

During breakout phases

After long consolidation periods

It may produce fewer signals in already trending markets.

## Important Note

This script is for testing and educational use. Results depend on market conditions and settings. Always test across multiple assets and timeframes.

## If you want to push this further

You can upgrade this idea by adding:

trend filter (EMA or HTF bias)

volume spike confirmation

session-based filtering

fake breakout detection

## Automate It with PineGen AI

Take this strategy to the next level with PineGen AI automation:

Website: https://www.pinegen.ai/

Twitter: https://x.com/PineGenAI

Telegram Channel: https://t.me/PineGenAI

YouTube Channel: https://www.youtube.com/@pinegenai

## Pine Script v6 Strategy Code

```pine
//@version=6
strategy("Range Compression → Expansion Strategy", overlay=true, initial_capital=100000, default_qty_type=strategy.percent_of_equity, default_qty_value=5, commission_type=strategy.commission.percent, commission_value=0.05, slippage=1)

// ───── INPUTS ─────
lookback      = input.int(20, "Range Lookback")
atrLen        = input.int(14, "ATR Length")
compressionTh = input.float(0.7, "Compression Threshold (ATR Ratio)")
atrMult       = input.float(1.5, "Stop ATR Multiplier")
rr            = input.float(2.0, "Risk Reward")

// ───── VOLATILITY ─────
atrVal = ta.atr(atrLen)
atrAvg = ta.sma(atrVal, lookback)

// Compression condition
isCompressed = atrVal < atrAvg * compressionTh

// ───── RANGE STRUCTURE ─────
rangeHigh = ta.highest(high, lookback)
rangeLow  = ta.lowest(low, lookback)

// ───── BREAKOUT LOGIC ─────
breakUp   = ta.crossover(close, rangeHigh[1])
breakDown = ta.crossunder(close, rangeLow[1])

// Entry conditions
longCondition  = isCompressed[1] and breakUp
shortCondition = isCompressed[1] and breakDown

if longCondition and strategy.position_size == 0
    strategy.entry("Long", strategy.long)

if shortCondition and strategy.position_size == 0
    strategy.entry("Short", strategy.short)

// ───── RISK MANAGEMENT ─────
longStop   = strategy.position_avg_price - atrVal * atrMult
longTarget = strategy.position_avg_price + atrVal * atrMult * rr

shortStop   = strategy.position_avg_price + atrVal * atrMult
shortTarget = strategy.position_avg_price - atrVal * atrMult * rr

strategy.exit("Exit Long", from_entry="Long", stop=longStop, limit=longTarget)
strategy.exit("Exit Short", from_entry="Short", stop=shortStop, limit=shortTarget)

// ───── VISUALS ─────
plot(rangeHigh, title="Range High", color=color.green)
plot(rangeLow, title="Range Low", color=color.red)
```
