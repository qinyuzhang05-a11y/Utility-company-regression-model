# Utility-company-regression-model
Time-Series Revenue Forecasting

A notebook that forecasts Apple's quarterly revenue using linear regression on time, with a seasonal adjustment for the fiscal-Q1 revenue spike. Built for AFM244 (Week 11, Thursday session) and runs in Google Colab.

Note: this README was reconstructed from a PDF export of the notebook (G.pdf). If your actual .ipynb differs, treat filenames and cell order below as a guide rather than an exact match.

What it does
Loads a quarterly sales dataset (qSales_2024.csv) covering many companies (277 rows, 16 columns).
Filters to Apple (tic == 'AAPL', 92 quarters, 2001–2024).
Plots revenue (saleq) over time to inspect the trend and seasonality.
Adds a numeric time index (1, 2, 3, …) to use time as the independent variable.
Splits the data 75% train / 25% test (chronologically — no shuffling).
Fits an OLS regression and forecasts the test quarters.
Adds a seasonal dummy (fiscal Q1) plus a time × dummy interaction term and refits, so the model captures both the trend and the recurring Q1 jump.
Produces prediction intervals (80% / 90% confidence) for the forecasts.
Requirements
Python 3
pandas, numpy, statsmodels, matplotlib

In Colab these are preinstalled. Locally:

bash
pip install pandas numpy statsmodels matplotlib
Data

The notebook expects a file named qSales_2024.csv in the working directory. Relevant columns:

Column	Meaning
tic	Ticker (e.g. AAPL)
conm	Company name
datadate	Quarter-end date
fqtr	Fiscal quarter (1–4)
saleq	Quarterly sales / revenue (the value being forecast)

I do not have a verified source for where this CSV originates; the field names (gvkey, saleq, datacqtr, etc.) match the Compustat quarterly format, but you should confirm with your course materials. In Colab, upload the CSV via the file panel or mount Google Drive before running.

How to run

In Colab (as intended):

Open the .ipynb in Google Colab.
Upload qSales_2024.csv (Files panel, or mount Drive).
Run cells top to bottom (Runtime → Run all).

Locally (Jupyter):

Put the notebook and qSales_2024.csv in the same folder.
jupyter notebook and run cells in order.

Run the cells sequentially — later cells depend on variables (apple_sales, dt4training, model1) created earlier.

The model

Baseline (trend only):

apple revenue ≈ -13,536 + 1,077 × time

Extended (trend + seasonal dummy + interaction):

apple revenue ≈ -11,044 + 933 × time
                 + (-10,422) × release_dummy_variable
                 + 578 × release_dummy_interaction

where release_dummy_variable = 1 when fqtr == 1 (Apple's fiscal Q1, the holiday quarter) and release_dummy_interaction = time × release_dummy_variable.

The coefficient numbers above are the fitted values shown in the exported notebook; they will re-compute when you run it and may shift slightly with a different data file.

Notes / gotchas
SettingWithCopyWarning appears when new columns are added to the filtered apple_sales slice. It's a warning, not an error; the notebook still runs. To silence it cleanly, create the slice with .copy() (e.g. apple_sales = qSales.loc[qSales['tic']=='AAPL'].copy()).
The train/test split is chronological (first 75% train, last 25% test), which is correct for time-series forecasting — don't shuffle.
For prediction ranges, read the obs_ci_lower / obs_ci_upper columns (individual prediction interval), not mean_se. alpha=0.2 → 80% confidence; alpha=0.1 → 90%.
Display formatting is set to two decimals via pd.options.display.float_format.
