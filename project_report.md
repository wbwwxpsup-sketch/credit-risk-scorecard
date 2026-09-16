# Credit Risk Prediction - Give Me Some Credit

## Project background

The project predicts serious delinquency within two years and compares credit approval cutoffs under stated profit and loss assumptions. The data come from the Kaggle Give Me Some Credit competition.

## Data and cleaning

The training file contains 150,000 records and 12 columns, including a row index. Cleaning removes that index, 1 record with age at or below zero, 269 records with delinquency codes of 96 or 98, and 565 exact duplicates. This leaves 149,165 records.

Duplicates are removed before imputation. A binary feature records missing income. Missing income is filled with the training median of 5,400; missing dependent counts are filled with the training mode of zero. The debt ratio is capped at the training 99th percentile, 4,936. Each cross-validation fit estimates its own preprocessing values. The test set retains all 101,503 records.

## Exploratory analysis

Serious delinquency occurs in 6.62% of retained records. Predicting every record as non-delinquent would give accuracy of 93.38%, so accuracy alone would provide little evidence of useful risk discrimination.

Observed delinquency rises from 4.64% for borrowers with no previous 90-day delinquency to 33.72% for one occurrence, 49.90% for two, and 61.68% for three or more. It also rises across utilization groups, from 1.82% at 0-10% utilization to 37.25% above 100%.

These grouped patterns need not match Pearson correlations. The raw utilization variable has extreme values, so its near-zero linear correlation with the target does not contradict the grouped risk pattern. The tables and correlation matrix are included in the repository.

## Features and models

A stratified split assigns 111,873 records to training and 37,292 to validation. The models use 16 features, including the original borrower variables, an income-missing indicator, any past delinquency, maximum delinquency level, log income, capped debt ratio and log capped debt ratio.

Logistic Regression uses standardized features and balanced class weights. LightGBM uses 300 trees, a learning rate of 0.05 and 31 leaves, without class weighting. Five-fold cross-validation is performed within training data. A separate three-fold calibration fit within training data applies sigmoid calibration to LightGBM probabilities.

| Model | Training AUC | Validation AUC |
|---|---:|---:|
| Logistic Regression | 0.8228 | 0.8186 |
| LightGBM | 0.9084 | 0.8582 |
| Calibrated LightGBM | 0.9166 | 0.8592 |

LightGBM's five-fold mean AUC is 0.8637, with standard deviation 0.0022. Training AUC is higher than validation AUC. The small CV dispersion describes variation across these folds; it does not establish performance stability in future populations. The calibrated model's training AUC is an in-sample diagnostic rather than an out-of-fold estimate.

## Validation results

Calibrated LightGBM achieves AUC 0.8592, KS 0.5691 and Brier score 0.0505. The maximum-KS probability threshold is 0.0446. The highest-risk decile has observed delinquency of 35.47%, compared with 6.62% across validation records, giving lift of 5.36.

At a classification threshold of 0.10, the confusion matrix is:

| Actual outcome | Predicted 0 | Predicted 1 |
|---|---:|---:|
| No serious delinquency | 32,443 | 2,381 |
| Serious delinquency | 1,154 | 1,314 |

Risk groups below 5%, from 5% to below 15%, and at or above 15% have observed delinquency of 2.36%, 14.19% and 40.59%, respectively. Risk increases across the groups. Calibration is not exact: the middle group's observed rate is close to its upper boundary. The calibration plot provides a more detailed check.

The most-used features by split count are utilization, age, monthly income, debt ratio and open credit lines. These counts come from the uncalibrated LightGBM fit, not the calibration ensemble. Log income and log debt ratio have zero split counts in that fit. Creating a feature does not imply that it improved the model.

## Approval and profit simulation

The simulation assumes a loan amount of RMB 10,000, profit of RMB 1,000 for a good loan and loss of RMB 7,000 for a bad loan. A borrower is approved when predicted probability is below the cutoff. Expected profit per loan is `1000 * (1 - p) - 7000 * p`; the theoretical break-even probability is 12.5%.

| Cutoff | Approval rate | Observed delinquency among approved | Profit using validation outcomes (RMB) | Expected profit using probabilities (RMB) |
|---|---:|---:|---:|---:|
| 5% | 80.56% | 2.36% | 24,361,000 | 22,258,788 |
| 10% | 90.09% | 3.43% | 24,365,000 | 23,911,427 |
| 15% | 92.59% | 3.90% | 23,754,000 | 23,934,835 |
| 20% | 93.86% | 4.17% | 23,313,000 | 23,754,651 |

Using validation outcomes, 10% ranks first among these four cutoffs. It approves 33,597 records. However, it exceeds the 5% cutoff by only RMB 4,000. That difference is too small to establish a reliable advantage without further testing.

Using predicted probabilities, 15% ranks first among the four cutoffs. This is a different calculation, and the two rankings should be reported separately. A 15% cutoff also admits some loans above the theoretical 12.5% break-even probability. An aggregate profit comparison does not imply that every additional approved loan has positive expected profit. The one-page summary uses the ranking based on observed validation outcomes. It does not claim a global or deployable optimum.

## Business recommendations

1. Apply stricter review to borrowers with severe past delinquency and high utilization. Evaluate limit or pricing changes separately.
2. Consider lower limits, manual review or additional guarantees for middle-risk borrowers rather than rejecting the entire group.
3. Monitor borrower composition and approval rates monthly. Track AUC, KS, observed delinquency and calibration when outcome labels mature.

## Kaggle submission

The calibrated model is refitted on all retained training records before predicting the test set. The completed submission after the competition deadline receives public score 0.86147 and private score 0.86707. These scores are separate from the validation results above.

## Limitations

Serious delinquency is used as a proxy for a bad loan; realized loss is not observed. The financial assumptions exclude funding, operating and capital costs. Cutoff selection uses validation labels and needs an independent evaluation. Changes in calibration or applicant composition could change the preferred policy. Split importance is descriptive and does not identify causal effects.

## Tools

Python, pandas, NumPy, matplotlib, scikit-learn, LightGBM, joblib and Kaggle Notebook. Package versions are recorded in `requirements.txt`.
