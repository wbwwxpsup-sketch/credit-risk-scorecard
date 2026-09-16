# One-Page Project Summary

## Project Background

Predict serious delinquency within two years using Kaggle borrower records, and translate predicted probabilities into simulated credit approval decisions.

## Data and Cleaning

The raw dataset contained 150,000 records and 12 columns. Cleaning removed the row index, 1 invalid-age record(s), 269 abnormal-code records and 565 duplicates. 149,165 records remained. Income missingness was retained; imputation and debt capping were learned from the fitting partition.

## Methods and Metrics

A stratified 75%/25% split was used. Logistic Regression and LightGBM were compared. Calibrated LightGBM validation AUC was 0.8592, KS was 0.5691, and Brier score was 0.0505. Five-fold training-set LightGBM CV AUC was 0.8637 (standard deviation 0.0022). Sigmoid calibration used training-only folds.

## Strategy Conclusion

The highest-risk 10% had a target rate of 35.47%, versus 6.62% overall (lift 5.36). Assuming RMB 1,000 profit per good loan and RMB 7,000 loss per bad loan, the 10% cutoff gave the highest retrospective simulated profit among four tested cutoffs: RMB 24,365,000, with 90.09% approved and 3.43% observed delinquency among approved borrowers. This validation-set result requires independent confirmation.

## Tools

Python, pandas, NumPy, matplotlib, scikit-learn, LightGBM, joblib, and Kaggle Notebook.
