# Quantitative Trading Strategies

This repository collects a set of quantitative trading strategies I implemented as
coursework for a graduate **Quantitative Trading Strategies** class (FINM 33150).
Each assignment asked us to design, implement, and empirically evaluate a distinct
trading strategy end to end — from raw market/fundamental data through signal
construction, backtesting, transaction-cost modeling, and performance diagnostics
(returns, Sharpe ratio, drawdown, robustness checks).

Each folder contains a single Jupyter notebook implementing one strategy, with all
analysis cells already executed and their outputs (tables and figures) saved inline,
so the results can be reviewed without re-running. The underlying raw datasets and
intermediate caches have been removed to keep the repository lightweight; the
notebooks document the data sources they were built from.

## Notebooks

### `CDS/` — CDS Residual-Return Predictive Regression
**`CDS_Residual_Return_Predictive_Regression.ipynb`**

A two-stage predictive regression on weekly credit-default-swap (CDS) spread
returns. First, contemporaneous regressions hedge each issuer's CDS return against
its equity return and the market, producing residual ("idiosyncratic") CDS returns.
Second, the strategy tests whether *lagged* residual equity returns predict
*next-week* residual CDS returns, comparing equal-weighted (boxcar) versus
exponentially-discounted predictive fits. Returns are sampled Wednesday-to-Wednesday
to mitigate stale daily CDS quotes. The strategy exploits lead–lag information flow
between the equity and credit markets.

### `Crypto/` — BTC Trade-Flow Signal
**`BTC_TradeFlow_Signal.ipynb`**

A short-horizon microstructure strategy on BTC-USDT trade data. It builds a signed
trade-flow signal — net buy/sell volume over a trailing window — and tests whether it
predicts forward returns. A through-origin regression is fit on a per-exchange
training split (first 40%), a trading threshold is calibrated to a target
participation rate, and a thresholded rule is traded out of sample (last 60%).
P&L is evaluated both round-trip and on a cumulative-inventory basis, with and
without transaction costs, plus volatility-conditioning and participation-rate
robustness checks. The strategy is a classic order-flow / price-impact bet.

### `ETH_USDT_export/` — Cross-Exchange Crypto Relative Value
**`Crypto_CrossExchange_RelativeValue.ipynb`**

A relative-value (statistical-arbitrage) strategy trading the spread between the same
crypto asset quoted on three exchanges (Binance, Coinbase, OKX) using one-second
trade prices. It constructs synchronized one-second series, defines a shifted spread
via an exponential moving average, and generates entry/exit signals from short- and
long-window persistence statistics. The backtest sweeps several parameter cases and
transaction-cost (zeta) levels, reporting total return, maximum drawdown, and Sharpe
on one-minute equity returns. The strategy harvests transient mispricings between
venues that revert as prices re-synchronize.

### `Carry_Trade/` — FX Carry Strategy
**`FX_Carry_Strategy.ipynb`** (main strategy)

A weekly emerging-market FX carry strategy: lend in higher-yielding EM currencies
funded by borrowing in GBP (UK OIS). Using EM swap curves, UK OIS funding rates
(Bank of England `IUDSOIA`), and spot FX normalized to USD-per-local-currency, it
aligns all series on a weekly Wednesday schedule, computes the lending- and
funding-leg P&L per currency, and applies a no-position rule at entry based on the
5Y spread condition. Performance is summarized with return/Sharpe/drawdown statistics
and diagnostic plots. The strategy captures the interest-rate differential (carry)
between currencies.

Supporting data-acquisition / curve-construction notebooks (kept for reproducibility
of the inputs):
- **`Bank_of_England_Data_Fetch.ipynb`** — pulls UK OIS and FX rates from the
  Bank of England statistical database API.
- **`Nasdaq_Daily_USEq_Price_Fetch.ipynb`** — pulls daily raw/adjusted equity prices
  via the Nasdaq Data Link API.
- **`Zero_And_Spot_Curves.ipynb`** — bootstraps zero-coupon curves from spot/swap
  curves for correct discounting of cashflows.

### `ZacksFundamentalsB/` — Fundamental-Ratio Quantile Long–Short
**`Fundamental_Ratio_Quantile_LongShort.ipynb`**

A cross-sectional equity strategy on fundamental data. It builds an investable
universe (Jan 2018–Jun 2023) with price-coverage, market-value, leverage, and sector
filters, then constructs three financial ratios from Zacks fundamentals. It evaluates
quantile long–short portfolios — buying the top quantile and shorting the bottom —
on both ratio *levels* and ratio *changes*, at monthly and weekly rebalance
frequencies. The strategy is a factor/anomaly bet on fundamental signals predicting
the cross-section of returns.

## Notes
- Author: Malank Goel
- All notebooks retain their executed outputs; raw data was intentionally removed.
- Several notebooks originally relied on subscription/API data (Nasdaq Data Link,
  Bank of England) or large tick datasets, so they are not re-runnable as-is without
  re-provisioning those sources.
