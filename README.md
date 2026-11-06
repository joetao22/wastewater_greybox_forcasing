# Avedøre Wastewater Forecasting Atlas

How well can we forecast ammonium (NH₄) and dissolved nitrous oxide (N₂O) 30 minutes ahead from wastewater treatment sensor data? This project follows two years of measurements from Line 3, Tank 1 at the Avedøre wastewater treatment plant in Denmark. A single notebook combines data-quality analysis, forecasting baselines, and paper-based grey-box models with **16 figures**.

[View the notebook on GitHub](./avedore_wastewater_forecasting_atlas.ipynb) · [Open on Kaggle](https://www.kaggle.com/code/jueyetao/grey-box-model-forcasting)

## What the notebook covers

| Analysis | What to look for |
| --- | --- |
| Data quality and alignment | A dictionary of 24 signals, quality-flag screening, and a regular two-minute UTC grid. Coverage plots distinguish gaps with rejected readings from gaps with no source measurements. |
| N₂O forecasting baselines | Persistence, trailing means, and Ridge regression. The notebook separates the original baseline scores from a runnable reconstruction with documented features. |
| Grey-box models | Delayed process inputs and Equations (4) and (5) for NH₄ and N₂O. The NH₄ analysis compares delays at two-minute and 30-minute horizons. [Debel et al. (2025)](https://doi.org/10.1016/j.jwpe.2025.108128) |
| Walk-forward N₂O evaluation | A static model and daily refits over three- and seven-day windows. Each update uses completed phases available before the update. Unsupported fits fall back to persistence. |

## Main results

For **NH₄**, a 22-minute input delay gave the lowest 30-minute validation error among the five rollout candidates. Compared with persistence, it reduced MAE by **38.47%** and RMSE by **31.33%** on 50,224 common validation forecasts.

For **N₂O**, the models below share **99,678 test forecast origins**, covering 84.99% of candidate origins. MAE and RMSE are reported in mg N/L.

| 30-minute N₂O forecast | MAE ↓ | RMSE ↓ | Fallback to persistence |
| --- | ---: | ---: | ---: |
| Persistence | 0.043935 | 0.068191 | — |
| **Static grey-box (Soft-L1)** | **0.041806** | **0.061051** | — |
| Seven-day rolling grey-box | 0.042927 | 0.062700 | 7.20% |
| Three-day rolling grey-box | 0.043974 | 0.064806 | 37.09% |

The seven-day window performed best on validation in the grey-box comparison. On test, it improved on persistence overall, while its performance varied by month. The static grey-box also achieved the lowest MAE and RMSE in a separate comparison with the reconstructed baselines on **96,360 matched test origins**. These results apply to the observed concentrations in this tank on eligible samples.

## Reproduce the analysis

### Kaggle

1. Open the linked Kaggle notebook or import `avedore_wastewater_forecasting_atlas.ipynb` into a new Kaggle Notebook.
2. Use **Add Input** to attach a dataset containing `aved_raw.csv` or `aved_raw_semicolon.csv`. Either file is sufficient.
3. Run all cells on CPU. The notebook writes figures, quality summaries, forecast metrics, and parameter schedules to `/kaggle/working/wastewater_forecast_atlas/`.

### Local Jupyter

Download one raw CSV from the dataset release. From the folder containing the notebook, install the dependencies and launch JupyterLab. Replace the example input path with the path to your CSV.

```bash
python -m pip install jupyterlab numpy pandas scipy matplotlib scikit-learn
export WASTEWATER_INPUT_PATH="/path/to/aved_raw.csv"
python -m jupyterlab
```

Open the notebook and run all cells. Local outputs are saved under `wastewater_forecast_atlas_outputs/` by default.

## Data and references

- **Dataset.** Hansen et al. (2024), *Time Series Dataset for Modeling and Forecasting of N₂O in Wastewater Treatment*, version 4. The dataset is released under CC BY 4.0. [Mendeley Data · DOI 10.17632/xmbxhscgpr.4](https://data.mendeley.com/datasets/xmbxhscgpr/4)
- **Data documentation.** The companion paper describes the sensor tags, operating phases, and quality codes. [Hansen et al. (2024)](https://arxiv.org/abs/2407.05959)
- **Model equations.** Debel et al. (2025), *Model predictive control for nitrous oxide mitigation in a full-scale wastewater treatment plant*. [DOI 10.1016/j.jwpe.2025.108128](https://doi.org/10.1016/j.jwpe.2025.108128)
