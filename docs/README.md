# QuantStats

Portfolio analytics for quants. A Python library that provides comprehensive performance metrics, risk analysis, drawdown analysis, and report generation for portfolio and strategy evaluation.

## Overview

QuantStats (by Ran Aroussi) is a lightweight, focused library for portfolio performance analysis. It computes 60+ statistical metrics, generates visual reports (HTML and console), supports benchmark comparison, and can extend pandas with financial methods. It is designed for quick, thorough evaluation of trading strategy returns.

## Key Features

- **60+ Performance Metrics**: Sharpe, Sortino, Calmar, Omega, CAGR, VaR, CVaR, drawdown analysis, and many more
- **Benchmark Comparison**: Side-by-side strategy vs benchmark analysis with alpha, beta, R-squared, information ratio
- **HTML Reports**: Full tearsheet-style HTML reports with charts and statistics tables
- **Console Reports**: Terminal-friendly tabular output via tabulate
- **Visualization**: 20+ chart types including returns, drawdowns, monthly heatmaps, rolling metrics, distributions
- **Monte Carlo Simulation**: Path simulation with bust/goal probability analysis
- **Pandas Extension**: Optional `extend_pandas()` adds all metrics as DataFrame/Series methods
- **Data Download**: Built-in Yahoo Finance integration for benchmark data

## Package Structure

```
src/quantstats/
    __init__.py         # Package init, extend_pandas() function
    stats.py            # 60+ statistical functions (Sharpe, Sortino, etc.)
    reports.py          # HTML and console report generation
    plots.py            # Plotting module entry point
    utils.py            # Data preparation, validation, conversions
    _plotting/
        __init__.py
        core.py         # Low-level matplotlib chart rendering
        wrappers.py     # High-level plotting API (20+ chart functions)
    _montecarlo.py      # Monte Carlo simulation engine
    _compat.py          # Compatibility layer for pandas/yfinance versions
    _numpy_compat.py    # NumPy version compatibility
    version.py          # Version string
```

## Quick Start

This example creates a synthetic returns series and computes key performance metrics entirely offline -- no API keys or data downloads required.

```python
import numpy as np
import pandas as pd
import quantstats as qs

# Generate synthetic daily returns (252 trading days x 3 years)
np.random.seed(42)
dates = pd.bdate_range("2021-01-01", periods=756)
returns = pd.Series(
    np.random.normal(0.0004, 0.012, len(dates)),  # slight positive drift
    index=dates,
    name="MyStrategy",
)

# Compute individual metrics
print("=== Key Performance Metrics ===")
print(f"  CAGR:           {qs.stats.cagr(returns):.2%}")
print(f"  Sharpe Ratio:   {qs.stats.sharpe(returns):.3f}")
print(f"  Sortino Ratio:  {qs.stats.sortino(returns):.3f}")
print(f"  Max Drawdown:   {qs.stats.max_drawdown(returns):.2%}")
print(f"  Volatility:     {qs.stats.volatility(returns):.2%}")
print(f"  Calmar Ratio:   {qs.stats.calmar(returns):.3f}")
print(f"  Win Rate:       {qs.stats.win_rate(returns):.2%}")
print(f"  Profit Factor:  {qs.stats.profit_factor(returns):.3f}")
print(f"  VaR (95%):      {qs.stats.value_at_risk(returns):.4f}")

# Print full metrics table to console (no benchmark needed)
print("\n=== Full Metrics Report ===")
qs.reports.metrics(returns, mode="basic", prepare_returns=False)

# Cumulative returns
cum = qs.stats.compsum(returns)
print(f"\nFinal cumulative return: {cum.iloc[-1]:.2%}")
```

## Available Metrics

### Return Metrics
`comp`, `compsum`, `expected_return`, `geometric_mean`, `ghpr`, `cagr`, `best`, `worst`, `consecutive_wins`, `consecutive_losses`, `avg_return`, `avg_win`, `avg_loss`, `monthly_returns`

### Risk Metrics
`volatility`, `rolling_volatility`, `implied_volatility`, `value_at_risk`, `conditional_value_at_risk`, `expected_shortfall`, `max_drawdown`, `to_drawdown_series`, `ulcer_index`, `risk_of_ruin`

### Risk-Adjusted Ratios
`sharpe`, `smart_sharpe`, `sortino`, `smart_sortino`, `adjusted_sortino`, `omega`, `calmar`, `ulcer_performance_index`, `serenity_index`, `gain_to_pain_ratio`, `risk_return_ratio`, `treynor_ratio`, `information_ratio`

### Probabilistic Metrics
`probabilistic_sharpe_ratio`, `probabilistic_sortino_ratio`, `probabilistic_adjusted_sortino_ratio`, `kelly_criterion`

### Distribution Metrics
`skew`, `kurtosis`, `outliers`, `remove_outliers`, `distribution`, `pct_rank`

### Trade Metrics
`win_rate`, `payoff_ratio`, `win_loss_ratio`, `profit_ratio`, `profit_factor`, `cpc_index`, `common_sense_ratio`, `outlier_win_ratio`, `outlier_loss_ratio`, `recovery_factor`, `exposure`, `tail_ratio`

### Benchmark Metrics
`r_squared`, `greeks` (alpha, beta), `rolling_greeks`, `compare`, `rolling_sharpe`, `rolling_sortino`

### Monte Carlo
`montecarlo`, `montecarlo_sharpe`, `montecarlo_drawdown`, `montecarlo_cagr`

## Dependencies

- numpy, pandas, scipy
- matplotlib, seaborn (visualization)
- tabulate (console output)
- yfinance (benchmark data download)
- Optional: plotly (interactive charts)

## Documentation

- [Architecture](architecture.md) — System design and components
- [Workflow](workflow.md) — Event flows and processing pipelines
- [State Management](state-management.md) — State lifecycle and data models
- [Development](development.md) — Development guide and best practices

## License

Apache License 2.0
