# Day 23 — Software Performance / Final Validation

## Frozen evaluation protocol
- Loaded the Day 22 fitted pipeline without changing its parameters.
- Reconstructed the same chronological 70% / 15% / 15% split from the exact Day 22 CSV.
- Training: 1939 rows; validation: 416 rows; reserved test: 416 rows.
- Evaluated the final method on the latest held-out test partition.
- Compared it with the strongest simple validation baseline from Day 22: a constant-mean predictor, fitted on train + validation only.

## Held-out test quality

| Method | MAE | RMSE |
|---|---:|---:|
| Frozen pH-only Linear Regression | 1.481534 | 1.900469 |
| Constant-mean baseline | 1.481718 | 1.901536 |

Linear Regression test RMSE improvement over constant mean: **0.001067** (0.0561%).
Linear Regression test MAE improvement over constant mean: **0.000184** (0.0124%).
Linear Regression had lower absolute error on **221 / 416** test rows (53.12%).

## Runtime and model complexity

| Measurement | Linear Regression | Constant mean |
|---|---:|---:|
| Median batch inference, 416 rows (200 repeats) | 1.0856 ms | 0.0339 ms |
| P95 batch inference, 416 rows | 2.9523 ms | 0.2021 ms |
| Saved model size | 1.198 KB | 0.640 KB |

Data preparation (timestamp parsing, filtering, sorting, target creation and split) took **0.008116 seconds** in this runtime. A separate one-shot 416-row inference pass took **2.8570 ms** for Linear Regression.

## Quality-versus-complexity interpretation
The selected model is a one-feature linear regression with median imputation, so it is computationally lightweight and interpretable. The constant-mean baseline is even simpler. The held-out test comparison shows whether the small validation advantage survives unseen rows; the observed gain is **0.001067 RMSE**. If this gain is negligible in practical terms, the simpler constant baseline remains a credible reference and the model's practical benefit should not be overstated.

## Representative errors
`Day23_Representative_Test_Cases.csv` contains the five lowest-error and five highest-error test examples. `Day23_Heldout_Test_Predictions.csv` contains every held-out prediction and absolute error for both methods.

## Reproducibility artefacts
- `Day23_Final_Validation.ipynb` — notebook with frozen pipeline evaluation and benchmarking
- `Day23_Performance_Comparison.csv` — accuracy/runtime/model-size comparison
- `Day23_Performance_Metadata.json` — machine/software and evaluation metadata
- `Day23_Heldout_Test_Predictions.csv` — per-row test results
- `Day23_Representative_Test_Cases.csv` — example successes and failures
- `Day22_Final_Linear_Regression_Pipeline.joblib` — exact frozen pipeline used
- `water_potability_temporal_synthetic.csv` — exact source data

## Limitations
The timestamps are synthetic and the original water-potability dataset is static. Therefore, this is final validation of a reproducible pseudo-temporal academic experiment, not evidence of forecasting performance on a real underwater sensor stream. It is not anomaly-detection validation and provides no anomaly precision/recall. Genuine timestamped underwater/water-quality data is required before operational claims.
