# Estimating Motor Winding Temperature Using Machine Learning

### A Machine Learning Virtual Sensor for a Permanent Magnet Synchronous Motor (PMSM)

**Course:** UCS321 – AI for Engineers
**Institution:** Thapar Institute of Engineering & Technology, Patiala
**Project:** MST Mini Project 1
**Date:** October 2026

---

## 1. Project Overview

Electric motors generate heat during operation, particularly in their stator windings. Excessive winding temperature can damage insulation and reduce motor reliability. However, directly measuring winding temperature can be difficult and expensive in practical applications.

This project develops a **machine learning-based virtual sensor** that estimates stator winding temperature using electrical, mechanical, and ambient measurements that are easier to obtain.

We formulate the task as a supervised regression problem using data from a Permanent Magnet Synchronous Motor (PMSM).

The objective is to develop and compare different regression models and identify a model that provides accurate predictions on previously unseen motor test runs.

## 2. Objectives

- Explore the motor temperature dataset and understand its characteristics.
- Perform exploratory data analysis (EDA).
- Preprocess the data and select relevant input features.
- Prevent data leakage during feature selection and model evaluation.
- Develop baseline, linear, polynomial, and advanced regression models.
- Evaluate models using group-aware cross-validation.
- Tune model hyperparameters using GridSearchCV.
- Compare prediction accuracy, generalisation, and computational cost.
- Interpret the results and identify possible improvements.

## 3. Dataset

The project uses the Electric Motor Temperature dataset recorded on a test bench by the LEA department at Paderborn University.

| Property                | Description           |
| ----------------------- | --------------------- |
| Dataset file            | `measures_v2.csv`     |
| Number of observations  | 1,330,816             |
| Number of columns       | 13                    |
| Number of test profiles | 69                    |
| Prediction target       | `stator_winding`      |
| Problem type            | Supervised regression |

### Input Features

The final models use seven input features:

- `u_q` – q-axis voltage
- `u_d` – d-axis voltage
- `i_d` – d-axis current
- `i_q` – q-axis current
- `motor_speed` – Motor rotational speed
- `torque` – Motor torque
- `ambient` – Ambient temperature

**Target variable:** `stator_winding`, representing stator winding temperature in degrees Celsius.

The columns `stator_tooth`, `stator_yoke`, and `pm` were excluded because they contain other motor temperatures that could introduce data leakage. `coolant` was also excluded to keep the model inputs aligned with the project problem statement.

The `profile_id` column identifies individual test runs and is used for data splitting rather than as a model input.

> **Dataset note:** The original dataset is not included in this repository by default. Place `measures_v2.csv` in the expected data directory before running the notebooks. Check the dataset's original source for access and licensing information.

## 4. Project Workflow

The project follows a structured machine learning workflow:

1. **Exploratory Data Analysis:** Examine data distributions, missing values, correlations, and temperature patterns.
2. **Data Preprocessing:** Select input features, exclude leakage-prone columns, and prepare training and testing data.
3. **Baseline and Linear Models:** Establish baseline performance and evaluate linear regression methods.
4. **Feature Engineering:** Explore polynomial features and the effect of squared current magnitude.
5. **Model Benchmarking:** Compare different regression algorithms.
6. **Cross-Validation:** Use GroupKFold to evaluate generalisation across different motor test profiles.
7. **Hyperparameter Tuning:** Use GridSearchCV to find suitable model configurations.
8. **Final Evaluation:** Compare tuned models on held-out test profiles.
9. **Error Analysis:** Study prediction errors, actual-versus-predicted temperatures, and differences across test profiles.

## 5. Data Leakage Prevention and Validation

Motor measurements are recorded sequentially, so neighbouring observations from the same test run can be highly similar. A random row-wise split could allow information from nearly identical observations to appear in both training and testing data.

To reduce this risk, the project uses a group-aware split based on `profile_id`.

| Dataset split |      Rows | Profiles |
| ------------- | --------: | -------: |
| Training      | 1,026,423 |       55 |
| Testing       |   304,393 |       14 |

No test profile is shared with the training set.

StandardScaler is used where appropriate, with scaling fitted on training data through scikit-learn pipelines. Group-aware cross-validation is used during model evaluation and hyperparameter tuning.

## 6. Models Evaluated

The project investigates the following model families:

- Mean baseline
- Linear Regression
- Ridge Regression
- Lasso Regression
- Polynomial Regression
- Polynomial Regression with Ridge regularisation
- Support Vector Regression (SVR)
- Decision Tree
- Random Forest
- Extra Trees
- AdaBoost
- Gradient Boosting
- HistGradientBoosting
- Multi-Layer Perceptron (MLP) Neural Network
- XGBoost

Not every model was trained on the full dataset. Some algorithms were evaluated on smaller samples because of computational requirements.

## 7. Evaluation Metrics

The following metrics are used to assess regression performance:

- **MAE (Mean Absolute Error):** Average absolute prediction error, measured in °C. Lower is better.
- **RMSE (Root Mean Squared Error):** Penalises larger prediction errors more strongly. Lower is better.
- **R² Score:** Measures the proportion of target variance explained by the model. Higher is generally better.

Training and testing scores are also compared to assess generalisation and possible overfitting.

## 8. Final Results

The following results were obtained by evaluating tuned models on 14 held-out motor test profiles.

| Model                         | Train R² |   Test R² | Test MAE (°C) | Test RMSE (°C) |
| ----------------------------- | -------: | --------: | ------------: | -------------: |
| MLP Neural Network            |    0.869 | **0.811** |      **9.07** |      **13.19** |
| Polynomial (degree 3) + Ridge |    0.769 |     0.762 |          9.80 |          14.82 |
| HistGradientBoosting          |    0.728 |     0.637 |         14.20 |          18.27 |

### Best Performing Model

The tuned MLP Neural Network achieved the best overall test performance.

- **Test R²:** 0.811
- **Test MAE:** 9.07 °C
- **Test RMSE:** 13.19 °C

The model explains approximately 81.1% of the variation in winding temperature on the held-out test data.

### Simpler Alternative

Polynomial Regression (degree 3) with Ridge regularisation achieved a test R² of 0.762 and a test MAE of 9.80 °C.

It provides a useful alternative when faster training, simpler analysis, or lower computational cost is important.

*The reported results are specific to this dataset and the selected held-out profiles; they should not be interpreted as a guarantee of real-world motor performance.*

## 9. Feature Engineering Experiment

The project also evaluates an engineered feature representing total current magnitude squared:

`i_squared = i_d² + i_q²`

The additional feature produced small improvements in the linear and Ridge models. The polynomial models already include squared terms and did not show a consistent improvement from adding the feature.

Therefore, the final model comparison retains the original seven input features.

## 10. Key Findings

- Linear models provide a useful baseline but struggle to capture nonlinear temperature relationships.
- Polynomial features improve performance by representing nonlinear effects associated with motor current and operating conditions.
- The tuned MLP Neural Network achieves the highest test R² among the final evaluated models.
- Polynomial Regression with Ridge regularisation offers competitive performance with lower computational cost.
- Group-aware data splitting provides a more realistic evaluation on unseen test runs.
- Prediction accuracy varies across profiles, and some operating conditions remain more difficult to predict.

## 11. Limitations and Future Work

### Limitations

- The models estimate temperature from individual measurements and do not explicitly model thermal history.
- Accuracy varies across motor test profiles.
- Very high temperatures are less common in the dataset.
- Training the MLP Neural Network requires substantially more computational time than training the polynomial model.
- Evaluation on held-out test profiles does not establish generalisation to every motor, vehicle, or operating environment.

### Future Improvements

- Add time-history features such as rolling averages of current and speed.
- Investigate sequence-based models for capturing thermal dynamics.
- Evaluate performance more carefully on high-temperature operating conditions.
- Improve error analysis for difficult test profiles.
- Validate the models using additional datasets and real-world operating conditions.

## 12. Team Contributions

| Team Member     | Responsibility                                                            |
| --------------- | ------------------------------------------------------------------------- |
| Lakshya Gupta   | Exploratory Data Analysis                                                 |
| Atharva Pareek  | Data Preprocessing                                                        |
| Vinayak Singh   | Baseline and Linear Models; additional optimisation and evaluation work   |
| Mannanjit Singh | Initial model benchmarking, optimisation and evaluation                   |
| Namit Mehta     | Result interpretation, visualisation, report compilation and presentation |

The team collaborated on the overall machine learning workflow, model comparison, and final project report.

## 13. Repository Structure

The repository is organised as follows:

```text
motor-temperature-estimation/
├── README.md
├── data/
│   └── measures_v2.csv
├── notebooks/
│   ├── eda.ipynb
│   ├── preprocessing.ipynb
│   └── Person3_Person4_Models.ipynb
├── results/
│   ├── person3_linear_results.csv
│   ├── person4_cv_results.csv
│   ├── person4_tuning_results.csv
│   ├── person4_final_test_results.csv
│   └── engineered_feature_test.csv
└── report/
    └── Motor_Temperature_Project_Report.pdf
```

## 14. Technologies Used

- Python
- Jupyter Notebook / Google Colab
- pandas
- NumPy
- scikit-learn
- Matplotlib
- Seaborn

## 15. How to Run the Project

### Step 1: Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd motor-temperature-estimation
```

### Step 2: Install dependencies

```bash
pip install numpy pandas scikit-learn matplotlib seaborn jupyter
```

Install any additional libraries required by the notebooks.

### Step 3: Prepare the dataset

Obtain the original dataset and place `measures_v2.csv` at the path expected by the notebooks.

### Step 4: Open the notebooks

```bash
jupyter notebook
```

Run the notebooks in the appropriate order:

1. `eda.ipynb`
2. `preprocessing.ipynb`
3. `Person3_Person4_Models.ipynb`

Check the file paths in each notebook before executing it.

## 16. References

1. Kirchgässner, W., Wallscheid, O., and Böcker, J. *Electric Motor Temperature* dataset, Paderborn University (LEA), available on Kaggle.
2. Pedregosa, F. et al. *Scikit-learn: Machine Learning in Python*. Journal of Machine Learning Research, 12, 2825–2830, 2011.
3. Official scikit-learn documentation for GroupKFold, GroupShuffleSplit, GridSearchCV, PolynomialFeatures, Ridge, Lasso, MLPRegressor, and HistGradientBoostingRegressor.
4. McKinney, W. *Data Structures for Statistical Computing in Python*. Proceedings of the Python in Science Conference, 2010.
5. Hunter, J. D. *Matplotlib: A 2D Graphics Environment*. Computing in Science & Engineering, 9(3), 90–95, 2007.
6. UCS321 AI for Engineers course material and MST mini-project guidelines, Thapar Institute of Engineering & Technology.

---

## Acknowledgement

This project was completed as part of the UCS321 – AI for Engineers MST Mini Project 1 at Thapar Institute of Engineering & Technology, Patiala.

