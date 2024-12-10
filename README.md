# League of Legends Role Prediction

# Introduction 

### Introduction to the Dataset

This project analyzes a dataset of professional League of Legends (LoL) esports matches, sourced from Oracle's Elixir. The dataset contains detailed post-game statistics for players and teams, spanning multiple tournaments, regions, and years. Each row represents either a player's or team's performance in a specific game, with over 16,000 rows in the cleaned dataset. The dataset provides a rich collection of features capturing gameplay elements such as kills, assists, vision control, gold earned, and damage dealt.

### Central Question

The project aims to address the question: **How can we predict a player's role (Top, Jungle, Mid, ADC, Support) based on their post-game statistics?** This question is significant because understanding the performance profiles of different roles can enhance strategic decisions in professional gameplay, talent scouting, and coaching.

### Why It Matters

League of Legends is one of the most popular esports titles globally, with a massive following and professional ecosystem. Fans, analysts, and teams can use insights from this analysis to:
1. Evaluate the strengths and weaknesses of specific players based on their role.
2. Understand role-specific performance metrics that correlate with match success.
3. Explore how different playstyles influence outcomes and team dynamics.

### Dataset Overview

The cleaned dataset contains **16,558 rows and 38 columns**, focusing on player-level performance metrics. Below are the relevant columns and their descriptions:

| **Column Name**            | **Description**                                                                                   |
|----------------------------|---------------------------------------------------------------------------------------------------|
| `position`                 | The player's in-game role (Top, Jungle, Mid, ADC, Support).                                       |
| `result`                   | Match outcome for the player’s team (1 = Win, 0 = Loss).                                          |
| `kills`, `deaths`, `assists` | Individual performance metrics representing combat contributions.                                |
| `visionscore`              | A composite score reflecting a player’s contributions to vision control on the map.              |
| `totalgold`                | Total gold earned by the player during the game.                                                 |
| `damagetochampions`        | Total damage dealt to enemy champions.                                                           |
| `teamkills`, `teamdeaths`  | The total number of kills and deaths for the player’s team.                                      |
| `golddiffat10`, `golddiffat15` | The player’s team gold advantage (or deficit) at 10 and 15 minutes, respectively.              |
| `kill_participation`       | Proportion of team kills the player contributed to (kills + assists) / teamkills.                |
| `gold_efficiency`          | Ratio of total gold earned to damage dealt, indicating resource usage efficiency.                |
| `vision_contribution`      | Contribution to vision control based on wards placed, wards cleared, and total vision score.     |
| `damage_output_consistency` | Measures the consistency of damage output relative to gold earned at 15 minutes.                 |

These features provide a comprehensive view of each player's in-game impact and allow us to build models that predict roles based on performance. By focusing on role-specific performance, we aim to uncover key gameplay dynamics and create meaningful insights for the esports community.

# Data Cleaning and Feature Engineering

### 1. Initial Data Cleaning
The raw dataset contained both **player-level** and **team-level** rows, as indicated by the `position` column:
- **Player Rows**: Contain detailed statistics for each of the 10 players in a match.
- **Team Rows**: Summarize team-level information for each side (e.g., Blue and Red).

##### **Action**: Remove Team Rows
- We focused only on **player rows** (`position != 'team`) to ensure the dataset reflected individual player performance.
- **Effect**: Simplified analysis by removing irrelevant rows that could confound feature engineering.

---

### 2. Handling Missing Values
Certain columns, such as `ban1` through `ban5`, had significant missingness:
- These columns were omitted from further analysis because they didn’t directly contribute to our player-focused role prediction task.
- **Effect**: Improved data quality and focused the dataset on relevant columns.

Other columns with minor missingness, such as `damageshare` or `goldat10`, were handled by:
- **Filling Missing Values**: Replaced missing numerical values with `0` during feature engineering to avoid breaking calculations.

---

### 3. Data Type Conversions
Several columns were incorrectly typed:
- **Boolean Conversions**: Columns like `firstblood`, `firsttower`, etc., were converted to boolean types to reflect their binary nature.
- **Effect**: Improved consistency and interpretability during feature engineering.

---

### 4. Feature Engineering
To enhance the dataset and align it with the gameplay context, we created several new features:

1. **Kill Participation**:
   kill_participation = (kills + assists) / teamkills

2. **Gold Efficiency**:
   gold_efficiency = totalgold / damagetochampions

3. **Vision Contribution**:
   vision_contribution = (wardsplaced + wardskilled) / visionscore

4. **Death Contribution**:
   death_contribution = deaths / teamdeaths

5. **Early Game Impact**:
    kill_participation_at_10 = (killsat10 + assistsat10) / teamkills

6. **Damage Share per Minute**:
  damage_share_per_minute = dpm / damageshare

7. **Vision Efficiency** (Support-Specific):
   vision_efficiency = (wardsplaced + wardskilled) / totalgold

8. **Gold Damage Ratio** (ADC-Specific):
   gold_damage_ratio = goldat15 / damagetochampions

9. **Tankiness Ratio** (Top and Jungle):
   tankiness_ratio = damagetakenperminute / damagemitigatedperminute

10. **Damage Output Consistency** (Mid and ADC):
    damage_output_consistency = dpm / goldat15


---

### 5. Final Dataset
- After cleaning and feature engineering, the dataset was ready for exploratory analysis and modeling.
- Example of the final cleaned dataset:

Part 1: Player Metrics

| position | result | kills | deaths | assists | visionscore | totalgold |
|:---------|-------:|------:|-------:|--------:|------------:|----------:|
| top      |      1 |     4 |      1 |       4 |          17 |     10762 |
| jng      |      1 |     5 |      1 |      11 |          40 |     10895 |
| mid      |      1 |     1 |      3 |       8 |          24 |      9560 |
| bot      |      1 |     8 |      1 |       7 |          33 |     13097 |
| sup      |      1 |     2 |      1 |      17 |          72 |      8209 |

Part 2: Derived Metrics

| position | kill_participation | gold_efficiency | vision_contribution | damage_output_consistency |
|:---------|-------------------:|----------------:|--------------------:|--------------------------:|
| top      |               0.40 |           0.912 |              0.5294 |                    0.0937 |
| jng      |               0.80 |           0.875 |              0.5250 |                    0.0963 |
| mid      |               0.45 |           0.679 |              0.5417 |                    0.1165 |
| bot      |               0.75 |           0.757 |              0.5758 |                    0.0984 |
| sup      |               0.95 |           1.406 |              0.5694 |                    0.0587 |

## Univariate Analysis

The distribution of Damage Per Minute (DPM) is right-skewed, with most players contributing between 200 and 800 DPM, while outliers exceed 1500 DPM. High DPM values are likely from damage-focused roles like ADC and Mid, whereas lower DPM values likely represent Support players or underperforming games.

<iframe
  src="assets/dpm_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


## Bivariate Analysis

Support players have the highest vision scores, as expected, with a median significantly higher than other roles, reflecting their focus on vision control. Jungle and bot roles also show moderate vision scores, while top and mid players contribute the least to vision.

<iframe
  src="assets/vision_score_role.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Interesting Aggregations

| `position`                  |   (`kills`, `mean`) |   (`kills`, `std`) |   (`assists`, `mean`) |   (`assists`, `std`) |   (`dpm`, `mean`) |   (`dpm`, `std`) |   (`visionscore`, `mean`) |   (`visionscore`, `std`) |   (`totalgold`, `mean`) |   (`totalgold`, `std`) |   (`goldat10`, `mean`) |   (`goldat10`, `std`) |   (`golddiffat10`, `mean`) |   (`golddiffat10`, `std`) |
|----------------------------|--------------------:|-------------------:|----------------------:|---------------------:|------------------:|-----------------:|--------------------------:|-------------------------:|------------------------:|-----------------------:|-----------------------:|----------------------:|---------------------------:|--------------------------:|
| bot                        |            4.66312  |            3.3921  |               5.73922 |              3.76505 |           685.917 |          256.623 |                   37.404  |                  20.4506 |                13759.1  |                3356.9  |                3457.5  |               432.346 |                          0 |                   657.299 |
| jng                        |            2.7583   |            2.45546 |               8.07006 |              4.73928 |           408.352 |          207.646 |                   44.519  |                  17.2263 |                10960    |                2340.89 |                3332.54 |               354.247 |                          0 |                   506.008 |
| mid                        |            3.99686  |            2.89922 |               5.95724 |              3.93386 |           688.277 |          230.92  |                   35.0246 |                  14.6462 |                13346.2  |                3055.78 |                3488.77 |               304.739 |                          0 |                   470.412 |
| sup                        |            0.932178 |            1.21199 |               9.95235 |              5.64557 |           205.088 |          104.729 |                  104.67   |                  37.2195 |                 8074.34 |                1694.54 |                2387.44 |               317.467 |                          0 |                   404.105 |
| top                        |            2.90663  |            2.42174 |               5.19368 |              3.65029 |           558.403 |          211.935 |                   30.6201 |                  11.7312 |                12247.2  |                2838.14 |                3296.38 |               350.509 |                          0 |                   568.317 |

This table summarizes performance metrics by player position, highlighting each role’s unique contributions. Bot leads in kills (4.66) and damage per minute (DPM ~685), reflecting its role as a primary damage dealer, while Support dominates assists (9.95) and vision score (104.67), focusing on utility and map control. Mid and Top show balanced contributions, with Mid excelling in versatility. Jungle supports map control with high vision scores (44.52). The consistent golddiffat10 across roles suggests balanced matches early on. These insights reveal each position's distinct priorities and impact in competitive play.

# Missingness Mechanism 

## NMAR Analysis 

I believe the column killsat25 is likely Not Missing At Random (NMAR) because its missingness may depend on unobserved values. For instance, teams in one-sided matches might avoid reporting poor performance metrics, or missingness could occur if the game duration didn’t reach 25 minutes.

To make this Missing At Random (MAR), additional data such as match duration, game outcomes (e.g., forfeits), league metadata, or team resources could explain the missingness using observable factors rather than the unreported killsat25 values.

## Missingness Dependency

<iframe
  src="assets/dist_pos.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/emp_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interpretation of the Graphs

#### **1. Distribution of Position When `playerid` is Missing or Not**
- The graph illustrates the frequency distribution of `position` when `playerid` is missing versus when it is not missing.
- **Observation**:
  - The `team` position accounts for the overwhelming majority of missing values in the `playerid` column.
  - For other positions (`jng`, `sup`, `bot`, `mid`, `top`), the frequency of missing and non-missing `playerid` is nearly identical, indicating no significant missingness in these positions.
- **Implication**:
  - This distribution strongly suggests that the missingness in `playerid` is concentrated within the `team` position.

---

#### **2. Adjusted Permutation Test for Dependency on Position**
- The adjusted permutation test evaluates whether the missingness of `playerid` is dependent on `position`.
- **Observation**:
  - The observed test statistic (red dashed line) lies far outside the null distribution (blue histogram), confirming a significant difference in missingness proportions across `position`.
  - The p-value is 0.0000, indicating that the dependency is highly statistically significant.
- **Implication**:
  - This result confirms that the missingness in `playerid` is not random but systematically linked to the `position` column.

---

#### **Missing by Design**
- The dependency of `playerid` missingness on the `team` position suggests that the missing values are **Missing by Design (MBD)**:
  - In the dataset, `team` represents a collective entity, not an individual player. Therefore, it is logical that a `playerid` would not exist for entries categorized as `team`.
  - This indicates that the missingness is intentional and reflects the nature of the data collection process rather than a failure to capture data or a random occurrence.
  - For positions like `jng`, `sup`, `bot`, `mid`, and `top`, `playerid` is present as these represent individual players, making the absence of `playerid` for `team` an intentional design decision.

---

### Conclusion
The missingness in `playerid` is heavily concentrated in the `team` position and is **Missing by Design (MBD)**. The dataset design intentionally excludes `playerid` for team-level entries, as `playerid` is irrelevant for collective entities. This insight is critical for interpreting the dataset and confirms that no imputation or statistical handling is necessary for these missing values.

# Hypothesis Test

Question:
Does Gold Difference at 10 Minutes Differ by Match Outcome?

H_0: The mean gold difference at 10 minutes is the same for winning and losing matches.

H_a: The mean gold difference at 10 minutes is the different for winning and losing matches.

Test Statistic: The observed difference in mean golddiffat10 between winning and losing matches:
- T: x_win - x_loss

Significance Level: 
- α = 0.05

A permutation test was performed by shuffling the result column and recalculating the test statistic 1,000 times to generate a null distribution of 𝑇.

P-value: The proportion of test statistics in the null distribution as extreme as 
𝑇_observed

Results:

P-value = 0.0: None of the permuted test statistics were as extreme as the observed statistic.

Since 𝑝 < 𝛼, we reject the null hypothesis.

Conclusion:

The data provides strong evidence to conclude that the mean gold difference at 10 minutes is significantly different between winning and losing matches. This suggests that early gold advantage plays a key role in determining match outcomes, aligning with the importance of early game strategy in League of Legends.


# Prediction Problem: Classifying Player Roles from Post-Game Data

## The Problem:
This is a multiclass classification problem, where the objective is to predict a player's role (Top, Jungle, Mid, ADC, or Support) based on their post-game metrics. The response variable is the position column, which contains five distinct classes corresponding to the roles.

### Why This Problem?
In League of Legends, roles are associated with unique playstyles, responsibilities, and metrics (e.g., Supports focus on vision, while ADCs excel in damage output). Identifying a player's role based on metrics enables insights into role-specific performance and helps coaches or analysts evaluate gameplay.

### Features Used for Prediction:
Features are derived from post-game statistics that would have been available after a match ends. Examples include:

Kills, deaths, assists: Indicators of combat contribution.

Gold metrics (e.g., totalgold, goldat10, gold_efficiency): Indicators of resource accumulation and usage.

Vision metrics (e.g., visionscore, wardsplaced): Indicators of map control.

Damage metrics (e.g., damagetochampions, dpm, damageshare): Indicators of contribution in fights.

Role-specific engineered features (e.g., vision_efficiency, gold_damage_ratio, tankiness_ratio): Capture gameplay nuances relevant to specific roles.

These features are selected because they reflect key gameplay attributes unique to different roles.

### Evaluation Metric:
Primary Metric: Accuracy.

Reason: Accuracy measures the proportion of correctly classified roles and is an intuitive way to assess overall model performance for a balanced dataset.

Secondary Metric: F1-score (macro-averaged).

Reason: Since different roles may have imbalanced representation or varying difficulty to classify (e.g., ADC vs. Support), the F1-score captures performance across all classes by balancing precision and recall.

## Baseline Model 

### Model Overview:
The baseline model is a Random Forest Classifier used to predict a player’s role (position) based on a set of gameplay metrics. The model pipeline includes preprocessing steps for both numeric and categorical features and uses a decision-tree-based classifier for prediction.

### Features Used:
1. visionscore (Quantitative): Indicates a player’s contribution to map vision, critical for Support and Jungle roles.

2. goldat10 (Quantitative): Represents the player’s gold accumulated at 10 minutes, a proxy for early-game performance.

3. result (Nominal):Match outcome (1 for win, 0 for loss), reflecting team performance.

Preprocessing:
- Numeric Features: Scaled using StandardScaler to normalize the data, ensuring numerical stability for the classifier.
- Categorical Features: Encoded using OneHotEncoder to convert the result column into a binary representation.

### Model Performance:
Accuracy: The baseline model achieved an accuracy of 43% on the test set.
Classification Report:

Bot (ADC): Precision: 0.26, Recall: 0.25, F1-Score: 0.26

Jungle: Precision: 0.35, Recall: 0.36, F1-Score: 0.36

Mid: Precision: 0.29, Recall: 0.29, F1-Score: 0.29

Support: Precision: 0.91, Recall: 0.92, F1-Score: 0.92

Top: Precision: 0.31, Recall: 0.31, F1-Score: 0.31

Overall Accuracy: 43%

### Evaluation of Performance:
Strengths: The model performs exceptionally well in classifying the Support role, with precision and recall of over 90%. Reasonable performance for Jungle, reflecting the importance of early gold and vision metrics for this role.

Weaknesses: ADC (Bot) and Mid roles show significantly lower precision and recall, indicating the current features are not sufficient to differentiate these roles effectively. The overall accuracy of 43% is modest but expected for a baseline with minimal features.

### Justification for Model Suitability:
While the baseline model is relatively simple, it captures some role-specific trends (e.g., high vision scores for Support, early gold for Jungle). However, the low accuracy for ADC and Mid roles suggests that additional features or advanced engineering will be necessary to improve the model's performance. This baseline serves as a foundation for understanding the role prediction problem and highlights the importance of refining features to enhance accuracy.


## Final Model: Description and Performance

### Features Added to the Final Model
To enhance the baseline model, several new features were engineered to capture role-specific gameplay behaviors:

1. **Kill Participation**:
   - Formula: `(kills + assists) / teamkills`
   - Relevance: Highlights a player's contribution to team success, essential for roles like Bot and Support.

2. **Gold Efficiency**:
   - Formula: `totalgold / damagetochampions`
   - Relevance: Measures how effectively a player translates gold into damage output, crucial for ADC and Mid roles.

3. **Vision Contribution**:
   - Formula: `(wardsplaced + wardskilled) / visionscore`
   - Relevance: Tracks map awareness and vision control, especially for Support.

4. **Roaming Potential (Mid)**:
   - Formula: `killsat15 - killsat10`
   - Relevance: Captures Mid-lane roaming tendencies to influence other parts of the map.

5. **Tankiness Ratio (Top)**:
   - Formula: `damagetakenperminute / (damagemitigatedperminute + 1e-5)`
   - Relevance: Reflects the durability of Top lane players in team fights.

6. **Gold Damage Ratio (Bot)**:
   - Formula: `goldat15 / (damagetochampions + 1e-5)`
   - Relevance: Evaluates how well ADCs scale their damage output from gold.

7. **Damage Output Consistency (Mid and ADC)**:
   - Formula: `damagetochampions / (goldat15 + 1e-5)`
   - Relevance: Measures damage consistency relative to gold income.

8. **Mid Solo Kills**:
   - Formula: `killsat10 / (kills + 1e-5)`
   - Relevance: Captures solo performance in the early game for Mid players.

9. **Bot CS Efficiency**:
   - Formula: `totalgold / (teamkills + 1e-5)`
   - Relevance: Highlights farming efficiency for ADCs.

---

### Modeling Algorithm
- **Algorithm**: Random Forest Classifier
- **Hyperparameter Tuning**:
  - GridSearchCV was used to optimize the following parameters:
    - `n_estimators`: Number of trees in the forest.
    - `max_depth`: Maximum depth of the trees.
    - `min_samples_split`: Minimum samples required to split a node.
  - Best Parameters:
    - `n_estimators = 200`
    - `max_depth = 20`
    - `min_samples_split = 10`

---

### Performance 
- **Bot**:
  - Precision: 0.56
  - Recall: 0.59
  - F1-Score: 0.58
  - Support: 3304

- **Jungle**:
  - Precision: 0.72
  - Recall: 0.71
  - F1-Score: 0.71
  - Support: 3261

- **Mid**:
  - Precision: 0.48
  - Recall: 0.40
  - F1-Score: 0.43
  - Support: 3367

- **Support**:
  - Precision: 0.85
  - Recall: 0.86
  - F1-Score: 0.86
  - Support: 3344

- **Top**:
  - Precision: 0.64
  - Recall: 0.72
  - F1-Score: 0.68
  - Support: 3282

### Overall Metrics
- **Accuracy**: 65%
- **Macro Average**:
  - Precision: 0.65
  - Recall: 0.66
  - F1-Score: 0.65
- **Weighted Average**:
  - Precision: 0.65
  - Recall: 0.65
  - F1-Score: 0.65

---

### Improvements Over Baseline Model
1. **Higher Accuracy**:
 - Improved from 43% to 65%, representing a significant increase in prediction capability.

2. **Role-Specific Insights**:
 - Precision for challenging roles like Mid increased from 29% to 48%.
 - Bot and Top roles saw major gains in precision and recall due to targeted feature engineering.

3. **Better Generalization**:
 - Hyperparameter tuning enhanced the model’s ability to generalize across the dataset.

4. **Balanced Predictions**:
 - Achieved more consistent performance across all roles, with improvements in precision, recall, and F1-score.

---

### Conclusion
The final model demonstrates significant improvements over the baseline, both in accuracy and role-specific performance. By incorporating domain knowledge into feature engineering and optimizing hyperparameters, the model effectively captures gameplay nuances, providing actionable insights into player roles.

# Fairness Analysis

#### Groups and Metric
- **Group X**: Support role predictions (`sup`).
- **Group Y**: ADC (Bot) role predictions (`bot`).
- **Evaluation Metric**: Precision.

#### Hypotheses
- **Null Hypothesis (H0)**: The model's precision for Support and ADC roles is the same. Any observed difference is due to random chance.
- **Alternative Hypothesis (Ha)**: The model's precision for Support is higher than for ADC, indicating potential bias.

#### Test Statistic
- The observed difference in precision between the Support and ADC roles:
  - T = Precision(Support) - Precision(ADC)

#### Significance Level
- Alpha (α) = 0.05

#### Results
- **Observed Test Statistic**: T = 0.0
- **Permutation Test p-Value**: p = 1.0

#### Conclusion
- Since the p-value is greater than α = 0.05, we fail to reject the null hypothesis.
- This suggests that the observed difference in precision between Support and ADC roles is not statistically significant and could be due to random chance. The model appears to perform fairly in terms of precision for these two roles within the analyzed subset.
