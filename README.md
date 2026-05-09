# Crime-Classifier
This project builds a K-Nearest Neighbours (KNN) multiclass classifier to predict 
crime type from incident data published by the Vancouver Police Department (VPD). 
The goal is to determine whether the timing and geographic location of a crime are meaningful predictors for distinguishing between crime categories for resource allocation and patrol scheduling.

Model performance is evaluated on a test set to ensure results generalize to 
unseen data, and parameter tuning is performed via cross-validation to select the 
optimal `k`.

---

## Data Source

| Field | Details |
|---|---|
| **Provider** | Vancouver Police Department (VPD) GeoDASH Open Data |
| **Dataset** | Crime incidents from 2003 to present, updated weekly |
| **URL** | https://geodash.vpd.ca/opendata/ |

---

## Features

| Feature | Description |
|---|---|
| `NEIGHBOURHOOD` | Vancouver neighbourhood where the incident occurred |
| `HOUR` | Hour of day (0–23) |
| `MONTH` | Month of year (1–12) |
| `DAY_OF_WEEK` | Day of week derived from date |
| `YEAR` | Year of incident |


### Target Variable

`TYPE` — Crime category, including: Theft from Vehicle, Other Theft, Offence Against 
a Person, Break and Enter (Commercial/Residential), Mischief, Theft of Vehicle, 
Theft of Bicycle, Homicide, and Vehicle Collision (with Injury/Fatality).

---

## Methodology

1. **Data loading and exploration** — Examined class distributions, missing values, 
   and feature characteristics across the full dataset.

2. **Data cleaning and sampling** — Removed incomplete records/observations and converted categorical variables to numerical   representations for use in the KNN algorithm. A 3% sample was taken from the original dataset. All numerical data was then scaled and centred.

3. **Train/test split** — Data was partitioned into training and test sets.

4. **Hyperparameter tuning** — A `tune_grid()` search over a range of `k` values was 
   run using v-fold cross-validation on the training set. 

5. **Model training** — The optimal value of `k = 96` (rectangular weight function) was 
   selected based on the accuracy curve, and the final model was fit on the full training set.

6. **Evaluation** — Predictions were generated on the test set (6,851 observations) and 
   assessed using overall accuracy and a full confusion matrix across all crime classes.

---

## Results

### Overall Accuracy

| Metric | Estimator | Value |
|---|---|---|
| Accuracy | Multiclass | **38.4%** |

### Confusion Matrix Highlights

- The model correctly identifies patterns primarily in the three most frequent classes: 
  **Other Theft**, **Theft from Vehicle**, and **Offence Against a Person**.
- Rare classes, including **Homicide**, **Theft of Bicycle**, **Theft of Vehicle**, 
  and both **Vehicle Collision** categories received zero correct predictions, as 
  they are underrepresented and dominated by majority-class neighbours.
- A warning was raised during cross-validation, noting no `Homicide` observations in some folds. Those levels were excluded from metric computation for those folds.

---

## Discussion

### Is 38.4% meaningful?

With 11 crime classes, a random classifier would achieve approximately ~9% 
accuracy. At 38.4%, this model performs over 4× better than chance, suggesting 
that temporal and neighbourhood features carry predictive abilities.

However, 38.4% remains low for a classifier, and this is expected 
given the feature constraints available in the dataset. Specifically, crime type is influenced by many factors 
beyond time and location, such as victim/suspect profiles and 
situational context.

### Limitations & Future Work

- **Class imbalance** — Dominant classes dominate rare ones. 
- **Feature expansion** — Adding features such as socioeconomic indicators would likely yield an increase in accuracy.

---

## Tools & Libraries

| Tool | Purpose |
|---|---|
| **R** | Primary programming language |
| **JupyterLab** | Development environment |
| **tidyverse** | Data wrangling and visualization |
| **caret / tidymodels** | Model training and cross-validation |
| **class** | KNN implementation |
| **ggplot** | Accuracy and confusion matrix plots |

---