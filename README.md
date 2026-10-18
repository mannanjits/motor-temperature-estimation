# Motor Temperature Prediction Using Machine Learning

## Project Overview

This project focuses on estimating the stator winding temperature of a Permanent Magnet Synchronous Motor (PMSM) using machine learning. The objective is to develop a virtual temperature sensor that predicts winding temperature from motor operating parameters.

## Objectives

- Perform Exploratory Data Analysis (EDA).
- Preprocess the motor measurement dataset.
- Train and compare different regression models.
- Evaluate model performance using R², MAE, and RMSE.
- Identify a suitable model for stator winding temperature estimation.

## Dataset

The project uses the `measures_v2.csv` dataset.

- **Rows:** 1,330,816
- **Columns:** 13
- **Profiles:** 69
- **Target variable:** `stator_winding`

### Download the Dataset

The dataset is hosted on Google Drive because the file is approximately 286 MB, exceeding GitHub's standard 100 MB file limit.

[**Download measures_v2.csv from Google Drive**](https://drive.google.com/file/d/111y8yCgJpIWBXK72eP6lkDzrxpqKaKWb/view?usp=drive_link)

After downloading, place the file inside the project's `data/` directory:

data/
└── measures_v2.csv


Keep the filename exactly as shown so the notebooks can locate it.

## Input Features

The final model uses these seven input features:

| Feature       | Description         |
| ------------- | ------------------- |
| `u_q`         | q-axis voltage      |
| `u_d`         | d-axis voltage      |
| `i_d`         | d-axis current      |
| `i_q`         | q-axis current      |
| `motor_speed` | Motor speed         |
| `torque`      | Motor torque        |
| `ambient`     | Ambient temperature |

The target is `stator_winding`. Other motor temperature measurements were excluded from the final model inputs to reduce target leakage. `profile_id` was used for grouping during data splitting rather than as a model input.

## Project Workflow

1. **EDA:** Explore dataset structure, distributions, relationships, and motor profiles.
2. **Preprocessing:** Prepare the features and target for modelling.
3. **Data splitting:** Use profile-based grouping to evaluate generalization to unseen profiles.
4. **Model training:** Train and compare regression algorithms.
5. **Evaluation:** Compare models using R², MAE, and RMSE.
6. **Feature engineering:** Test whether additional current-related features improve performance.

## Models Evaluated

- Baseline Regression
- Linear Regression
- Ridge Regression
- Lasso Regression
- Polynomial Regression with Ridge
- Support Vector Regression (SVR)
- Decision Tree and ensemble models
- Histogram-based Gradient Boosting
- Multi-Layer Perceptron (MLP)

## Final Model Results

| Model                         | Test R² | Test MAE (°C) | Test RMSE (°C) |
| ----------------------------- | ------: | ------------: | -------------: |
| Tuned MLP                     |   0.811 |          9.07 |          13.19 |
| Polynomial Regression + Ridge |   0.762 |          9.80 |          14.82 |
| HistGradientBoosting          |   0.637 |         14.20 |          18.27 |

The tuned MLP achieved the best test performance among the models listed above.

## Repository Structure

motor-temperature-prediction/
├── README.md
├── .gitignore
├── notebooks/
│   ├── eda.ipynb
│   ├── preprocessing.ipynb
│   └── Person3_Person4_Models.ipynb
├── results/
│   ├── engineered_feature_test.csv
│   ├── person3_linear_results.csv
│   ├── person4_cv_results.csv
│   ├── person4_final_test_results.csv
│   ├── person4_tuning_results.csv
│   └── results_old/
└── report.pdf


The dataset is not stored in this repository. Download it separately using the Google Drive link above.

## Installation and Usage

### 1. Clone the Repository

git clone https://github.com/mannanjits/motor-temperature-estimation.git
cd motor-temperature-estimation


### 2. Create a Virtual Environment (Optional)

python3 -m venv .venv
source .venv/bin/activate

### 3. Install Dependencies

pip install numpy pandas matplotlib seaborn scikit-learn jupyter xgboost

### 4. Download the Dataset

Download `measures_v2.csv` from Google Drive and save it to `data/measures_v2.csv`.

### 5. Launch Jupyter Notebook

jupyter notebook


Run the notebooks in the appropriate order: EDA, preprocessing, then model development and evaluation.

## Limitations

- Prediction accuracy may vary across motor profiles.
- Rare high-temperature observations can be harder to predict.
- MLP training can be computationally expensive and may produce convergence warnings.
- The models do not explicitly incorporate thermal history or time-dependent heat transfer.

## Future Improvements

- Investigate time-aware features and thermal history.
- Improve generalization to unseen motor profiles.
- Further tune model hyperparameters.
- Explore lightweight models for real-time deployment.

## Team Contributions

- **Lakshya Gupta:** Exploratory Data Analysis
- **Atharva Pareek:** Data Preprocessing
- **Vinayak Singh:** Baseline and linear models, optimization, and evaluation
- **Mannanjit Singh:** Model benchmarking, optimization, and evaluation
- **Namit Mehta:** Result interpretation, visualizations, report, and presentation

## Acknowledgement

This project was developed as part of the UCS321 AI for Engineers mini-project on estimating motor winding temperature using machine learning.
