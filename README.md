# Credit Risk Prediction - Give Me Some Credit

This project predicts serious delinquency within two years using Kaggle borrower data. It compares Logistic Regression and LightGBM, calibrates LightGBM probabilities, and evaluates four credit approval cutoffs.

## Data

The data come from [Give Me Some Credit](https://www.kaggle.com/competitions/GiveMeSomeCredit). The training file contains 150,000 records; 149,165 remain after cleaning. The test file contains 101,503 records. Download the original files from Kaggle and follow the competition rules and data-use terms. Borrower-level data are not included in this repository. See [DATA_DESCRIPTION.md](DATA_DESCRIPTION.md).

## Run the analysis

1. Open `analysis.ipynb` in Kaggle.
2. Attach Give Me Some Credit through **Add Input**.
3. Select **Run All**.

The code reads `cs-training.csv` and `cs-test.csv` under `/kaggle/input` and writes outputs to `/kaggle/working`. `credit_risk_project.py` runs the same analysis. Both files retain the source wrapper used to export the notebook and script. Package versions are in `requirements.txt`. The exported notebook contains code without stored execution outputs; the tables, figures and PDF record the completed run.

Imputation values and the debt-ratio cap are estimated from the training partition and refitted inside cross-validation folds. Calibration uses three folds within training data. The validation partition is used for evaluation and cutoff comparisons. The submission model is then refitted on all retained training records.

## Results

| Measure | Result |
|---|---:|
| Logistic Regression validation AUC | 0.8186 |
| LightGBM validation AUC | 0.8582 |
| Calibrated LightGBM validation AUC | 0.8592 |
| Calibrated LightGBM validation KS | 0.5691 |
| LightGBM five-fold CV mean AUC | 0.8637 |
| CV standard deviation | 0.0022 |
| Highest-risk-decile lift | 5.36 |
| Kaggle public score | 0.86147 |
| Kaggle private score | 0.86707 |

The Kaggle scores are from the completed submission after the competition deadline. They are separate from the project's validation metrics.

## Approval simulation

Assuming RMB 1,000 profit per good loan and RMB 7,000 loss per bad loan, the 10% cutoff gives the highest profit calculated from validation outcomes: RMB 24,365,000. It approves 90.09% of records, with observed delinquency of 3.43% among approved borrowers.

The 5% cutoff gives RMB 24,361,000, only RMB 4,000 less. The comparison does not establish that 10% is reliably better on new data. When profit is calculated from predicted probabilities instead of observed labels, 15% has the highest expected profit among the four cutoffs. See [approval_profit_simulation.csv](approval_profit_simulation.csv).

## Project files

- [analysis.ipynb](analysis.ipynb): runnable notebook.
- [credit_risk_project.py](credit_risk_project.py): analysis script.
- [cleaning_log.txt](cleaning_log.txt): cleaning counts and preprocessing values.
- [one_page_project_summary.pdf](one_page_project_summary.pdf): one-page summary with four charts.
- [project_report.md](project_report.md): methods, results and strategy discussion.
- [DATA_DESCRIPTION.md](DATA_DESCRIPTION.md): data source and usage notes.
- [figures](figures): exploratory plots, ROC curve and calibration plot.
- [model_comparison.csv](model_comparison.csv): model performance.
- [resume_bullet.txt](resume_bullet.txt): concise project description.

## Scope of the results

The target records serious delinquency, not observed loan loss. The financial amounts are illustrative. Cutoff selection uses validation labels and needs confirmation on independent data before a lending decision. Calibration and borrower composition may change over time. Feature split counts describe model usage and do not measure causal effects.
