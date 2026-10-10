# Anomaly Detection in 4G Cellular Networks

## 1. Problem Statement

Detect unusual behavior in 4G cellular network traffic using performance and resource-utilization metrics. The goal is to identify anomalous network activity from labeled cellular data and generalize predictions to previously unseen cells.

## 2. Approach

1. **Data Loading:** Loaded the training and test datasets and inspected their structure.
2. **Data Preprocessing:** Checked missing values, duplicates, invalid values, feature distributions, and train–test consistency.
3. **Exploratory Data Analysis:** Studied anomaly distribution, feature correlations, cell-wise patterns, and differences between normal and unusual records.
4. **Feature Engineering:** Converted `Time` into minutes for experiments. Excluded `Time` and `CellName` from the final numerical feature set.
5. **Model Training:** Trained Logistic Regression, Random Forest, Extra Trees, and HistGradientBoosting models.
6. **Evaluation:** Used ROC-AUC and PR-AUC, with group-based validation to evaluate generalization to unseen cells.
7. **Prediction:** Trained the final HistGradientBoosting model on all labeled training data and generated anomaly probabilities for the test dataset.

## 3. Results

The HistGradientBoosting model performed best among the evaluated models.

| Metric | Mean across 5 validation splits |
|---|---:|
| ROC-AUC | 0.9730 |
| PR-AUC | 0.9651 |

- Validation was performed using cell-based group splits, ensuring that cells in the validation set were not present in the corresponding training split.
- The model retained strong performance after excluding records with specific suspicious `meanUE_UL` values.
- Final predictions were generated for all **9,158 test records**.

## 4. Dataset Overview

The dataset contains cellular network measurements collected at different times for multiple 4G cells.

- **Training records:** 36,904
- **Test records:** 9,158
- **Cell identifiers:** 33
- **Target column:** `Unusual` (available only in training data)

### Column Descriptions

| Column | Description |
|---|---|
| `Time` | Time of the measurement, recorded at 15-minute intervals. |
| `CellName` | Identifier of the cellular network cell. |
| `PRBUsageUL` | Physical Resource Block usage for uplink traffic. |
| `PRBUsageDL` | Physical Resource Block usage for downlink traffic. |
| `meanThr_DL` | Mean downlink throughput. |
| `meanThr_UL` | Mean uplink throughput. |
| `maxThr_DL` | Maximum downlink throughput. |
| `maxThr_UL` | Maximum uplink throughput. |
| `meanUE_DL` | Mean number of user-equipment units associated with downlink measurements. |
| `meanUE_UL` | Mean number of user-equipment units associated with uplink measurements. |
| `maxUE_DL` | Maximum number of user-equipment units for downlink. |
| `maxUE_UL` | Maximum number of user-equipment units for uplink. |
| `maxUE_UL+DL` | Combined maximum user-equipment count for uplink and downlink. |
| `Unusual` | Target label: `1` indicates unusual behavior and `0` indicates normal behavior. |

*Note: Feature definitions follow the dataset column names and their apparent meanings; consult the original competition documentation for exact measurement units and calculation methods.*

## 5. Exploratory Data Analysis

The following analyses were performed:

- Checked dataset dimensions, data types, missing values, invalid values, and duplicate records.
- Examined the distribution of normal and unusual records.
- Compared numerical feature distributions across the training and test datasets.
- Analysed correlations between network metrics to identify redundant features.
- Investigated anomaly rates across different cells.
- Compared feature distributions between normal and unusual records.
- Identified unusual label patterns associated with specific `meanUE_UL` values.
- Used statistical tests to investigate potential train–test distribution shifts, including within individual cells.

## 6. Models Trained

| Model | Purpose |
|---|---|
| Logistic Regression | Baseline classification model. |
| Random Forest | Captured nonlinear relationships and feature interactions. |
| Extra Trees | Evaluated an alternative tree-ensemble approach. |
| HistGradientBoosting | Final model; achieved the strongest validation performance. |

**Evaluation strategy:** `GroupShuffleSplit` was used with `CellName` as the grouping variable. Five splits were evaluated to reduce dependence on a single train–validation split.

The final HistGradientBoosting configuration used `max_iter=200`, `max_leaf_nodes=31`, `learning_rate=0.1`, and `random_state=42`.

## 7. Conclusion

The experiments show that tree-based ensemble methods can effectively identify unusual patterns in 4G cellular network measurements. HistGradientBoosting achieved a mean ROC-AUC of **0.9730** and mean PR-AUC of **0.9651** across five unseen-cell validation splits.

Group-based validation provides a more realistic test of generalization to cells not observed during training. However, validation performance does not guarantee the same performance on the unlabeled test set or in a live network.

## 8. Future Scope

- Investigate feature importance and explain individual anomaly predictions.
- Tune model hyperparameters and evaluate additional boosting algorithms.
- Study temporal patterns using chronological data if timestamps spanning multiple dates become available.
- Calibrate predicted probabilities and select a decision threshold based on operational requirements.
- Evaluate the model on independently labeled network data.
- Develop a real-time monitoring interface to flag and investigate anomalous network behavior.
