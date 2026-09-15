# Data

This project uses the UCI Maternal Health Risk dataset.

Source: https://archive.ics.uci.edu/dataset/863/maternal+health+risk

DOI: https://doi.org/10.24432/C5DP5D

License: CC BY 4.0

The raw CSV is not included in this repository package. Download the dataset from the UCI source and save it as:

`data/Maternal Health Risk Data Set.csv`

The analysis notebook searches for this relative path.

The project CSV used during development contained 1,014 rows, while the current UCI metadata page reports 1,013 instances. The analysis reports the dimensions of the actual file used rather than silently replacing them with the metadata count.

Two observations with HeartRate = 7 bpm were treated as implausible. In the primary modeling analysis, exact repeated rows were removed as an explicit analytical assumption. The notebook also includes a sensitivity analysis that retains repeated rows while using a grouped split so that identical predictor profiles do not appear in both training and test data.
