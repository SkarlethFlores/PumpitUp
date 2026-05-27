# 💧 Pump It Up: Data Mining the Water Table

> **Predicting the operational status of water pumps in Tanzania using machine learning in R**

This repository contains a proposed solution for the [DrivenData Pump It Up competition](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/), developed as part of the *Big Data Applications in Business* course at Universidad Complutense de Madrid (UCM), October 2023.

---

## 📋 Table of Contents

- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Methodology](#methodology)
- [Results](#results)
- [How to Run](#how-to-run)
- [Dependencies](#dependencies)
- [Author](#author)

---

## 🎯 Problem Statement

The goal is to predict the **operating condition** of water points across Tanzania. Each pump is classified into one of three states:

| Label | Description |
|---|---|
| `functional` | The pump is working properly |
| `functional needs repair` | The pump works but needs maintenance |
| `non functional` | The pump is not operational |

Accurate predictions help prioritize maintenance efforts and ensure access to clean water for local communities.

---

## 📊 Dataset

Data sourced from [DrivenData](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/data/):

| File | Description |
|---|---|
| `Training_set_values.csv` | Feature data for ~59,000 training records |
| `Training_set_labels.csv` | Target labels for training records |
| `Test_set_values.csv` | Feature data for ~14,000 test records |
| `SubmissionFormat.csv` | Expected format for competition submissions |

The dataset contains ~50 variables (numeric and categorical) describing each pump, including geographic coordinates, installation details, management type, water quality, and more. Key challenges include:

- High-cardinality categorical variables (e.g., `funder`, `installer`, `wpt_name`)
- Missing values in several columns (`permit`, `public_meeting`, `construction_year`)
- Class imbalance in the target variable

---

## 📁 Project Structure

```
PumpitUp/
│
├── proyectoPumpitUp.Rmd       # Main R Markdown analysis notebook
├── proyectoPumpitUp.html      # Rendered HTML report
├── Imagen.html                # Supporting HTML visualization
├── hand_pump_diagram.png      # Reference diagram of a hand pump
│
├── Training_set_values.csv    # Training features
├── Training_set_labels.csv    # Training labels
├── Test_set_values.csv        # Test features
├── SubmissionFormat.csv       # Submission template
│
├── predi_fit_r1.csv           # Predictions from Iteration A (Ranger baseline)
├── predi_fit_r2.csv           # Predictions from Iteration B (Ranger improved)
├── predi_fit_r3.csv           # Predictions from Iteration C (further tuning)
│
└── README.md
```

---

## 🔬 Methodology

The project is structured in iterative rounds of exploratory analysis, feature engineering, and modeling.

### Iteration A — Baseline
- Exploratory Data Analysis (EDA) using `inspectdf`: correlations, missingness, distributions, class balance
- Minimal data cleaning: type corrections, removal of irrelevant columns (`recorded_by`, `num_private`)
- Basic feature engineering: date decomposition (`year`, `month`, `day`, `weekday`), high-cardinality lumping (`wpt_name` → named/none, `scheme_name` → known/unknown)
- Missing value imputation for `permit`, `public_meeting`, and `construction_year` (recoded as `"Desconocido"`)
- **Model: Random Forest (Ranger)** — 80/20 train/validation split, 100 trees, Gini criterion
- **Validation Accuracy: ~80.7%**

### Iteration B — Feature Engineering & Multi-model
- Date features converted to categorical type
- Outlier treatment for `amount_tsh` and `population` at the 98th percentile, imputed with training median
- Stricter lumping on remaining high-cardinality categoricals (threshold: < 2% frequency → `"Otros"`)
- Models evaluated:
  - Ranger without resampling → **~80.97%**
  - Ranger with stratified 5-fold CV → ~76.17%
  - GBM without resampling
  - GBM with stratified 5-fold CV → ~68.06%

### Iteration C
- Further tuning based on results from iterations A and B
- Submission predictions stored in `predi_fit_r3.csv`

---

## 📈 Results Summary

| Iteration | Model | Resampling | Validation Accuracy |
|---|---|---|---|
| A | Random Forest (Ranger) | None | ~80.7% |
| B | Random Forest (Ranger) | None | ~80.97% |
| B | Random Forest (Ranger) | Stratified 5-fold CV | ~76.17% |
| B | GBM | Stratified 5-fold CV | ~68.06% |

> Note: This project was not submitted to the live competition within the deadline.

---

## ▶️ How to Run

1. Clone this repository:
   ```bash
   git clone https://github.com/SkarlethFlores/PumpitUp.git
   cd PumpitUp
   ```

2. Open `proyectoPumpitUp.Rmd` in **RStudio**.

3. Make sure all required packages are installed (see [Dependencies](#dependencies)).

4. Click **Knit** to render the full HTML report, or run chunks interactively.

> The data files (`Training_set_values.csv`, `Training_set_labels.csv`, `Test_set_values.csv`) must be in the same working directory as the `.Rmd` file.

---

## 📦 Dependencies

This project uses **R** with the following packages:

```r
install.packages(c(
  "data.table", "dplyr", "caret", "scales", "ggplot2",
  "stringi", "stringr", "dataPreparation", "knitr", "kableExtra",
  "ggpubr", "tictoc", "ggeasy", "lubridate", "inspectdf",
  "questionr", "gbm", "ranger", "doMC", "car"
))
```

---

## 👤 Author

**Skarleth Motiño Flores**
Master's in Data Science — Universidad Complutense de Madrid (UCM)
Project submitted: October 2023

---

## 🔗 References

- DrivenData. (2015). *Pump it Up: Data Mining the Water Table*. Retrieved from https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/
