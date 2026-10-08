# Predicting Parkinson's Disease Severity from Biomedical Voice Measurements
A machine learning project investigating whether biomedical voice measurements can predict motor symptom severity in people with Parkinson's disease.

## Project overview
Parkinson's disease is a progressive neurological disorder that can affect movement, speech and other aspects of daily functioning. Changes in speech and voice may provide measurable information related to disease severity, creating potential opportunities for non-invasive symptom monitoring.
This project investigates whether biomedical voice measurements can be used to predict **Motor UPDRS**, a clinical measure of motor symptom severity.
The analysis compares a simple mean baseline with **Linear Regression** and **Random Forest Regression**. Because the dataset contains repeated recordings from the same participants, participant-grouped validation is used to prevent recordings from the same individual appearing in both the training and validation sets.

### Research Question
**Can biomedical voice features predict the severity of Parkinson's disease motor symptoms, as measured by Motor UPDRS?**

## Dataset
The project uses the **Parkinson's Telemonitoring Dataset** from the UCI Machine Learning Repository. It contains **5,875 voice recordings from 42 participants** with early-stage Parkinson's disease collected during a six-month telemonitoring study.
Each observation contains biomedical voice measurements alongside participant information and clinical measures of Parkinson's disease severity.
**Target variable:** 'motor_UPDRS'
**Dataset:** [UCI Parkinson's Telemonitoring Dataset](https://archive.ics.uci.edu/dataset/189/parkinson)
**DOI:** [10.24432/C5ZS3N](https://doi,org/10.24432/C5ZS3N)

## Methodology

### 1. Exploratory Data Analysis
The dataset was explored to understand participant demographics, the distribution of Motor UPDRS scores and the relationships between biomedical voice measurements and symptom severity.

Correlational analysis was used to investigate linear relationships between voice features and Motor UPDRS. Highly redundant measurements were also identified. 'Jitter:DDP' and 'Shimmer:DDA' were removed because they contained near-duplicate information from 'Jitter:RAP' and 'Shimmer:APQ3', respectively.

This resulted in **14 biomedical voice features** being used for modelling.

## 2. Participant-Grouped Validation
A key challenge in the dataset is that each participant contributed multiple voice recordings. A random row-level train/test split could therefore place recordings from the same person in both sets, potentially causing data leakage.
To address this, participant identity ('subjects#') was used as a grouping variable. Model evaluation used:

- A participant-grouped train/test split
- 5-fold 'GroupKFold' cross-validation
- No participant overlap between training and validation data

### 3. Models
Three approaches were compared:

- **Mean Baseline** - predicts the Motor UPDRS value from the training data
- **Linear Regression** - provides a simple linear regression benchmark
- **Random Forest Regression** - investigates whether non-linear relationships and interactions between voice features improve prediction

Performance was evaluated using **Mean Absolute Error (MAE)**, **Root Mean Squared Error (RMSE)** and **R²**.

Random Forest hyperparameter tuning was also performed using participant-grouped cross-validation.

## Results

## 5-Fold Participant-Grouped Cross-Validation

| Model | MAE ↓ | RMSE ↓ | R² ↑ |
| --- | ---: | ---: | ---: |
| Mean Baseline | 7.19 ± 1.07 | 8.32 ± 0.82 | -0.229 ± 0.273 |
| Linear Regression | 7.37 ± 1.03 | 8.72 ± 1.26 | -0.347 ± 0.343 |
| Random Forest | 7.45 ± 0.67 | 8.90 ± 0.58 | -0.418 ± 0.356 |

The mean baseline achieved the best averaged performance across the grouped cross-validation folds. Neither Linear Regression nor Random Forest consistently improved upon the baseline when predicting Motor UPDRS for unseen participants.
This is an important result: although the dataset contains thousands of voice recordings, these recordings come from only 42 individuals. Evaluating models on unseen participants produced substantially more challenging and realistic estimated of generalisation than allowing recordings from the same participant to appear in both training and validation data.

### Random Forest Hyperparameter Tuning
A grouped grid search was used to investigate whether controlling Random Forest complexity could improve generalisation.

On the held-out test participants, the tuned Random Forest achieved:

- **MAE:** 6.81
- **RMSE:** 8.02
- **R²:** -0.102
These results were very similar to the original Random Forest (MAE 6.76, RMSE 8.03, R² -0.105), indicating that modest hyperparameter tuning did not meaningfully improve performance.

### Feature Importance
Random Forest feature-importance analysis identified **DFA, HNR, PPE, Jitter(Abs) and RPDE** as the most influential features within the fitted model.
These importance values should be interpreted cautiously. The Random Forest did not demonstrate strong generalisation to unseen participants and importance within a fitted model does not establish that a feature is independently associated with Parkinson's symptom severity.

## Key Findings
- Individual biomedical voice features generally showed weaker linear relationships with Motor UPDRS.
- Preventing participant-level data leakage was important because the dataset contained repeated recordings from the same 42 participants.
- Neither Linear Regression nor Random Forest consistently outperformed the mean baseline during participant-grouped cross-validation.
- Random Forest hyperparameter tuning did not meaningfully improve generalisation to unseen participants.
- The results do not demonstrate that voice measurements have no predictive value but the models investigated did not provide reliable predictions for unseen participants.

## Limitations
- Although the dataset contains 5,875 recordings, these were collected from only 42 participants, limiting the number of independent individuals available for training and evaluation.
- The participant sample was relatively small and demographically imbalanced and consisted of people with early-stage Parkinson's disease.
- Only Linear Regression and Random Forest were investigated.
- The model did not explicitly model changes in symptom severity over time within individual participants.
- Motor UPDRS values associated with the recordings were derived using interpolation between clinical assessments.
- The models were not externally validated on an independent dataset.

## Technologies
- Python
- pandas
- NumPy
- Matplotlib
- schikit-learn
- Google Colab

## How to run
1. Download or clone this repository.
2. Download the Parkinsons Telemonitoring dataset from the UCI Machine Learning Repository.
3. Place parkinsons_updrs.data in the same working directory as the notebook.
4. Open parkinsons_voice_prediction.ipynb in Google Colab or Jupyter Notebook.
5. Run the notebook cells from top to bottom.

## Repository Structure
- 'README.md' - Project overview. methodology, results and instructions.
- 'parkinsons_voice_prediction.ipynb' - Complete data analysis and machine-learning notebook.

## Furtherwork
Future work could investigate larger and more diverse participant samples, external validation datasets, alternative machine-learning approaches and models designed specifically for longitudinal data. These approaches could help determine whether changes in voice measurements can contribute to reliable, non-invasive monitoring of Parkinson's motor symptom severity.
