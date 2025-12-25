# Cointegration-Based Pairs Trading on NIFTY 50

Market-neutral pairs trading strategy on NIFTY 50 stocks using Engle–Granger cointegration tests, OLS hedge ratios, and z-score based mean-reversion signals. The notebook walks through the full pipeline from data download to backtesting and risk analysis.

## 1. Project Overview

Pairs trading exploits temporary divergences between two **cointegrated** assets. When their price spread deviates significantly from its long-run equilibrium, we take a long/short position betting on convergence.

This project:

- Selects cointegrated pairs from liquid NIFTY 50 stocks
- Builds an OLS-based hedge ratio and mean-reverting spread
- Generates z-score trading signals (±2 entry, ±0.5 exit)
- Backtests the strategy with realistic transaction costs
- Compares performance vs a NIFTY 50 buy-and-hold benchmark

## 2. Data

- **Universe:** 30 liquid NIFTY 50 stocks (Yahoo Finance tickers `*.NS`)
- **Frequency:** Daily, adjusted close prices
- **Period:** 2018-01-01 to 2025-12-01
- **Source:** `yfinance` (Yahoo Finance API)

Basic cleaning:

- Drop stocks with >5% missing data
- Forward-fill occasional missing prices

## 3. Methodology

1. **Cointegration screening**

   - Run Engle-Granger test on all stock pairs (435 combinations)
   - Keep pairs with p-value < 0.05 and reasonable hedge ratios |β| ∈ [0.5, 2.0]
   - Select the best pair by lowest p-value

2. **Spread & z-score**

   - Fit OLS: `log(P1) = α + β log(P2) + ε`
   - Define spread: `spread = log(P1) − β log(P2)`
   - Compute rolling z-score of the spread

3. **Trading rules**

   - Enter **short spread** when z > +2 (short P1, long β×P2)
   - Enter **long spread** when z < −2 (long P1, short β×P2)
   - Exit when |z| < 0.5
   - Position sizing: volatility-adjusted with a 1% daily risk target

4. **Backtesting & costs**

   - Daily PnL from changes in spread × position size
   - Transaction costs: 0.1% round-trip per trade
   - Benchmark: NIFTY 50 buy-and-hold over the same period

## 4. Results

**Backtest summary (2018–2025):**

- Total return (strategy): **105.4%**
- Total return (NIFTY 50): **251.1%**
- Annualized return: **0.68%**
- Annualized volatility: **1.35%**
- Sharpe ratio: **0.51**
- Max drawdown: **−2.23%**
- Win rate: **52.9%**
- Total trades: **4** (high-conviction mean-reversion trades)

The strategy behaves as a **low-volatility, market-neutral stat arb strategy** rather than trying to outperform long-only equity. It maintains very small drawdowns and a modest positive Sharpe.

### Key visualizations

- **Spread Z-Score and Trading Bands** (`plots/spread_zscore_bands.png`)  
- **Pairs Strategy vs NIFTY 50** (`plots/strategy_vs_nifty.png`)  
- **Strategy Drawdown** (`plots/drawdown.png`)  
- **Trading Position Over Time** (`plots/position_timeline.png`)  
- **Daily PnL Distribution** (`plots/daily_pnl_hist.png`)

## 5. Repository Structure

- plots\
- src\ main jupyter notebook
- .gitignore
- readme.md
- requirements.txt

