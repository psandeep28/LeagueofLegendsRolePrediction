# LeagueofLegendsRolePrediction

# Introduction 

## Data Cleaning and Feature Engineering

### 1. Initial Data Cleaning
The raw dataset contained both **player-level** and **team-level** rows, as indicated by the `position` column:
- **Player Rows**: Contain detailed statistics for each of the 10 players in a match.
- **Team Rows**: Summarize team-level information for each side (e.g., Blue and Red).

#### **Action**: Remove Team Rows
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
   \[
   \text{kill\_participation} = \frac{\text{kills} + \text{assists}}{\text{teamkills}}
   \]

2. **Gold Efficiency**:
   \[
   \text{gold\_efficiency} = \frac{\text{totalgold}}{\text{damagetochampions}}
   \]


3. **Vision Contribution**:
   \[
   \text{vision\_contribution} = \frac{\text{wardsplaced} + \text{wardskilled}}{\text{visionscore}}
   \]

4. **Death Contribution**:
   \[
   \text{death\_contribution} = \frac{\text{deaths}}{\text{teamdeaths}}
   \]


5. **Early Game Impact**:
   \[
   \text{kill\_participation\_at\_10} = \frac{\text{killsat10} + \text{assistsat10}}{\text{teamkills}}
   \]


6. **Damage Share per Minute**:
   \[
   \text{damage\_share\_per\_minute} = \frac{\text{dpm}}{\text{damageshare}}
   \]


7. **Vision Efficiency** (Support-Specific):
   \[
   \text{vision\_efficiency} = \frac{\text{wardsplaced} + \text{wardskilled}}{\text{totalgold}}
   \]


8. **Gold Damage Ratio** (ADC-Specific):
   \[
   \text{gold\_damage\_ratio} = \frac{\text{goldat15}}{\text{damagetochampions}}
   \]

9. **Tankiness Ratio** (Top and Jungle):
   \[
   \text{tankiness\_ratio} = \frac{\text{damagetakenperminute}}{\text{damagemitigatedperminute}}
   \]

10. **Damage Output Consistency** (Mid and ADC):
    \[
    \text{damage\_output\_consistency} = \frac{\text{dpm}}{\text{goldat15}}
    \]

---

### 5. Final Dataset
- After cleaning and feature engineering, the dataset was ready for exploratory analysis and modeling.
- Example of the final cleaned dataset:
| position   |   result |   kills |   deaths |   assists |     dpm |   damageshare |   goldat10 |   goldat15 |   visionscore |   damagetochampions |   totalgold |   wardsplaced |   wardskilled |   teamkills |   teamdeaths |   golddiffat10 |   golddiffat15 |   killsat10 |   assistsat10 |   deathsat10 |   killsat15 |   assistsat15 |   deathsat15 |   damagetakenperminute |   damagemitigatedperminute |   kill_participation |   gold_efficiency |   vision_contribution |   death_contribution |   kill_participation_at_10 |   damage_share_per_minute |   vision_efficiency |   gold_damage_ratio |   tankiness_ratio |   damage_output_consistency | kill_participation_missing   |
|:-----------|---------:|--------:|---------:|----------:|--------:|--------------:|-----------:|-----------:|--------------:|--------------------:|------------:|--------------:|--------------:|------------:|-------------:|---------------:|---------------:|------------:|--------------:|-------------:|------------:|--------------:|-------------:|-----------------------:|---------------------------:|---------------------:|------------------:|----------------------:|---------------------:|---------------------------:|--------------------------:|--------------------:|--------------------:|------------------:|----------------------------:|:-----------------------------|
| top        |        1 |       4 |        1 |         4 | 489.875 |     0.192039  |       3325 |       5229 |            17 |               11806 |       10762 |             7 |             2 |          20 |            7 |             32 |           -575 |           0 |             0 |            0 |           0 |             0 |            0 |                886.805 |                    947.759 |                 0.4  |          0.91157  |              0.529412 |             0.142857 |                       0    |                   2550.92 |         0.000836276 |            0.44291  |          0.935686 |                   0.0936844 | False                        |
| jng        |        1 |       5 |        1 |        11 | 516.805 |     0.202596  |       3409 |       5366 |            40 |               12455 |       10895 |             7 |            14 |          20 |            7 |            101 |            236 |           1 |             1 |            0 |           2 |             2 |            1 |               1089.54  |                    742.656 |                 0.8  |          0.874749 |              0.525    |             0.142857 |                       0.1  |                   2550.91 |         0.00192749  |            0.430831 |          1.46709  |                   0.096311  | False                        |
| mid        |        1 |       1 |        3 |         8 | 584.315 |     0.229061  |       3310 |       5015 |            24 |               14082 |        9560 |             6 |             7 |          20 |            7 |           -211 |           -409 |           0 |             1 |            1 |           0 |             1 |            1 |                679.253 |                    316.432 |                 0.45 |          0.678881 |              0.541667 |             0.428571 |                       0.05 |                   2550.92 |         0.00135983  |            0.356128 |          2.1466   |                   0.116514  | False                        |
| bot        |        1 |       8 |        1 |         7 | 717.635 |     0.281325  |       4292 |       7296 |            33 |               17295 |       13097 |            10 |             9 |          20 |            7 |           1426 |           2811 |           3 |             1 |            0 |           4 |             2 |            0 |                334.025 |                    273.527 |                 0.75 |          0.757271 |              0.575758 |             0.142857 |                       0.2  |                   2550.91 |         0.00145071  |            0.421856 |          1.22118  |                   0.09836   | False                        |
| sup        |        1 |       2 |        1 |        17 | 242.282 |     0.0949786 |       2668 |       4128 |            72 |                5839 |        8209 |            34 |             7 |          20 |            7 |             16 |            230 |           0 |             3 |            1 |           0 |             5 |            1 |                225.021 |                    271.701 |                 0.95 |          1.40589  |              0.569444 |             0.142857 |                       0.15 |                   2550.91 |         0.00499452  |            0.70697  |          0.828192 |                   0.0586924 | False                        |
