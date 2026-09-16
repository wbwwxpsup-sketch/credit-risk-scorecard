# Data description

## Source

The data come from the [Kaggle Give Me Some Credit competition](https://www.kaggle.com/competitions/GiveMeSomeCredit).

## Records and target

The original training file contains 150,000 records; 149,165 remain after record cleaning. The test file contains 101,503 records. `SeriousDlqin2yrs` equals 1 for serious delinquency within two years and 0 otherwise. Kaggle does not supply the test labels.

## Access and use

Obtain the original competition data directly from Kaggle and follow the applicable competition rules and data-use terms. Raw and cleaned borrower data are excluded from the portfolio ZIP. The Kaggle working directory contains a cleaned analysis CSV separately.

## Interpretation

The dataset measures serious delinquency rather than realized loan loss. The strategy simulation assumes RMB 1,000 profit for a good loan and RMB 7,000 loss for a bad loan. These amounts are assumptions, not values observed in the dataset. See [cleaning_log.txt](cleaning_log.txt) for processing and [project_report.md](project_report.md) for the analysis.
