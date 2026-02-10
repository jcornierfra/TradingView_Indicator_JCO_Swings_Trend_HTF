# JCO Swings Trend HTF

Pine Script indicator for TradingView that detects swing highs and lows on a higher timeframe (HTF) and determines the trend direction based on swing structure.

![JCO Swings Trend HTF Screenshot](screenshot.png?v=1.4)

## Features

- **Trend Detection**: Identifies Bullish/Bearish trends with Momentum or Compression status
- **Trend Reversal Gating**: Requires CHoCH confirmation before accepting a trend reversal
- **Dual CHoCH Detection**: Simultaneous bullish and bearish CHoCH evaluation with liquidity sweep identification
- **CHoCH Detection**: Change of Character detection with previous trend analysis and 5-candle close confirmation
- **Liquidity Sweep Detection**: Identifies liquidity grabs with close price confirmation
- **Swing Alternation**: Enforces High-Low-High-Low sequence with automatic missing swing insertion
- **Multi-Timeframe**: Works on any timeframe while analyzing swings from a higher timeframe

## Dashboard

The indicator displays a compact dashboard with:

| Field | Description |
|-------|-------------|
| TF | Selected swing timeframe |
| Trend | Current trend direction with status (M=Momentum, C=Compression) |
| CHoCH | Change of Character status (Bullish/Bearish/Continuation) |
| Liq Sweep | Liquidity sweep detected (Yes/No) |
| Expansion | Distance between last swing high and low in pips |

## Parameters

### Swings H & L

- **Swing Period**: Number of bars for pivot detection (default: 5)
- **Swing Time Frame**: Higher timeframe for swing analysis (default: 60 min)

### Swings Chart

- **Draw Icons**: Show swing high/low markers on chart
- **Icon Gap (% of price)**: Distance of icons from price level
- **Display Dashboard**: Show/hide the dashboard table
- **Show SH/SL Values**: Display swing high/low price values in dashboard
- **Pip Value ($)**: Value of 1 pip in price units (default: 0.1 for Gold, 0.0001 for Forex majors)
- **Dashboard Border Width**: Border thickness (0 = no borders)

## Trend Logic

Uses the 3 most recent swing highs (SH0, SH1, SH2) and swing lows (SL0, SL1, SL2) to determine the trend.

### Primary Analysis

- **Bullish**: Higher lows structure (SL2 < SL1 < SL0), or sweep recovery (SL2 > SL1 but SL0 > SL2)
- **Bearish**: Lower highs structure (SH2 > SH1 > SH0), or sweep breakdown (SH2 < SH1 but SH0 < SH2)

### Secondary Confirmation

- **Momentum**: Primary structure confirmed by opposite swings (e.g. Bullish + SH0 > SH1)
- **Compression**: Primary structure intact but opposite swings diverge (e.g. Bullish + SH0 < SH1)

### Ambiguous Cases

When the primary structure shows a V-shape correction (e.g. SL2 > SL1 < SL0), the trend is determined by the secondary confirmation (opposite swings). If neither confirms, the trend is **Unclear**.

## Trend Reversal Gating

When the raw trend calculation reverses compared to the previous trend (computed on swings 1, 2, 3), the reversal is **not accepted immediately**. Instead:

1. **With CHoCH liquidity sweep** → the previous trend is restored as **Momentum** and a liquidity sweep is flagged
2. **With CHoCH confirmation** → the reversal is accepted with its raw status (Momentum/Compression)
3. **Without CHoCH** → the indicator checks if the previous trend structure is still valid:
   - **Bullish to Bearish** (no CHoCH): if SH0 > SH3 → stays **Bullish (C)** (compression), otherwise → **Unclear**
   - **Bearish to Bullish** (no CHoCH): if SL0 < SL3 → stays **Bearish (C)** (compression), otherwise → **Unclear**

This prevents premature trend flips caused by temporary swing structure changes that haven't been confirmed by a close-based breakout.

## CHoCH (Change of Character)

Detects potential trend reversals by evaluating the **previous trend** (using swings 1, 2, 3) and checking if swing 0 breaks swing 1 with close confirmation:

- **Bullish CHoCH**: Previous trend was not bullish + higher high (SH0 > SH1) + close above SH1
- **Bearish CHoCH**: Previous trend was not bearish + lower low (SL0 < SL1) + close below SL1

### Dual CHoCH Detection

Both bullish and bearish CHoCH conditions are evaluated simultaneously using **structure-based** checks:

- **Bullish by structure**: SH1 < SH2 (declining highs) + SH0 > SH1 + close above SH1
- **Bearish by structure**: SL1 > SL2 (rising lows) + SL0 < SL1 + close below SL1

When **both** conditions are true at the same time (dual CHoCH), the most recent swing determines which CHoCH wins:

- If the **last swing was a high** → Bullish CHoCH wins
- If the **last swing was a low** → Bearish CHoCH wins

### CHoCH as Liquidity Sweep

When a dual CHoCH occurs and the winning CHoCH matches the **previous trend direction**, the opposing CHoCH is identified as a **liquidity sweep** rather than a true reversal. In this case:

- The previous trend is **restored as Momentum** (the structure is re-confirmed)
- The liquidity sweep flag is set (displayed in the dashboard)

This handles scenarios where price briefly breaks structure in both directions but ultimately continues the existing trend.

### Close Confirmation Window

Close confirmation uses a **5-candle window** (2 before, pivot candle, 2 after) in the swing timeframe. The max close (for bullish) or min close (for bearish) across the window is compared against the previous swing level. This handles **immediate rebalance** scenarios where the pivot candle itself has a long wick and closes back below/above the level.

## Liquidity Sweep

Detects when price takes out liquidity at a swing level before reversing.

### Bullish Trend (analyze lows)

- **Case 1**: SL1 < SL2 AND SL0 > SL1 AND SH0 > SH1 → Price swept SL1 and reversed
- **Case 2**: SL0 < SL1 AND (Close0 > SL1 OR SH0 > SH1) → New low made but rejected

### Bearish Trend (analyze highs)

- **Case 1**: SH1 > SH2 AND SH0 < SH1 AND SL0 < SL1 → Price swept SH1 and reversed
- **Case 2**: SH0 > SH1 AND (Close0 < SH1 OR SL0 < SL1) → New high made but rejected

## Swing Alternation

The indicator enforces a strict High-Low-High-Low alternation. When consecutive swings of the same type are detected (e.g. two highs in a row), the indicator automatically finds and inserts the missing opposite swing between them (lowest low between two highs, or highest high between two lows).

## Changelog

- **v1.0** (2026-02-05): Initial release
- **v1.1** (2026-02-06): CHoCH detection with previous trend analysis and 5-candle close confirmation window
- **v1.2** (2026-02-09): Trend reversal gating (require CHoCH confirmation), "Unclear" displayed in gray
- **v1.3** (2026-02-10): Dual CHoCH detection (simultaneous bullish/bearish evaluation), CHoCH liquidity sweep identification
- **v1.4** (2026-02-10): Configurable pip value parameter (replaces hardcoded mintick * 100)

## Installation

1. Open TradingView and go to Pine Editor
2. Copy the indicator code
3. Click "Add to Chart"

## License

This project is licensed under the Mozilla Public License 2.0 - see the [LICENSE](https://mozilla.org/MPL/2.0/) for details.

## Author

- **Jerome Cornier** - [GitHub](https://github.com/jcornierfra)
