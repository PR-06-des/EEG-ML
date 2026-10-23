# Phantom noise encoding

A Jupyter notebook that builds a first Random Forest model for predicting
`hasTinnitus` from the Kaggle EEG feature dataset.

## Setup

1. Open `Phantom noise encoding.ipynb` in Jupyter or VS Code with the project
   folder as the working directory.
2. Run the first code cell, `%pip install -r requirements.txt`, to install
   the libraries into the notebook's Python environment. Restart the kernel
   if packages were upgraded, then run the remaining cells in order.
3. Each collaborator must download the Kaggle dataset separately. Extract it
   into this layout; `pip` does not download the EEG data:

```text
EEG-ML/
  Phantom noise encoding.ipynb
  requirements.txt
  data/decoding-phantom-sounds/
    Training/Training/<participant ID>/...
    Test/Test/<participant ID>/...
    tinnitus_train_upload.csv
    sample_submission.csv
```

The requirements file installs NumPy, pandas, Matplotlib, and scikit-learn.
Jupyter or the VS Code Jupyter extension must already be available.
The data and generated outputs are excluded from Git.
Only the power CSVs are read for this baseline; the larger connectivity files
are not loaded into memory.

## What the notebook does

- Checks participant IDs against labels and reports missing files. The current
  archive has 455 label rows and 450 training folders; the five missing
  participants are reported and excluded, without changing the source data.
- Starts with frequency-band power features only. Each named EEG feature is
  summarized by its mean and population standard deviation across the available
  columns, giving one row per participant. Other feature families are unused.
- Excludes `eyes`, `site`, `subject_id`, participant IDs, and `center` from the
  predictors. Feature names in the first CSV column become the index.
- Reserves 20% of participants for validation, preserving the class proportions.
  Missing feature values are filled with medians learned only from the fitting
  participants. A fixed random seed makes the split repeatable.
- Compares a Random Forest with a majority-class baseline using accuracy,
  balanced accuracy, and ROC AUC, and shows the confusion matrix.
- Fits a fresh model on all available labeled participants and writes predictions
  for the 112 test participants in the sample submission's order.

## Generated files

Running all cells creates `outputs/` containing the missing-file report,
validation scores, validation predictions, and `submission.csv`.
The submission contains model predictions; the example zeros in
`sample_submission.csv` are placeholders, not test labels. Nothing is uploaded.

## Initial run

The verified run used 450 participants (225 per class), with 360 for fitting
and 90 for validation. Mean and standard-deviation summaries produced 1,584
features. Because channel names and availability differ across recordings,
53.3% of the combined feature table was missing before imputation.

| Model | Validation accuracy | Balanced accuracy | ROC AUC |
| --- | --- | --- | --- |
| Random Forest | 0.60 | 0.60 | 0.62 |
| Majority class | 0.50 | 0.50 | 0.50 |

All 112 test predictions were exported in the required ID order. The modeling
cells were executed with Python 3.14.7, NumPy 2.5.3, pandas 3.0.6,
Matplotlib 3.11.2, and scikit-learn 1.9.1. The installation cell was checked
but skipped because these libraries were already installed. Notebook outputs
contain the results of that run.

This is an initial baseline using one fixed validation split. Validation centers
may also appear in the fitting set, and differences in recorded channels can
reflect acquisition sites. Results do not establish performance at a new center
or clinical usefulness. Use repeated and center-held-out evaluation before
drawing research conclusions. Package versions are not pinned, so results can
vary across environments.
