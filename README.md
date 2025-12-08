# The Impact of the First Dragon on Match Outcomes in League of Legends
Authors: Kangxin Peng, Francisco Zhao

## Introduction
Since 2013, League of Legends(LOL) has become the most popular game worldwide, attracting more and more players. During this period, almost 90% of gamers in Chinese cybercafe were playing LOL, demonstrating the dominance of LOL in Chinese games market. As LOL rapid development, Riot Games company held various competitions and provided generous bonuses, especially the world championship. Each important competition attains global attention and generates enormous commercial value, gradually forming a new culture which is LOL esport.

In League of Legends, when the bottom lane gains an early advantage, signifying that it can secure lane priority to easily push the milion wave under the enemy turrent and reduce the enemy champions' health, enabling the cooperation with jungler to take the first dragon. At the same time, if the mid lane also holds a slight advantage, then getting the dragon becomes almost risk-free. Therefore, whether a team can secure the first dragon usually depends on the skill gap between the mid and bot laners as well as the relative strength of their chosen champions. Moreover, securing the first dragon typically provides valuable bonuses for entire team, laying the foundation for the outcome of the subsequent match.

Therefore, I decided to investigate this topic through data analysis by proposing a crucial research question: **To what extent does securing the first dragon influence the outcome of a professional match?** This study focuses on examining various performance factors after obtaining the first dragon. These factors can be players' kills and deaths, as well as how the subsequent progression goes in the entire game. Using this approach, we can effectively evaluate whether the first dragon is truly significant in professional competition, thereby helping coaches and players gain a deeper understanding of the game and create more useful strategies for future matches.

### Introduction of DataFrame
This project uses team-level match data from the OraclesElixir professional League of Legends dataset. Since the LCK and LPL leagues have dominated the world championships for the past decade, this dataset only filtered to matches from the **LCK** and **LPL** leagues. There are 1110 rows with 9 columns that are valuable for upcoming research in this dataset. Each row represents one team's performance in a single match. The following variables are used in the analysis:

- `gameid` : Each professional match has a unique identifier. Since the blue team and the red team record the match data separately, each match ID appears exactly twice in the cleaned dataset: once for the blue team and once for the red team.

- `league` : The league in which the match was played. LCK – League of Legends Champions Korea; LPL – League of Legends Pro League (China).

- `result` : The outcome of each match. '1' represents the team wins the match, and '0' represents the team loses the match.

- `kills` : The total number of times a team kills a opposing champion during the match.

- `deaths` : The total number of times a champion is killed by the opposing team during the match.

- `firstdragon` : Binary variable indicating whether a team secured the first elemental dragon of the match: '1' represents the team obtained the first dragon; '0' represents opposing team obtained the first dragon.

- `firstmidtower` : Binary variable indicating whether the team destroyed the first mid-lane tower: '1' represents the team secured first mid turret; '0' represents opposing team secured first mid turret.

- `golddiffat25` : Difference in total team gold at the 25-minute mark: Positive values indicate a gold lead and negative values indicate a gold deficit.

- `csdiffat25` : Difference in total creep score (minion kills) between the team and its opponent at 25 minutes: Positive values indicate superior farming performance and negative values indicate poorer resource acquisition.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
To build a clear team-level dataset, we use a few simple cleaning steps.

First, we keep only matches marked as “complete” in the datacompleteness column. This choice makes sure the match records are finished and checked.

Next, we keep only the LCK and LPL leagues, because these are the leagues we study. Then we remove player-level rows and keep only rows where participantid is 100 or 200. Each of these rows shows one team’s stats for one match.

Because each match has one row for the blue side and one row for the red side, we keep only game IDs that appear exactly twice. This step removes matches that do not record both teams.

Then we keep only the variables that we need for our analysis:

- firstdragon

- result

- kills and deaths

- firstmidtower

- golddiffat25 and csdiffat25

We check missing values for the mid-game features. Both golddiffat25 and csdiffat25 have about 1% missing values. These values are missing because some games end before the 25-minute mark, so the stats do not exist. This pattern is NMAR. To avoid bias, we remove these rows only when we study mid-game features. All other features stay in the dataset.

Below is the first five rows of our dataframe.

| gameid                | result |   kills |   deaths |   firstdragon |   firstmidtower |   golddiffat25 |   csdiffat25 |
|:----------------------|---------:|--------:|---------:|----------:|-------------:|-----------------:|---------------:|
| LOLTMNT03_183532 |           1 |      18 |        2 |         1.0 |           1.0 |                6526.0  |             69.0 |
| LOLTMNT03_183532 |           0 |       2 |       18 |         0.0 |           0.0 |                -6526.0 |            -69.0 |
| LOLTMNT03_183538 |           1 |      13 |        5 |         1.0 |           1.0 |                5646.0	 |            -13.0 |
| LOLTMNT03_183538 |           0 |       5 |       13 |         0.0 |           0.0 |                -5646.0 |             13.0 |
| LOLTMNT03_183544 |           0 |      17 |       22 |         0.0 |           1.0 |                3574.0  |             23.0 |

To build a clear team-level dataset, we use a few simple cleaning steps.

First, we keep only matches marked as “complete” in the datacompleteness column. This choice makes sure the match records are finished and checked.

Next, we keep only the LCK and LPL leagues, because these are the leagues we study. Then we remove player-level rows and keep only rows where participantid is 100 or 200. Each of these rows shows one team’s stats for one match.

Because each match has one row for the blue side and one row for the red side, we keep only game IDs that appear exactly twice. This step removes matches that do not record both teams.

Then we keep only the variables that we need for our analysis:

- firstdragon

- result

- kills and deaths

- firstmidtower

- golddiffat25 and csdiffat25

We check missing values for the mid-game features. Both golddiffat25 and csdiffat25 have about 1% missing values. These values are missing because some games end before the 25-minute mark, so the stats do not exist. This pattern is NMAR. To avoid bias, we remove these rows only when we study mid-game features. All other features stay in the dataset.


### Univariate Analysis

# 两个

First, we examined the distribution of key team performance metrics. This helps us better understand the overall game flow before analyzing the relationship between early objectives and match outcomes.

We discovered that the distribution of total team kills showed a moderate right skew. Most teams secured between 5 and 25 kills per match, indicating that the majority of professional games remained relatively even rather than featuring extremely lopsided outcomes. Only a very small number of matches exceeded 30 kills, typically occurring when one side snowballed their economic and kill advantage early on, gaining significant vision and teamfight dominance.

The distribution of the 25-minute gold difference (golddiffat25) is generally clustered around zero, but extends into long positive and negative tails. This indicates that while many games remain economically close at the 25-minute mark, some teams have already established substantial gold leads or deficits. These extreme values often appear in games where teams rapidly snowballed their advantage through early objectives and lane dominance.

Overall, these distributions reveal that mid-game economic gaps do exist in professional matches, though they are not universal. Consequently, this metric helps us assess whether early advantages—such as securing the first dragon—can significantly influence match outcomes.


### Bivariate Analysis

#三个

First, we plan to use box plots to examine the performance differences in kill count, 25-minute gold difference, and 25-minute last-hit difference between teams that secure the first dragon. This will be our initial step.

Subsequent charts reveal that teams securing the first dragon exhibit higher median kill counts, indicating superior performance in team fights. Additionally, these teams more frequently maintain positive economic and last-hit advantages by the 25-minute mark, reflecting their ability to establish stable resource leads during the mid-game phase.

To further analyze and compare match outcomes, we created a pie chart illustrating the win rate difference between teams securing the first dragon versus those that did not. The chart clearly shows that teams securing the first dragon consistently achieve a higher overall win rate. This result aligns with the earlier performance trends, demonstrating that early control of the dragon significantly impacts both the game's pacing and the final outcome.

## Assessment of Missingness

### NMAR Analysis

First, both variables golddiffat25 and csdiffat25 contain some missing values because these statistics are only recorded when a match reaches the 25-minute mark. If a match ends prematurely (e.g., before 25 minutes), these metrics are not available, resulting in missing data.

This missing data directly depends on the duration of unrecorded matches. When a match ends earlier, the two 25-minute data points are more likely to be missing. Since match duration does not appear in the dataset and is related to whether these values are missing, this missingness does not arise solely from observable variables.

Therefore, the missingness mechanism for golddiffat25 and csdiffat25 is more appropriately classified as NMAR (Not Missing At Random).

### Missingness Dependency

To correctly determine whether the missing values in golddiffat25 are related to other missing-at-random (MAR) variables, we conducted a permutation test. We compared the mean difference in team kills between the missing and non-missing value groups and also examined whether the missing values were related to league (LCK vs LPL).

For team kills, the permutation test yielded a p-value of 0.64, indicating no significant difference between the two groups. For league, the p-value was 1.00, similarly showing no dependency or association.

Since both p-values were substantially greater than 0.05, we could not reject the null hypothesis in either test. Therefore, the missingness of golddiffat25 does not depend on observable variables like kills or league, providing evidence against a Mechanism of Randomness (MAR) mechanism. Considering the data generation process (matches ending before 25 minutes resulting in missing data), this pattern of missingness most closely aligns with Missing Completely At Random (MCAR).

## Hypothesis Testing
### Hypotheses

**Null Hypothesis (H₀):**  
There is no difference in win rate between teams that secure the first dragon and teams that do not.

**Alternative Hypothesis (H₁):**  
Teams that secure the first dragon have a higher win rate than teams that do not.


### Test Statistic

We use the difference in mean win rates:

mean(win | firstdragon=1) - mean(win | firstdragon=0)

# 一个

Permutation Test

We conducted a one-sided permutation test by randomly shuffling firstdragon's labels and recalculating the win rate difference after each permutation. We performed 10,000 repetitions to generate the null distribution used in the experiment.

The observed win rate difference was:

- 0.1099 (≈ 11.0%)

The corresponding p-value is:

- p = 0.0002

Conclusion

Since the p-value is significantly smaller than the significance level α = 0.05, we reject the null hypothesis. The data clearly indicates that securing the first dragon is significantly associated with a higher match win rate.

## Framing a Prediction Problem
Prediction Task

The purpose of this prediction task is to determine whether a team will win the match based on early performance metrics related to the first dragon. This is a binary classification task.

Response Variable:

result —— Represents whether the team wins the match (1 = win, 0 = loss).
We selected this variable because the project's primary goal is to investigate the relationship between early-game objectives, particularly the first dragon, and the final match outcome.

Prediction Goal:

Predict match outcomes using the following early or mid-game features:

- firstdragon
- kills
- deaths
- firstmidtower
- golddiffat25
- csdiffat25

These features provide timely information available shortly after the first dragon appears or during the mid-game phase. They are thus particularly suitable as data sources for prediction timepoints, avoiding data leakage from future information.

## Baseline Model

In the baseline model, we employ a Random Forest Classifier to predict match outcomes, utilizing features derived from four preliminary metrics:

- Numerical features: kills, deaths

- Categorical features: firstdragon, firstmidtower (these features are inherently binary variables)

We standardized numerical features using StandardScaler, while binary features remained unchanged without additional encoding. All preprocessing and modeling steps were executed within a single sklearn Pipeline.

The dataset was split into a 75% training set and a 25% test set. The target variable underwent stratification to maintain consistent category distribution.

After training the baseline model, we evaluated its performance on the test set using classification accuracy. The model achieved an accuracy of:

- Accuracy = 0.9604

This result indicates that even with a limited set of early-stage features, the model can predict match outcomes with considerable accuracy. However, the subsequent final model incorporates additional mid-game economic features and employs hyperparameter tuning to further enhance predictive performance.

## Final Model

To further enhance model quality beyond the baseline model, we incorporated two mid-game economic metrics:

- golddiffat25: Gold difference at 25 minutes

- csdiffat25: CS difference at 25 minutes

These features reflect a team's mid-game pacing and economic control. A gold lead typically indicates earlier item completion and stronger teamfight potential, while a CS lead signifies superior last-hitting efficiency and lane dominance. Incorporating these variables allows the model to capture economic dynamics not fully revealed by combat data alone.

The Final Model employs the same learning algorithm as the baseline—Random Forest Classifier—implemented via a sklearn pipeline. This pipeline includes:

- StandardScaler (for numerical features: kills, deaths, golddiffat25, csdiffat25)

- Passthrough (for binary features: firstdragon, firstmidtower)

Random Forest classifier

To enhance model performance, we employed GridSearchCV (5-fold cross-validation) for hyperparameter tuning. The searched hyperparameters included:

max_depth: Ranging from 2 to 182 (step size 20)
n_estimators: Ranging from 2 to 92 (step size 10)

The optimal parameter combination identified was:

- max_depth = 22

- n_estimators = 22

We evaluated the final model on the test set using these parameters, achieving an accuracy of:

- Final Test Accuracy = 0.9604

This performance matches the baseline model's accuracy. While there is no numerical improvement, the Final Model incorporates richer economic features, aligning more closely with the actual game's data generation process. This makes the model conceptually more sound and enhances the interpretability of the results.

## Fairness Analysis

## Evaluation Metric

First, our evaluation metric is classification accuracy, defined as:

- Accuracy = mean(correct predictions)

We used the final trained model to calculate the accuracy for each group.



## Hypotheses

Null Hypothesis (H₀):
The model is fair — the prediction accuracy is the same for both groups.

Alternative Hypothesis (H₁):
The model is biased — the prediction accuracy differs between the two groups.



## Group Definition

Group X: Teams with csdiffat25 ≤ 0 (no CS advantage or trailing at 25 minutes).

Group Y: Teams with csdiffat25 > 0 (CS advantage at 25 minutes).



## Test Statistic

Our test statistic is the accuracy difference between Group Y and Group X:

- Observed Difference = Accuracy(Group Y) − Accuracy(Group X)

Based on model predictions, we obtain:

- Observed Difference ≈ 0.0353


## Permutation Test

To further validate our hypothesis, we conducted a permutation test with N = 10,000 shuffles.
For each shuffle, we randomly permuted group labels and recalculated the accuracy difference to simulate the null distribution under a fair model.

The p-value was calculated as:

- p-value = proportion of permuted differences

with absolute value ≥ absolute observed difference


## Results

Our permutation test yielded a p-value of:

- p-value ≈ 0.1318

Since the p-value exceeds the significance level α = 0.05, we cannot reject the null hypothesis.



## Conclusion

There is currently insufficient statistical evidence to indicate a performance difference between the two CS-difference groups.

Therefore, under this fairness test, the model demonstrates fairness regarding the 25-minute CS difference.
