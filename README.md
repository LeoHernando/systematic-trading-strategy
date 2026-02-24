# MACD-RSI-EMA Trading Strategy

A systematic quantitative trading strategy that combines three technical indicators — **MACD**, **RSI**, and **EMA** — to capitalise on trend reversals and momentum shifts. The strategy dynamically sizes positions based on signal strength and was backtested on the top 5 S&P 500 stocks by market capitalisation (NVDA, AAPL, MSFT, AMZN, GOOGL), benchmarked against the S&P 500 index.

---

## Strategy Logic

### Entry Rules

| Signal | Condition |
|---|---|
| **Long** | MACD Line > Signal Line (bullish crossover) **AND** Price > EMA (uptrend confirmation) |
| **Short** | MACD Line < Signal Line (bearish crossover) **AND** Price < EMA (downtrend confirmation) |

### Position Sizing

Position sizing adapts to RSI conditions to reflect conviction strength:

- **RSI < 30** (oversold) → Allocate 50% of capital to long position
- **RSI > 70** (overbought) → Allocate 50% of capital to short position
- **30 ≤ RSI ≤ 70** → Equal capital split across all active positions

---

## Project Structure

```
├── MACD-RSI-EMA-strategy.ipynb   # Main notebook with strategy, backtesting & optimisation
```

### Key Components

- **`Common_Class`** — Base class handling data ingestion (via `yfinance`), order execution (buy/sell/short), trade logging, and performance analytics.
- **`RSI_MACD_EMA_Strategy`** — Inherits from `Common_Class`, implements the signal logic and `run_strategy()` method.
- **`optimise_parameters()`** — Grid search over MACD fast/slow/signal periods and EMA window to find the highest Sharpe Ratio configuration on the training set.

---

## Backtesting Setup

| Parameter | Value |
|---|---|
| Initial Capital | $500,000 |
| Training Period | 2010-01-01 → 2016-12-31 |
| Testing Period | 2017-01-01 → 2019-12-31 |
| Universe | NVDA, AAPL, MSFT, AMZN, GOOGL |
| Benchmark | S&P 500 (`^GSPC`) |
| Data Interval | Daily (`1d`) |
| Transaction Cost | 0 (configurable) |

### Default Indicator Parameters

| Indicator | Parameter | Default |
|---|---|---|
| MACD | Fast EMA | 12 |
| MACD | Slow EMA | 26 |
| MACD | Signal | 9 |
| EMA | Window | 200 |
| RSI | Window | 14 |

### Grid Search Ranges

| Parameter | Values Tested |
|---|---|
| MACD Fast | 7, 9, 12, 15 |
| MACD Slow | 14, 26, 35, 40 |
| MACD Signal | 5, 9, 11, 15 |
| EMA Window | 50, 100, 200 |

---

## Performance Metrics

The `performance()` method computes the following on both training and test sets:

- **Annualised Returns** (regular & log)
- **Annualised Volatility**
- **Sharpe Ratio**
- **Sortino Ratio**
- **Maximum Drawdown**
- **Total Trades Executed**
- **Return on Investment (%)**

### Visualisations

The `performance_plots()` method generates a 5-panel dashboard:

1. Cumulative log returns of individual stocks
2. Histogram of strategy returns
3. Strategy vs. S&P 500 benchmark return curve
4. Drawdown chart
5. Equity curve (in $)

---

## Installation

```bash
pip install ta yfinance pandas numpy matplotlib
```

---

## Usage

```python
# Define your stock universe (last symbol must be the benchmark index)
stock_symbols = ["NVDA", "AAPL", "MSFT", "AMZN", "GOOGL", "^GSPC"]

# Run with default parameters
strategy = RSI_MACD_EMA_Strategy(
    symbol=stock_symbols,
    start="2010-01-01",
    end="2016-12-31",
    backtrack=200,
    interval="1d",
    capital=500000,
    transaction_cost=0,
    verbose=True
)
strategy.run_strategy(macd_fast=12, macd_slow=26, macd_signal=9, ema_window=200)
strategy.performance()
strategy.performance_plots()
```

```python
# Optimise parameters via grid search
best_params = optimise_parameters(
    symbols=stock_symbols,
    start="2010-01-01",
    end="2016-12-31",
    initial_capital=500000,
    transaction_cost=0,
    macd_fast_range=[7, 9, 12, 15],
    macd_slow_range=[14, 26, 35, 40],
    macd_signal_range=[5, 9, 11, 15],
    ema_window_range=[50, 100, 200]
)
```

---

## Dependencies

| Library | Purpose |
|---|---|
| `ta` | Technical indicator computation (MACD, RSI, EMA) |
| `yfinance` | Historical OHLCV data retrieval |
| `pandas` / `numpy` | Data manipulation & return calculations |
| `matplotlib` | Performance visualisation |

---

## Disclaimer

This project is for educational and research purposes only. Past backtested performance does not guarantee future results. This is not financial advice.
