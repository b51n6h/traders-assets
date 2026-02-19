//@version=6
indicator("TTM Squeeze + Supertrend Pro", shorttitle="Sqz_Super_Pro", overlay=true)

// ==========================================
// 1. INPUTS - Supertrend (Trend Filter)
// ==========================================
grp_st = "Supertrend Settings"
st_atr_len = input.int(10, "Supertrend ATR Length", minval=1, group=grp_st)
st_mult    = input.float(3.0, "Supertrend Multiplier", minval=0.1, step=0.1, group=grp_st, tooltip="3.0 is standard; 2.0 is more aggressive for scalping.")

// ==========================================
// 2. INPUTS - TTM Squeeze
// ==========================================
grp_sqz = "TTM Squeeze Settings"
lengthBB = input.int(14, "Squeeze Length", group=grp_sqz)
multKC   = input.float(1.2, "Keltner Multiplier", group=grp_sqz)
lengthMom = input.int(12, "Momentum Length", group=grp_sqz)

// ==========================================
// 3. CALCULATIONS - Supertrend
// ==========================================
[supertrend, direction] = ta.supertrend(st_mult, st_atr_len)

// Visuals for Supertrend
bodyMiddle = plot(supertrend, "Supertrend Line", color = direction < 0 ? color.new(color.blue, 0) : color.new(color.white, 0), linewidth=2)
fill_color = direction < 0 ? color.new(color.blue, 90) : color.new(color.white, 90)
fill(bodyMiddle, plot(close, display=display.none), fill_color, "Trend Cloud")

// ==========================================
// 4. CALCULATIONS - TTM Squeeze (On Pane)
// ==========================================
// Bollinger / Keltner Squeeze
basis = ta.sma(close, lengthBB)
dev   = 2.0 * ta.stdev(close, lengthBB)
upperBB = basis + dev
lowerBB = basis - dev

ma = ta.sma(close, lengthBB)
rangema = ta.sma(ta.tr, lengthBB)
upperKC = ma + rangema * multKC
lowerKC = ma - rangema * multKC

sqzOn  = (lowerBB > lowerKC) and (upperBB < upperKC)
sqzOff = (lowerBB < lowerKC) and (upperBB > upperKC)

// Momentum
highestHigh = ta.highest(high, lengthMom)
lowestLow   = ta.lowest(low, lengthMom)
avgPrice    = math.avg(math.avg(highestHigh, lowestLow), ta.sma(close, lengthMom))
momVal      = ta.linreg(close - avgPrice, lengthMom, 0)

// ==========================================
// 5. SIGNALS & OVERLAY VISUALS
// ==========================================
// Trend Alignment Logic
is_bullish = direction < 0 and momVal > 0
is_bearish = direction > 0 and momVal < 0

// Plot Entry Shapes on Chart
plotshape(is_bullish and sqzOff and not sqzOff[1], "Long Fire", shape.triangleup, location.belowbar, color.blue, size=size.small, text="Sqz Long")
plotshape(is_bearish and sqzOff and not sqzOff[1], "Short Fire", shape.triangledown, location.abovebar, color.white, size=size.small, text="Sqz Short")

// ==========================================
// 6. SQUEEZE PANE (Force to separate pane)
// ==========================================
// Note: Since main script is 'overlay=true', we plot momentum on chart using a relative scale 
// or you can add this as a second indicator. 
// For better utility, I have added the Squeeze state as a BARCOLOR.
barcolor(is_bullish ? color.new(color.blue, 50) : is_bearish ? color.new(color.white, 50) : na)
