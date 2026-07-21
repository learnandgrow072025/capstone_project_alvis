# Karnataka TOP-Crop Price Forecasting - Capstone Project

**QM640: Data Analytics Capstone — Walsh College**
Student: Alvis Lazarus A · Mentor: Pralhad Teggi · Term 3

Machine learning–based price forecasting for Karnataka's "TOP" crops (onion,
tomato, potato). An XGBoost regression pipeline forecasts monthly modal mandi
prices across four pilot districts (Kolar, Chikkaballapur, Bengaluru, Hassan),
benchmarked against naive and SARIMA baselines, and converts the forecast into
a profit-ranked, uncertainty-quantified planting recommendation. Full
methodology is in the project synopsis.

## Repository structure

```
capstone_project_alvis/
├── Data/       # Raw source extracts and the cleaned, merged crop-district-month panel
├── Program/    # Data-collection and modeling scripts
└── Output/     # Results, figures, and saved model artifacts
```

- **Data/** — unmodified raw pulls from each source (`raw/`) and the merged,
  analysis-ready monthly panel (`processed/`). Raw files are kept as
  originally downloaded so every downstream step is auditable back to source.
- **Program/** — one script per pipeline stage: data acquisition, cleaning
  and integration, feature engineering, baseline models (naive, SARIMA),
  XGBoost training and tuning, evaluation, and profit ranking.
- **Output/** — results tables, figures, and serialized model files produced
  by the scripts in `Program/`.

## Data sources

| Source | Data | Link |
|---|---|---|
| AGMARKNET | Mandi (wholesale) price, daily | https://agmarknet.gov.in/marketwisespecificcommodityinput |
| NASA POWER | District-level rainfall, daily | https://power.larc.nasa.gov/ |
| Agricultural Statistics at a Glance 2023 | District yield, area, production, annual | https://desagri.gov.in/wp-content/uploads/2024/09/Agricultural-Statistics-at-a-Glance-2023.pdf |
| DES Cost of Cultivation Scheme | Cost of cultivation (state-level), annual | https://desagri.gov.in/document-report-category/cost-of-cultivation-production-estimates/ |

Tomato cost of cultivation is supplemented from Vanitha et al. (2018), a
Kolar-district primary survey, since the DES series covers onion and potato
only.

## Setup

```bash
git clone https://github.com/learnandgrow072025/capstone_project_alvis.git
cd capstone_project_alvis
pip install -r Program/requirements.txt
```

## Reproducing the pipeline

Run the scripts in `Program/` in this order:

1. **Acquisition** — pull raw data from each of the four sources into `Data/raw/`.
2. **Cleaning & integration** — aggregate daily price and rainfall to
   monthly, map markets to the four pilot districts, and merge all sources
   into one crop-district-month panel in `Data/processed/`.
3. **Feature engineering** — construct lag-1 price, the season indicator,
   and merge in the rainfall/yield/cost covariates.
4. **Baseline models** — fit the seasonal-naive and SARIMA baselines.
5. **XGBoost training** — train the point-forecast model under
   rolling-origin (walk-forward) validation, plus the quantile-regression
   models for the 80%/95% prediction intervals.
6. **Evaluation** — compute MAPE/RMSE/MAE/R², run the hypothesis tests,
   extract permutation importance, and compute prediction-interval coverage.
7. **Profit ranking** — combine the price forecast with cost of
   cultivation to produce the final ranked recommendation.

All results, figures, and model files land in `Output/`.

## Reproducibility notes

- A fixed random seed is used for every stochastic step (fold construction,
  XGBoost subsampling) so re-running the pipeline against the same raw data
  reproduces the same results.
- Rolling-origin validation fixes fold boundaries by date rather than by
  random split, so folds don't change between runs.
- Raw source files in `Data/raw/` are never edited in place — all cleaning
  happens in scripts, producing new files in `Data/processed/`, so the
  pipeline can always be re-run from the original source.

## Status

In development as part of a 10-week capstone timeline. See the project
synopsis for the full timeline, data dictionary, and research questions.

## License

Academic project — Walsh College QM640 Data Analytics Capstone.
