# JCO Swings Trend HTF

Pine Script indicator for TradingView that detects swing highs and lows on a higher timeframe (HTF) and determines the trend direction based on swing structure.

## Features

- **Trend Detection**: Identifies Bullish/Bearish trends with Momentum or Compression status
- **CHoCH Detection**: Change of Character detection for potential trend reversals
- **Liquidity Sweep Detection**: Analyzes 5 swings to identify institutional stop hunting patterns
- **Swing Alternation**: Enforces High-Low-High-Low sequence for clean swing structure
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
- **Dashboard Border Width**: Border thickness (0 = no borders)

## Trend Logic

### Primary Analysis
- **Bullish**: Higher lows structure (LL3 < LL2 < LL1)
- **Bearish**: Lower highs structure (HH3 > HH2 > HH1)

### Status
- **Momentum**: Both highs and lows confirm the trend direction
- **Compression**: Primary structure intact but opposite swings are blocked

## CHoCH (Change of Character)

Detects potential trend reversals:

- **Bullish CHoCH**: Previous bearish structure (lower highs) broken by a higher high with candle close above
- **Bearish CHoCH**: Previous bullish structure (higher lows) broken by a lower low with candle close below

## Liquidity Sweep

Identifies institutional stop hunting when an intermediate swing breaks at least 2 other swing levels within 5 swings.

## Installation

1. Open TradingView and go to Pine Editor
2. Copy the indicator code
3. Click "Add to Chart"

## License

This project is licensed under the Mozilla Public License 2.0 - see the [LICENSE](https://mozilla.org/MPL/2.0/) for details.

## Author

- **jcornier** - [GitHub](https://github.com/jcornierfra)
