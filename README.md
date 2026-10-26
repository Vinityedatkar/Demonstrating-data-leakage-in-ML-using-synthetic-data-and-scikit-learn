# When Random Noise Looks Predictive: Demonstrating Data Leakage

## Project overview

This project demonstrates how supervised feature selection performed
before cross-validation can leak outcome-label information into
validation folds and inflate estimated model performance.

## Key results

Across 10 independently generated synthetic datasets (200 observations
and 5,000 random features each):

-   **Flawed evaluation:** mean ROC-AUC = **0.9760**
-   **Leakage-controlled evaluation:** mean ROC-AUC = **0.5131**
-   **Random-ranking reference:** ROC-AUC = **0.5000**

The labels were generated independently of the features. The data are
synthetic and do not represent real patients or clinical diagnoses.

## Methods

For each random seed from 0 to 9: 1. Generate a matrix of 200
observations by 5,000 normally distributed random features. 2. Generate
random binary labels independent of those features. 3. Evaluate logistic
regression with five-fold stratified cross-validation. 4. In the flawed
method, select the 100 features with the strongest univariate
association to the labels **before** cross-validation. This leaks
information from validation folds into feature selection. 5. In the
corrected method, put `SelectKBest`, `StandardScaler`, and
`LogisticRegression` in a single scikit-learn pipeline, so feature
selection is fitted separately within each training fold.

## Results by seed

    Seed   Flawed AUC   Correct AUC
  ------ ------------ -------------
       0       0.9960        0.5752
       1       0.9409        0.5193
       2       0.9534        0.4727
       3       0.9865        0.4737
       4       0.9820        0.5338
       5       0.9654        0.4568
       6       0.9760        0.4634
       7       0.9920        0.5229
       8       0.9840        0.5135
       9       0.9834        0.6002

## Interpretation

The flawed procedure produced a mean AUC of 0.9760, despite the absence
of a true feature-label relationship. The corrected procedure produced a
mean AUC of 0.5131, much closer to the 0.5 random-ranking reference.
This illustrates that validation procedures must prevent information
from the validation data influencing feature selection.

AUC values for individual datasets vary due to sampling variability. A
mean close to 0.5 is consistent with chance-level ranking in this
demonstration; it does not prove the model's performance is exactly 0.5.

## Limitations

-   This is a methodological demonstration using synthetic data, not a
    cancer prediction study.
-   The exact values depend on sample size, feature count, model,
    feature-selection procedure, cross-validation design, and random
    seeds.
-   The results are the observed outputs supplied for this project; the
    notebook should be rerun to independently reproduce them.
-   For a formal benchmark, include uncertainty estimates and additional
    validation designs, and document package versions.

## Files

`auc_by_seed.png`
`mean_auc_comparison.png`

## Reproducibility

The experiment can be reproduced in Python using NumPy, pandas,
scikit-learn, and Matplotlib. See the accompanying notebook
(`Random_Noise_Data_Leakage.ipynb`) if included by the project author.

## Responsible interpretation

Do not describe this as a cancer diagnostic model. No real patient data
were used, and the project does not establish clinical applicability.
