# Revision notes

This revision adds three methodological and reproducibility improvements:

1. A duplicate sensitivity analysis using `StratifiedGroupKFold`, with identical predictor profiles kept within a single fold.
2. A complete Q8 interpretation and final conclusion in the notebook.
3. Exact package versions in `requirements.txt`, plus synchronized repository filenames and instructions.

The primary model and its previously reported results were not replaced. The new sensitivity section is an additional robustness check and should be populated by running the notebook from a clean kernel with the source CSV.
