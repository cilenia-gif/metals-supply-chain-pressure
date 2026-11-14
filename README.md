# Supply Chain Pressure and Industrial Metal Prices

**Author:** Clara Engeli · Class: Advanced Data Analytics · HEC Lausanne · Autumn 2026

## Research question

Does the New York Fed's Global Supply Chain Pressure Index (GSCPI) help forecast the change in industrial metal prices over the next three months, beyond demand, oil prices, the US dollar and momentum? And is its effect stronger when supply chain pressure is high?

## Main findings

- **In sample,** the GSCPI adds no explanatory power beyond the controls (coefficient 0.003, p = 0.729), neither on average nor in high-pressure months (joint p = 0.607).
- **Out of sample,** adding the GSCPI makes the forecasts worse: its squared forecast errors are 10.4% larger than those of the model without it with an expanding window from 2011, and 64.4% larger with a train/test split at 2015.
- **The losses arise during the 2020–23 supply chain crisis,** when the GSCPI reached values far beyond the training data and its estimated effect changed sign.
- **Nonlinear specifications** (a quadratic GSCPI term and a Gaussian process with three different kernels) find no usable nonlinear effect. The Gaussian process overfits the small, noisy sample.
- The results hold for every individual metal, for equal and USGS index weights, for the quarterly change in the GSCPI instead of its level, and for forecast horizons of one, six and twelve months.
- What does help: oil prices are the most useful predictor in both designs (forecast gain +4.2% with the expanding window, Clark-West p = 0.034; +10.1% with the train/test split, p = 0.010). The model without the GSCPI beats the historical mean (+3.7% and +8.6%), although not the no-change forecast in the expanding window (+4.4% against the historical mean).


Full results: `paper.pdf`. Recording: [`recording.mp4` or link].

## Repository structure

    README.md                  this file
    paper.pdf                  research paper
    recording.mp4              presentation [or link above, if the file is large]
    requirements.txt           Python packages
    code/
      01_extract_data.ipynb    reads the raw files, saves cleaned series
      02_prepare_data.ipynb    builds the metals index, targets and predictors plus descriptive analysis
      03_model.ipynb           regressions, out-of-sample forecasts, Gaussian process, robustness; saves results
      04_figures.ipynb         final figures for the paper
    data/
      raw/                     original downloaded files
      processed/               cleaned series and the analysis dataset (created by notebooks 01–02)
    results/                   forecasts and result tables (created by notebook 03)
    figures/                   all figures (created by notebooks 02–04)

## How to reproduce

**Requirements:** Python 3.14 (tested with 3.14.0). No internet access is needed: all raw data are included in `data/raw/`.

1. Clone the repository and enter the folder:

       git clone https://github.com/cilenia-gif/metals-supply-chain-pressure.git
       cd metals-supply-chain-pressure

2. Create a virtual environment and install the packages:

       python3 -m venv .venv
       source .venv/bin/activate        # on Windows: .venv\Scripts\activate
       pip install -r requirements.txt

3. Run the four notebooks in order, either one by one in Jupyter or VS Code (Run All in each), or all at once:

       jupyter nbconvert --to notebook --execute --inplace code/01_extract_data.ipynb code/02_prepare_data.ipynb code/03_model.ipynb code/04_figures.ipynb

**Run time:** about 2–3 minutes in total with most of it stemming from notebook 03, because of the Gaussian process.

**Output:** the processed data in `data/processed/`, the result tables and forecasts in `results/`, and all figures in `figures/`. The numbers in the paper correspond to these outputs.

## Data

| Series | Source | File in `data/raw/` | Coverage | Licence / terms |
|---|---|---|---|---|
| Metal prices (copper, aluminium, nickel, tin, zinc, lead), Brent oil, natural gas index, Base Metals index | World Bank, Commodity Price Data (Pink Sheet), updated 2 Sep 2026 | `worldbank_pinksheet_monthly.xlsx` | 1960–Aug 2026 | CC BY 4.0 |
| Global Supply Chain Pressure Index | Federal Reserve Bank of New York, https://www.newyorkfed.org/research/policy/gscpi | `nyfed_gscpi.xls` | 1998–Aug 2026 | Publicly available; cited as requested by the NY Fed |
| World and Chinese industrial production | CPB Netherlands Bureau for Economic Policy Analysis, World Trade Monitor, July 2026 release | `cpb_world_trade_monitor_2026m07.xlsx` | 2000–Jul 2026 | © CPB; no terms of use stated; included for reproduction of this coursework only |
| US dollar, broad nominal effective exchange rate (NBUSBIS) | Bank for International Settlements, retrieved from FRED, Federal Reserve Bank of St. Louis, https://fred.stlouisfed.org/series/NBUSBIS | `bis_usd_broad_neer_fred.xlsx` | 1994–Jul 2026 | © BIS; use permitted with citation of the BIS as source (BIS terms of use) |
| Cobalt and lithium prices | International Monetary Fund, Primary Commodity Prices | `imf_pcps_metals_monthly.csv` | 1999– (lithium from June 2012) | IMF terms of use: free reuse with attribution |
| Metal production (for the USGS index weights) | U.S. Geological Survey, Mineral Commodity Summaries 2026 (ver. 1.3, May 2026), https://doi.org/10.3133/mcs2026 | `usgs_production_2024.csv` | 2024 | Public domain |

All data are included for reproduction of the results only; rights remain with the original providers.

All series are used in transformed form (price relatives, log changes, lags).

## Methods (overview)

- **Target:** log change in a World Bank–weighted Laspeyres index of copper, aluminium, nickel and tin (2010 = 100) over the next three months. Sample: March 2001 to May 2026 (303 months).
- **Predictors** (lagged one month): GSCPI, world industrial production growth, oil price change, US dollar change, and momentum (past three-month change in the index). Each predictor is lagged by its publication delay, hence that every forecast uses only data released by the forecast date: industrial production by two months, the GSCPI, oil and the US dollar by one month. The data are the latest revised vintages, revisions to the GSCPI and to industrial production are not reproduced.
- **Models:** linear regression with Newey-West standard errors (Model A without the GSCPI, Model B with it, Model C with a high-pressure interaction, Model B2 with a quadratic GSCPI term) and a Gaussian process (RBF kernel with one length scale per predictor plus white noise; Matérn and linear + RBF kernels as robustness checks).
- **Evaluation:** in-sample tests and out-of-sample forecasts with an expanding window (January 2011 to May 2026, 185 forecasts) and a train/test split (training to October 2014, test from January 2015); out-of-sample R² against the historical mean and against the model without the GSCPI; Clark-West test for nested models.

## Use of AI tools

This project was developed with the assistance of the AI tool Claude (Anthropic). The AI was used to draft and explain code, debug errors, and suggest wording for certain descriptions and helped with interpretations of figures and tables. The research question, data selection, modelling choices and final interpretations were decided by the author. All code was reviewed, adapted and run by the author, who takes full responsibility for the content.
