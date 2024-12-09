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

<iframe
  src="assets/dpm_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of Damage Per Minute (DPM) is right-skewed, with most players contributing between 200 and 800 DPM, while outliers exceed 1500 DPM. High DPM values are likely from damage-focused roles like ADC and Mid, whereas lower DPM values likely represent Support players or underperforming games.



## Bivariate Analysis

<iframe
  src="assets/vision_score_role.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Support players have the highest vision scores, as expected, with a median significantly higher than other roles, reflecting their focus on vision control. Jungle and bot roles also show moderate vision scores, while top and mid players contribute the least to vision.