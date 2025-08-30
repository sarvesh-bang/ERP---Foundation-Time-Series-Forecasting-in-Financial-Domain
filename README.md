Foundation Time Series Forecasting in Financial Domain 
================================================================

*Additional materials to allow reproduction of extended research project submitted to the University of Manchester for the degree of MSc in Data Science in the Faculty of Humanities*
-----------------------------------------------------------------------------------------------------------

Student ID: 11597497
--------------------



Core Contents
-------------
Notebooks (run in this order, with the environments shown below):
1) `etl.ipynb`                  (base_venv) – Build clean panel (Top 50 Energy stocks by market capitalization during in-sample period, in-sample: 2000–2015, out-of-sample: 2016–2024)
2) `eda.ipynb`                  (base_venv) – Basic descriptive statistics and sanity checks
3) `traditional_models.ipynb`   (base_venv) – OLS, Lasso, Ridge, Elastic Net, RF, XGBoost, and MLPs
4) `timesfm_model.ipynb`        (timesfm_chronos_venv) – Google TimesFM 1.0 (200M) & 2.0 (500M) zero-shot
5) `chronos_model.ipynb`        (timesfm_chronos_venv) – Amazon Chronos-Bolt (Tiny/Mini/Small/Base) & Chronos‑T5 (Mini/Small) zero-shot
6) `uni2ts_model.ipynb`         (uni2ts_venv) – Salesforce Uni2TS Moirai 1.1 (Small/Base) & Moirai‑MoE (Small/Base) zero-shot
7) `results.ipynb`              (base_venv) – Merge all model outputs; Diebold–Mariano (DM) pairwise tests; summary tables
8) `portfolio_building.ipynb`   (base_venv) – Build bottom‑up & ML long–short decile portfolios (equal/value weighted)
9) `portfolio_results.ipynb`    (base_venv) – Compute Sharpe, annualised return/vol, MDD, and CAGR for each portfolio
10) `graphs.ipynb`				(base_venv) – Visualize results and diagnostic trends

Directory Layout (expected)
---------------------------
Create the following top‑level folders before running (optionally, update file paths accordingly withing each file):
- `Data`        : input & ETL outputs (CSV files; **data not provided in repo**)
- `Results`     : all model predictions, merged files, portfolio series & evaluation tables
	- `Graphs`		: visualizations outputs
- `Notebooks`   : optional, if you prefer to store `.ipynb` here; code assumes running from repo root

Data Expectations
-----------------
You should place **source CRSP-like** data in `Data/`:
- `raw_data.csv` with (at minimum) columns: `PERMNO, SecurityNm, Ticker, SICCD, DlyCalDt, DlyPrc, DlyRet, DlyVol, ShrOut`
- `riskfree_rate_data.csv` with: `CALDT, TDYLD` (daily risk‑free rate / Treasury yield per day)

What ETL produces in `Data/`:
- `clean_filtered_data.csv` – panel of **top 50 Energy stocks** (by avg market cap in-sample), dates, and **excess_return**
- `top50_stocks_info.csv` – PERMNO, latest Ticker & SecurityNm, and `avg_market_cap` used for value weighting

Reproducible Environments (three venvs)
---------------------------------------
The project was implemented using **three** isolated Python virtual environments:

A) base_venv  (for `etl`, `eda`, `traditional_models`, `results`, `portfolio_building`, `portfolio_results`, `graphs`)
   - Python 3.12.3
   	- pandas==2.2.2, numpy==1.26.4, scikit-learn==1.4.2, xgboost==2.1.2, scipy==1.13.1
   	- matplotlib==3.8.4, seaborn==0.13.2
   	- torch==2.6.0 (install the CUDA build that matches your GPU; CPU-only also works for these notebooks)

B) timesfm_chronos_venv  (for `timesfm_model`, `chronos_model`)
   - Python 3.10.18
   	- numpy==1.26.4, pandas==2.1.4, scikit-learn==1.6.1
	- torch==2.5.1
   	- timesfm  (library providing TimesFm/TimesFmHparams/TimesFmCheckpoint)
   	- chronos  (library providing BaseChronosPipeline)
   	- huggingface_hub  (used under the hood to fetch checkpoints by repo id)

C) uni2ts_venv  (for `uni2ts_model`)
   - Python 3.10.18
   	- numpy==1.26.0, pandas==2.1.4, scikit-learn==1.7.0
	- torch==2.5.0, gluonts==0.14.4
	- uni2ts==1.2.0

GPU/CPU notes: All foundation‑model notebooks auto‑select device via `device_map = "cuda" if torch.cuda.is_available() else "cpu"`.
Chronos-T5 runs on CPU, but GPU is recommended for speed.

Key Parameters
--------------
- `WINDOW` (rolling context length) – set default to 21 trading days (best performing window) in each notebook. (Could be changed to test/validate other window sizes. Window sizes tested in project = {5, 21, 252, 512})
- Date splits: In‑sample 2000‑01‑01 to 2015‑12‑31; Out‑of‑sample 2016‑01‑01 to 2024‑12‑31. (Could be changed as per needs.)
- Column names: `Date` (datetime), `PERMNO` (int), `excess_return` (float). (Modify as per data)

Artifacts Produced (Results/)
-----------------------------
(Names include `WINDOW`, e.g., {{WINDOW=21}} → “…21.csv”)

From model notebooks:
- `models_best_parameters{{WINDOW}}.csv`      – best hyper‑parameters for Lasso, Ridge, Elastic Net, RF, XGB, MLPs
- `models_results{{WINDOW}}.csv`              – predictions from traditional models
- `timesfm_models_results{{WINDOW}}.csv`      – predictions from TimesFM‑1.0‑200M & TimesFM‑2.0‑500M
- `chronos_models_results{{WINDOW}}.csv`      – predictions from Chronos‑Bolt (Tiny/Mini/Small/Base) & Chronos‑T5 (Mini/Small)
- `uni2ts_models_results{{WINDOW}}.csv`       – predictions from Moirai (Small/Base) & Moirai‑MoE (Small/Base)

Aggregation / evaluation:
- `merged_results{{WINDOW}}.csv`              – all predictions aligned with `Date, PERMNO, excess_return`
- `overall_stats.csv`                         – descriptive stats for excess returns (from `eda.ipynb`)

Portfolios (built on merged predictions):
- `ml_equal_portfolio_results{{WINDOW}}.csv`  – daily returns of long‑short ML decile portfolio (equal‑weighted)
- `ml_value_portfolio_results{{WINDOW}}.csv`  – same, value‑weighted (by `avg_market_cap` from Data/top50_stocks_info.csv)
- `bu_equal_portfolio_results{{WINDOW}}.csv`  – bottom‑up (benchmark) equal‑weighted long‑short
- `bu_value_portfolio_results{{WINDOW}}.csv`  – bottom‑up (benchmark) value‑weighted long‑short

Portfolio evaluations (Bottom-up and Long-Short):
- `ml_equal_portfolio_evaluation{{WINDOW}}.csv`
- `ml_value_portfolio_evaluation{{WINDOW}}.csv`
- `bu_equal_portfolio_evaluation{{WINDOW}}.csv`
- `bu_value_portfolio_evaluation{{WINDOW}}.csv`


License
-------
Code is provided for academic reproducibility only. Data files are **not** included.
