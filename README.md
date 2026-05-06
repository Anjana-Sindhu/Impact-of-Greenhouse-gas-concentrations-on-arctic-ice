# Impact-of-Greenhouse-gas-concentrations-on-arctic-ice

This repository contains an economic and climate modelling project that examines how Northern Hemisphere sea ice extent changes alongside atmospheric greenhouse gas concentrations. It combines monthly sea ice observations with carbon dioxide (CO2) and methane (CH4) data, prepares reproducible modelling datasets, creates analysis figures, validates a SARIMAX time-series model, and produces a 12-month sea ice extent forecast.

The project can be run either from the notebook or from the Python pipeline. The pipeline is the preferred reproducible entry point because it regenerates the processed datasets, figures, validation files, and forecast output from the raw inputs.

## Research Question

How has Northern Hemisphere sea ice extent changed over time, and how can CO2 and CH4 concentration trends help explain and forecast those changes?

The analysis focuses on:

- long-term Northern Hemisphere sea ice extent trends;
- relationships between sea ice extent, CO2, and CH4;
- July-December melt-season behaviour;
- short-term SARIMAX forecasting with greenhouse gas variables as exogenous regressors.

## Project Structure

```text
.
├── main.py
├── artic_ice.ipynb
├── src/
│   └── arctic_sea_ice_analysis.py
├── data/
│   ├── raw/
│   └── processed/
├── outputs/
│   └── figures/
├── ECO Project first draft.pptx
├── pyproject.toml
├── uv.lock
└── README.md
```

Key files and folders:

- `main.py`: small entry point that runs the analysis pipeline.
- `artic_ice.ipynb`: notebook version for interactive review and presentation work.
- `src/arctic_sea_ice_analysis.py`: main reproducible analysis pipeline.
- `data/raw/`: source and prepared input datasets.
- `data/processed/`: generated modelling datasets, validation files, and forecast output.
- `outputs/figures/`: generated JPEG charts used for reports and slides.
- `ECO Project first draft.pptx`: presentation draft for the project.

## Data

The project uses:

- NSIDC Sea Ice Index monthly Northern Hemisphere sea ice data;
- NOAA Global Monitoring Laboratory monthly CO2 data;
- NOAA Global Monitoring Laboratory monthly CH4 data.

The pipeline reads these main input workbooks:

- `data/raw/Sea_Ice_Index_Monthly_Data_with_Statistics_G02135_v3.0.xlsx`
- `data/raw/CO2_CH4_conc_1983.xlsx`

Additional raw NOAA CSV files are also stored in `data/raw/`:

- `data/raw/co2_mm_mlo.csv`
- `data/raw/ch4_mm_gl.csv`

The common modelling period starts at `1983-07-01` because the methane data begins in July 1983. The full monthly dataset keeps every month from that point onward, while a separate melt-season dataset keeps July through December observations for seasonal interpretation.

## Method

The reproducible pipeline:

1. Checks that required raw files exist.
2. Validates the expected workbook sheets and columns.
3. Loads monthly Northern Hemisphere sea ice data.
4. Loads monthly CO2 and CH4 concentration data.
5. Merges the datasets by `year` and `month`.
6. Filters the shared time window from July 1983 onward.
7. Interpolates monthly analysis columns where needed.
8. Creates a July-December melt-season subset.
9. Exports processed Excel datasets.
10. Generates static JPEG figures.
11. Trains and validates a SARIMAX model on a 24-month holdout period.
12. Writes validation predictions and metrics.
13. Refits the model on the full monthly dataset.
14. Produces a 12-month continuation forecast.

SARIMAX is configured with:

- dependent variable: sea ice extent;
- exogenous regressors: average CO2 ppm and average CH4 ppb;
- non-seasonal order: `(1, 1, 1)`;
- seasonal order: `(1, 1, 1, 12)`;
- validation window: final 24 monthly observations.

Future CO2 and CH4 values are estimated from recent average monthly changes, so the forecast should be read as a continuation scenario rather than a policy or emissions scenario.

## Generated Outputs

Running the pipeline regenerates:

- `data/processed/SeaIce_CO2_CH4_Merged.xlsx`
- `data/processed/Transformed Data.xlsx`
- `data/processed/Transformed Data - Melt Season.xlsx`
- `data/processed/model_validation_metrics.csv`
- `data/processed/model_validation_predictions.csv`
- `data/processed/sea_ice_extent_12_month_forecast.xlsx`
- JPEG figures in `outputs/figures/`

Generated figures:

- `extent_time_series.jpg`
- `avg_CO2_ppm_time_series.jpg`
- `avg_CH4_ppb_time_series.jpg`
- `correlation_matrix.jpg`
- `monthly_extent_distribution.jpg`
- `melt_season_extent.jpg`
- `seasonal_decomposition.jpg`
- `sarimax_12_month_forecast.jpg`

## Latest Validation Results

The current generated validation metrics use the final 24 months as the holdout period:

```text
MAE:  0.227
RMSE: 0.258
MAPE: 2.57%
```

The metrics are measured against sea ice extent in million square kilometres. The validation predictions are saved in `data/processed/model_validation_predictions.csv` with actual values, predicted values, and confidence interval bounds.

## Reproduce the Analysis

This project uses `uv` for dependency management.

Install dependencies:

```bash
uv sync
```

Run the full pipeline:

```bash
uv run python src/arctic_sea_ice_analysis.py
```

You can also run the wrapper entry point:

```bash
uv run python main.py
```

If the execution environment has a read-only home directory, redirect the `uv` cache and Python install paths to a writable location:

```bash
UV_CACHE_DIR=/tmp/uv-cache UV_PYTHON_INSTALL_DIR=/tmp/uv-python uv run python src/arctic_sea_ice_analysis.py
```

To work in the notebook:

```bash
uv run jupyter lab
```

Then open and run `artic_ice.ipynb`.

## Dependencies

Project dependencies are defined in `pyproject.toml` and locked in `uv.lock`. Main libraries include:

- `pandas`
- `numpy`
- `openpyxl`
- `matplotlib`
- `seaborn`
- `statsmodels`
- `scikit-learn`
- `jupyterlab`
- `python-pptx`

## Notes and Limitations

- CO2 and CH4 are climate-relevant explanatory variables, but this statistical model does not prove direct causality.
- SARIMAX is useful for short-term statistical forecasting, but it is not a physical climate model.
- The melt-season subset is for July-December interpretation; the SARIMAX model uses the full monthly sequence to preserve seasonality.
- The 12-month forecast depends on projected greenhouse gas regressors based on recent average changes.
- Stronger climate modelling would require additional variables such as temperature, ocean heat, atmospheric circulation, sea surface temperature, and radiative forcing.
