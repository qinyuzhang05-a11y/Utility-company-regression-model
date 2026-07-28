# Utility-company-regression-model
# Time Series Forecasting with Apple Quarterly Sales

This notebook demonstrates a basic time series forecasting workflow using linear regression (OLS) on Apple Inc.'s quarterly sales data, including how to model seasonality with a dummy variable and an interaction term.

> **Note:** This README was written based on a visual review of the notebook's cells and outputs (not the raw `.ipynb` source), so please double-check cell order and exact syntax against the notebook itself before relying on it.

## What This Notebook Does

1. **Loads quarterly sales data** (`qSales_2024.csv`) for multiple companies (e.g. AAPL, NTDOY) and filters it down to Apple (`AAPL`) records.
2. **Converts dates** to proper `datetime` objects and plots Apple's quarterly revenue over time to visually inspect trend and seasonality.
3. **Creates a `time` index column** (1, 2, 3, …) to use as the independent variable for a time-trend regression.
4. **Splits the data** into training (first 75%) and testing (last 25%) sets.
5. **Fits an OLS regression model** (`statsmodels.api.OLS`) of revenue (`saleq`) on `time`, and generates point forecasts and prediction intervals (using `get_prediction().summary_frame()`) for the test period.
6. **Adds a seasonal dummy variable** (`release_dummy_variable`) flagging Apple's fiscal Q1 (`fqtr == 1`), which typically captures the holiday-quarter sales spike (e.g. new iPhone release cycle), plus an **interaction term** (`time * release_dummy_variable`) to let the trend slope differ in that quarter.
7. **Re-fits the model** with the dummy and interaction term, and re-generates forecasts/prediction intervals for the test set.

## Key Concepts Illustrated

- Framing time series forecasting as an OLS regression problem where **time itself is an independent variable**.
- Train/test splitting for time-ordered data (sequential split rather than random split).
- Interpreting an OLS model's coefficients as a fitted trend line (e.g. `revenue = const + β·time`).
- Adding a seasonal dummy variable and a dummy–time interaction to model quarter-specific trend effects.
- Using `model.get_prediction(...).summary_frame(alpha=...)` to obtain both **mean confidence intervals** and **observation (prediction) intervals** — noting that `obs_ci_lower`/`obs_ci_upper` are the relevant bounds for individual forecasts, not `mean_se`.

## Requirements

```
python
pandas
numpy
statsmodels
matplotlib
```

Install with:
```bash
pip install pandas numpy statsmodels matplotlib
```

## Data

- **Input file:** `qSales_2024.csv` — expected to contain (at least) the following columns: `gvkey`, `datadate`, `fyearq`, `fqtr`, `fyr`, `indfmt`, `consol`, `popsrc`, `datafmt`, `tic`, `conm`, `curcdq`, `datacqtr`, `datafqtr`, `saleq`, `costat`.
- This looks like data sourced from a financial database such as **Compustat** (columns like `gvkey`, `indfmt`, `popsrc`, `costat` are characteristic of that source), though I can't confirm the exact provenance from the notebook alone — you may want to verify this with your course materials.
- The notebook filters this file down to Apple (`tic == 'AAPL'`) for the modeling steps, but the raw file includes multiple tickers (e.g. Nintendo/`NTDOY` also appears).

## How to Run

1. Place `qSales_2024.csv` in the same directory as the notebook (or update the file path in the `pd.read_csv(...)` call).
2. Run all cells in order from top to bottom.
3. Review the printed model coefficients and the `summary_frame()` output tables for forecasted values and confidence/prediction intervals.

## Known Issues / Notes

- The notebook throws `SettingWithCopyWarning` in a couple of cells (when creating `time`, `release_dummy_variable`, and `release_dummy_interaction` on `apple_sales`), because `apple_sales` is a filtered slice of the original DataFrame. This doesn't break the results shown here, but the more robust fix is to create `apple_sales` with `.copy()` right after filtering, e.g.:
  ```python
  apple_sales = qSales.loc[qSales['tic'] == 'AAPL'].copy()
  ```
- The 75/25 train/test split is done by row position (`int(0.75 * len(apple_sales))`), which assumes the data is already sorted chronologically.

## Course Context

This notebook appears to be course material (file name references `AFM244_S26_Week11_Thursday`), likely for a finance/accounting analytics course covering time series forecasting techniques. If you're re-using this for your own coursework or portfolio, you may want to add proper attribution back to the original course/instructor.

## License

Add a license of your choosing here if you intend to make this repository public (e.g. MIT).
