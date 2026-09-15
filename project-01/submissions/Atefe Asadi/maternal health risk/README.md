# Maternal Health Risk Stratification

## Project overview

This project examines patterns associated with maternal health risk using the UCI Maternal Health Risk dataset. The analysis combines data quality assessment, exploratory analysis, standardized group comparisons and an interpretable multinomial Logistic Regression model.

The project is intended for statistical learning and research practice. It is not a diagnostic system and should not be used for clinical decision making.

## Research questions

The analysis addresses the following questions:

1. What does the maternal risk sample look like?
2. Which clinical measurements differ across Low, Mid and High Risk groups?
3. Which variables show the strongest association with High Risk?
4. Can a simple baseline model classify the three risk groups?
5. Does overall accuracy adequately describe model performance?
6. Where does the model fail?
7. Which variables contribute most strongly to High Risk prediction?
8. What conclusions and limitations are appropriate for this dataset?

## Dataset

The dataset contains six predictors and one categorical target:

- Age
- SystolicBP
- DiastolicBP
- BS
- BodyTemp
- HeartRate
- RiskLevel

Source: UCI Machine Learning Repository, Maternal Health Risk, Dataset 863.

DOI: https://doi.org/10.24432/C5DP5D

License: CC BY 4.0

## Data quality and preprocessing

The project CSV contained 1,014 observations and no missing values. A HeartRate value of 7 bpm was identified as implausible. Exact repeated rows were also present.

For the main modeling analysis, the two HeartRate observations with a value of 7 were excluded and exact repeated rows were removed. This produced 451 observations. Because patient identifiers are unavailable, removal of repeated rows is treated as an analytical assumption rather than proof that repeated records represent the same patient.

After this preprocessing, 35 identical predictor profiles still had more than one RiskLevel label. These observations were retained because different labels with the same available predictors may reflect unmeasured clinical information or label uncertainty.

The notebook also includes a duplicate sensitivity analysis. In that analysis, repeated rows are retained after excluding HeartRate = 7, but identical predictor profiles are kept together during train and test splitting with `StratifiedGroupKFold`. This provides a direct robustness check without allowing the same predictor profile to appear in both subsets.

## Main descriptive findings

The cleaned modeling sample contained:

- 233 Low Risk observations, 51.7 percent
- 112 High Risk observations, 24.8 percent
- 106 Mid Risk observations, 23.5 percent

Standardized mean differences comparing High Risk with the other two groups showed the strongest separation for BS, followed by SystolicBP and DiastolicBP.

Approximate absolute standardized mean differences were:

- BS: 1.613
- SystolicBP: 0.695
- DiastolicBP: 0.611
- BodyTemp: 0.515
- Age: 0.443
- HeartRate: 0.428

These results describe associations within this dataset and do not establish causal effects.

## Baseline model

A multinomial Logistic Regression model was fitted using an 80/20 stratified train and test split with random state 42. StandardScaler was placed inside a scikit learn Pipeline so that scaling parameters were learned from the training data only.

The test set contained 91 observations.

### Test performance

Overall accuracy was 0.670.

Class specific results were:

| Risk level | Precision | Recall | F1 score |
| --- | ---: | ---: | ---: |
| High Risk | 0.812 | 0.565 | 0.667 |
| Low Risk | 0.635 | 1.000 | 0.777 |
| Mid Risk | 1.000 | 0.048 | 0.091 |

The overall accuracy therefore hides important differences between classes. The model identified Low Risk well, but it missed 10 of 23 High Risk observations and identified only 1 of 21 Mid Risk observations correctly.

## Error analysis

The main observed errors were:

- Mid Risk predicted as Low Risk: 17 cases
- High Risk predicted as Low Risk: 10 cases
- Mid Risk predicted as High Risk: 3 cases

High Risk observations missed by the model had substantially lower mean BS values than High Risk observations that were correctly identified, suggesting that High Risk cases without strongly elevated BS were more difficult for this baseline model to distinguish.

## Duplicate sensitivity analysis

Duplicate handling is one of the main methodological uncertainties in this dataset. The primary model uses unique rows to reduce the possibility that identical records are present in both train and test data. As a robustness check, the same Logistic Regression pipeline was evaluated on the 1,012 observations remaining after removing only HeartRate = 7. Repeated rows were retained, while identical six predictor profiles were kept in the same fold with five fold StratifiedGroupKFold validation.

No identical predictor profile appeared in both training and test data in any fold. Across the five grouped folds, mean Accuracy was **0.607 ± 0.029**, mean Macro F1 was **0.593 ± 0.032**, mean High Risk Recall was **0.695 ± 0.057**, mean Mid Risk Recall was **0.330 ± 0.139**, and mean Low Risk Recall was **0.777 ± 0.064**.

Compared with the primary holdout analysis, the grouped sensitivity analysis produced lower average Accuracy but higher Macro F1 and higher recall for both High Risk and Mid Risk. These values should not be interpreted as a direct model competition because the two analyses use different sample definitions and validation procedures. Instead, they show that estimated performance is materially sensitive to how repeated observations are handled. Because patient identifiers are unavailable, neither duplicate removal nor duplicate retention can be regarded as definitively correct.

## Model interpretation

For the High Risk class, the largest standardized Logistic Regression coefficient was associated with BS, followed by BodyTemp and SystolicBP.

Approximate coefficients were:

- BS: 1.176
- BodyTemp: 0.524
- SystolicBP: 0.345
- DiastolicBP: 0.178
- HeartRate: 0.166
- Age: -0.214

These coefficients describe predictive relationships in the fitted model and should not be interpreted as causal clinical effects.

## Repository structure

```text
maternal-health-risk-stratification/
├── README.md
├── requirements.txt
├── data/
│   └── README.md
├── notebooks/
│   └── maternal_health_risk_analysis.ipynb
├── figures/
│   ├── 01_risk_distribution.png
│   ├── 02_age_by_risk.png
│   ├── 03_blood_sugar_by_risk.png
│   ├── 04_systolic_bp_by_risk.png
│   ├── 05_standardized_mean_differences.png
│   ├── 06_confusion_matrix.png
│   └── 07_high_risk_coefficients.png
└── report/
    ├── maternal_health_risk_report.pdf
    ├── maternal_health_risk_report.docx
    └── maternal_health_risk_short_summary.docx
```

## Reproducing the analysis

1. Download the UCI dataset and place the CSV in the `data` folder using the filename shown in `data/README.md`.
2. Install dependencies:

```bash
python -m pip install -r requirements.txt
```

The pinned package versions in `requirements.txt` document the environment used for the packaged project.

3. Open `notebooks/maternal_health_risk_analysis.ipynb` in Jupyter or VS Code.
4. Restart the kernel and run the notebook from beginning to end.

## Limitations

The dataset is small and contains only six predictor variables. Patient identifiers are unavailable, the source contains many repeated records, and identical predictor profiles can have different labels. The primary performance estimate comes from one held out test split, while the five fold grouped sensitivity analysis provides a robustness assessment rather than external validation. The target is an existing risk category rather than an independently observed future clinical outcome.

## Author

Atefe Asadi
