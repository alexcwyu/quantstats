# QuantStats Development Guide

## Project Setup

```bash
cd quantstats
pip install -e .
# or
pip install -e ".[dev]"
```

## Dependencies

**Core**: numpy, pandas, scipy, matplotlib, seaborn, tabulate
**Data**: yfinance (benchmark downloads)
**Optional**: plotly (interactive charts), IPython (notebook display)

## Project Structure

```
quantstats/
    src/quantstats/
        __init__.py          # Package init, extend_pandas()
        stats.py             # 60+ statistical functions
        reports.py           # HTML and console report generation
        plots.py             # Plotting entry point
        utils.py             # Data preparation and validation
        _plotting/
            __init__.py
            core.py          # Low-level chart rendering
            wrappers.py      # High-level chart API
        _montecarlo.py       # Monte Carlo simulation
        _compat.py           # pandas/yfinance compatibility
        _numpy_compat.py     # NumPy compatibility
        version.py           # Version string
```

## Testing

```bash
# Run all tests
pytest

# Run specific tests
pytest tests/test_stats.py
pytest tests/test_reports.py

# With coverage
pytest --cov=quantstats
```

## Code Conventions

### Function Signatures

All stat functions follow this pattern:

```python
def metric_name(
    returns: _pd.Series | _pd.DataFrame,
    rf: float = 0.0,
    periods: int = 252,
    annualize: bool = True,
    prepare_returns: bool = True,
) -> float | _pd.Series:
    """
    Brief description.

    Args:
        returns: Return series or DataFrame
        rf: Risk-free rate (default: 0.0)
        periods: Periods per year for annualization (default: 252)
        annualize: Whether to annualize the result (default: True)
        prepare_returns: Whether to prepare returns first (default: True)

    Returns:
        Computed metric value
    """
```

### Type Aliases

```python
Returns = _pd.Series | _pd.DataFrame
```

### Import Conventions

Internal imports use underscore-prefixed aliases:

```python
import pandas as _pd
import numpy as _np
from scipy.stats import norm as _norm
```

This prevents namespace pollution when the module is imported with `*`.

### Lazy Imports

To avoid circular dependencies between stats, reports, and plots:

```python
_stats = None
def _get_stats():
    global _stats
    if _stats is None:
        from . import stats
        _stats = stats
    return _stats
```

## Adding a New Metric

1. Add the function to `stats.py`:

```python
def my_metric(
    returns: Returns,
    rf: float = 0.0,
    periods: int = 252,
    prepare_returns: bool = True,
) -> float:
    """
    Description of my metric.

    Args:
        returns: Return series
        rf: Risk-free rate
        periods: Periods per year
        prepare_returns: Whether to prepare returns

    Returns:
        float: Computed metric
    """
    if prepare_returns:
        returns = _utils._prepare_returns(returns, rf=rf, nperiods=periods)

    # Compute and return metric
    return computed_value
```

2. Add to `extend_pandas()` in `__init__.py`:

```python
_po.my_metric = stats.my_metric
```

3. Add to the metrics table in `reports.py` if it should appear in reports

4. Add tests

## Adding a New Plot

1. Add the wrapper function in `src/quantstats/_plotting/wrappers.py`:

```python
def my_chart(
    returns: Returns,
    benchmark: Returns | None = None,
    title: str = "My Chart",
    savefig: dict | None = None,
    show: bool = True,
    **kwargs,
) -> _Figure | None:
    """Chart description."""
    stats = _get_stats()
    utils = _get_utils()

    # Prepare data
    returns = utils._prepare_returns(returns)

    # Create figure
    fig, ax = _plt.subplots(figsize=(10, 6))

    # Plot
    ax.plot(returns.index, returns.values)
    ax.set_title(title)

    # Apply standard formatting
    _core.format_plot(fig, ax)

    if savefig:
        fig.savefig(**savefig)
    if show:
        _plt.show()

    return fig
```

2. Export from `src/quantstats/_plotting/__init__.py` (it re-exports from wrappers)

3. Add to `extend_pandas()`:

```python
_po.plot_my_chart = plots.my_chart
```

## Adding to HTML Reports

In `reports.py`, the `html()` function generates the report. To add a new section:

1. Compute the metric in the metrics gathering section
2. Add the chart generation call
3. Add the HTML template section

## Performance Considerations

- The `_prepare_returns()` cache helps when the same data is analyzed with multiple metrics
- Monte Carlo simulations use pre-allocated numpy arrays for efficiency
- Chart generation is the slowest part -- consider using `show=False` when generating many charts
- For large DataFrames, individual metric calls are faster than full `reports.html()`

## Compatibility Layer

`_compat.py` handles breaking changes in dependencies:

- `safe_concat()` -- Handles pandas concat API changes
- `safe_resample()` -- Handles resampling frequency string changes (e.g., 'M' to 'ME')
- `safe_yfinance_download()` -- Handles yfinance API changes

When pandas or yfinance release breaking changes, update `_compat.py` rather than scattering version checks throughout the codebase.

## Error Handling

Use the custom exception hierarchy:

```python
from quantstats.utils import DataValidationError, CalculationError

# In validation
if data is None:
    raise DataValidationError("Input data cannot be None")

# In computation
try:
    result = complex_calculation()
except ZeroDivisionError:
    raise CalculationError("Division by zero in metric calculation")
```

## Release Checklist

1. Update `version.py`
2. Run full test suite
3. Verify HTML report generation
4. Test pandas extension
5. Test with latest pandas/numpy/matplotlib versions
6. Update `_compat.py` if needed for new dependency versions

## Configuration Reference

QuantStats functions are configured via function parameters rather than global configuration files. Below are the key parameters for the most commonly used functions.

### Common Parameters (shared across most `stats.*` functions)

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series or DataFrame | _(required)_ | Return series (daily returns as decimals, e.g., 0.01 = 1%) |
| `rf` | float | `0.0` | Risk-free rate (annualized, same scale as returns) |
| `periods` | int | `252` | Number of trading periods per year (252 for daily, 12 for monthly) |
| `annualize` | bool | `True` | Whether to annualize the computed metric |
| `prepare_returns` | bool | `True` | Whether to auto-detect and convert prices to returns |

### `reports.html()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series | _(required)_ | Strategy return series |
| `benchmark` | Series or str | `None` | Benchmark returns or ticker symbol (e.g., `"SPY"`) to download |
| `rf` | float | `0.0` | Risk-free rate |
| `output` | str | `None` | Output HTML file path (returns HTML string if `None`) |
| `title` | str | `"Strategy Tearsheet"` | Report title |
| `compounded` | bool | `True` | Whether returns should be compounded |
| `periods_per_year` | int | `252` | Periods per year for annualization |
| `match_dates` | bool | `True` | Align benchmark dates to strategy dates |
| `download_filename` | str | `None` | Filename for the download button in the HTML report |

### `reports.metrics()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series | _(required)_ | Strategy return series |
| `benchmark` | Series or str | `None` | Benchmark for comparison |
| `rf` | float | `0.0` | Risk-free rate |
| `display` | bool | `True` | Print to console (`True`) or return DataFrame (`False`) |
| `mode` | str | `"basic"` | Detail level: `"basic"`, `"full"` |
| `sep` | bool | `False` | Add separator lines between metric groups |
| `compounded` | bool | `True` | Whether returns are compounded |
| `periods_per_year` | int | `252` | Periods per year |
| `prepare_returns` | bool | `True` | Auto-convert prices to returns |
| `match_dates` | bool | `True` | Align benchmark dates |

### `stats.sharpe()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series or DataFrame | _(required)_ | Return series |
| `rf` | float | `0.0` | Risk-free rate |
| `periods` | int | `252` | Periods per year |
| `annualize` | bool | `True` | Annualize the ratio |
| `smart` | bool | `False` | Apply autocorrelation penalty (Smart Sharpe) |

### `stats.sortino()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series or DataFrame | _(required)_ | Return series |
| `rf` | float | `0.0` | Risk-free rate |
| `periods` | int | `252` | Periods per year |
| `annualize` | bool | `True` | Annualize the ratio |
| `smart` | bool | `False` | Apply autocorrelation penalty (Smart Sortino) |

### `stats.max_drawdown()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series | _(required)_ | Return series |

### `stats.value_at_risk()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series | _(required)_ | Return series |
| `sigma` | float | `1.0` | Number of standard deviations (1.0 = ~84%, 1.65 = 95%, 2.33 = 99%) |
| `confidence` | float | `0.95` | Confidence level (alternative to sigma) |
| `prepare_returns` | bool | `True` | Auto-convert prices to returns |

### `stats.montecarlo()`

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `returns` | Series | _(required)_ | Return series |
| `sims` | int | `100` | Number of simulation paths |
| `bust` | float | `-0.1` | Loss threshold for bust probability (e.g., -0.1 = -10%) |
| `goal` | float | `0.0` | Gain threshold for goal probability |

### `extend_pandas()` Configuration

| Behavior | Description |
|----------|-------------|
| Adds all `stats.*` functions as methods on `pd.Series` and `pd.DataFrame` | e.g., `returns.sharpe()`, `returns.max_drawdown()` |
| Adds all `plots.*` functions as methods | e.g., `returns.plot_snapshot()`, `returns.plot_monthly_heatmap()` |
| Does not modify existing pandas methods | Only adds new methods, no overrides |

## Troubleshooting

### 1. `TypeError: unsupported operand type(s) for -: 'str' and 'str'`
**Cause**: Passing a ticker string (e.g., `"SPY"`) as `returns` instead of actual return data.
**Solution**: QuantStats expects numeric return series, not ticker symbols. Only the `benchmark` parameter in `reports.html()` accepts ticker strings for auto-download.

### 2. `yfinance` download fails or returns empty data
**Cause**: Yahoo Finance API changes or rate limiting.
**Solution**: Update yfinance (`pip install yfinance --upgrade`). If downloads fail, pre-download benchmark data and pass it as a Series instead of a ticker string.

### 3. HTML report is blank or missing charts
**Cause**: Matplotlib backend issue, often in headless environments.
**Solution**: Set the matplotlib backend before importing QuantStats: `import matplotlib; matplotlib.use('Agg')`. Ensure `matplotlib` and `seaborn` are installed.

### 4. `ValueError: cannot reindex from a duplicate axis`
**Cause**: The returns series has duplicate index values (duplicate dates).
**Solution**: Remove duplicates: `returns = returns[~returns.index.duplicated(keep='first')]`. Ensure date parsing produced a proper DatetimeIndex.

### 5. Metrics show `inf` or `nan` values
**Cause**: The returns series is too short, all zeros, or has no variance.
**Solution**: Ensure at least 20-30 data points. Check for all-zero returns or constant values. Verify `prepare_returns=True` if passing price data.

### 6. `FutureWarning: 'M' is deprecated, use 'ME' instead`
**Cause**: pandas frequency string deprecation in pandas 2.2+.
**Solution**: Update QuantStats to the latest version. The `_compat.py` module handles these changes via `safe_resample()`.

### 7. `AttributeError: 'Series' object has no attribute 'sharpe'`
**Cause**: `qs.extend_pandas()` was not called before using pandas extension methods.
**Solution**: Call `qs.extend_pandas()` once at the start of your script or notebook.

### 8. Monthly returns heatmap shows wrong months
**Cause**: The returns index is not a proper `DatetimeIndex` or uses non-standard frequency.
**Solution**: Convert the index: `returns.index = pd.to_datetime(returns.index)`. Ensure no timezone mismatches.

### 9. `CalculationError: Division by zero in metric calculation`
**Cause**: A metric requires a denominator that is zero (e.g., zero standard deviation for Sharpe).
**Solution**: This is expected for constant-return strategies. Handle by checking for edge cases before calling the metric, or catch the exception.

### 10. Monte Carlo simulation is slow
**Cause**: High `sims` count with a long returns series.
**Solution**: Reduce `sims` (100-500 is typically sufficient). Monte Carlo uses pre-allocated NumPy arrays but still scales linearly with `sims * len(returns)`.

## Security Considerations

### API Key Management
- QuantStats uses `yfinance` for benchmark data downloads. `yfinance` does not require API keys by default (it scrapes Yahoo Finance).
- If configuring `yfinance` with a premium API key, store it in an environment variable, not in your analysis scripts.

### Credential Storage
- QuantStats does not store or manage credentials. It is a pure analytics library.
- When generating HTML reports with `output="report.html"`, the report file is written locally. Ensure output directories are not publicly accessible, as reports contain strategy performance details.

### Network Security
- The only network call QuantStats makes is via `yfinance` when a benchmark ticker string (e.g., `"SPY"`) is passed. This can be avoided by passing pre-downloaded benchmark data as a Series.
- To use QuantStats in air-gapped environments, pre-download benchmark data and pass it directly. All metric computations are purely local.

### Safe Practices
- When using `extend_pandas()`, be aware that it modifies the global `pandas` namespace for the entire Python process. Avoid in library code; use only in scripts and notebooks.
- HTML reports embed all data inline (no external CDN calls). They are safe to share but may contain sensitive strategy performance data.
- When publishing or sharing reports, review them for proprietary information before distribution.
- If using `prepare_returns=True`, QuantStats auto-detects whether input is prices or returns. For production pipelines, explicitly pass returns and set `prepare_returns=False` to avoid misdetection.
- Pin dependency versions (`pandas`, `matplotlib`, `yfinance`) to avoid breaking changes in the compatibility layer.

---
## See Also
- [README](README.md) — Project overview and quick start
- [Architecture](architecture.md) — System design and components
- [Workflow](workflow.md) — Event flows and processing pipelines
- [State Management](state-management.md) — State lifecycle and data models
