# League of Legends: Top vs Bot.
League of Legends: Top vs Bot is a data science project conducted at UCSD that aims to cover analysis of professional League of Legends games in major regions (LCK, LPL, LCS, LEC) in 2022.  It encompasses processes such as exploratory data analysis, permutation testing, regression models, and fairness analysis.

This project aims to answer the question: is top lane or bot lane the stronger lane to give resources to in competitive play? Welcome to Summoner's Rift! 


# Introduction
### About League of Legends
League of Legends is a popular multiplayer online battle arena game released by Riot Games in 2009. Ever since the game came out, the game has had a bustling eSports scene. 5 players per team are assigned to 5 different roles: Top, Jungle, Mid, Bottom (Bot), and Support. The objective of the game is to work together with your team to destroy the enemy team's Nexus. 

Of note, there is a limited amount of resources on the map to allocate to each player on the map, and they can't be everywhere at once, which leads us into the question we're trying to answer: between the two positions on the opposite sides of the map, top lane and bot lane, which position is it more ideal to allocate resources to? To answer this, I am looking at a dataset of League of Legends games from 2022, from the website Oracle's Elixir, to answer this question.

### Picture of Summoner's Rift for context
![Annotated Picture]('assets/annotated_rift.png')

### Introduction of Columns Used

-`gameid`: Identifier of game played

-`league`: The region the game was played in

-`side`: Side of map each team was on (Blue team and Red team)

-`position`: Position played by each player in a game. We are focusing only on top and bot in this analysis, but mid, jungle, and support have been kept in for completeness.

-`result`: Which team won or lost. 1 signifies win, 0 signifies loss.

-`csdiffat15`: The difference of minions/monsters killed by the player compared to their opponent. Leads are usually acquired by exploiting lane pressure in a 1v1 matchup or receiving assistance from the team.

-`golddiffat15`: The difference of gold earned by the player compared to their opponent. Gold, when spent on items, makes the player's champion stronger.

-`strongside`:  Term used to describe which side lane (top or bot) gets more attention from the team in a given game. This metric is measured using CS difference at 15 minutes and gold difference at 15 minutes (CS difference greater than 20, gold difference greater than 500). The opposite of this is weakside, in which a lane gets relatiely little attention from their team.

-`turretplates`: Amount of turret plates destroyed by the player. Turret plates give gold and generally signify an
early lead or assistance from their team.

-`firsttower`: Signifies whether the team got first tower in the game. A milestone for map control.

-`firstdragon`: Signifies whether or not the team killed the first dragon in the game. Usually signals proximity to bot lane.

-`firstherald`: Signifies whether ot not the team killed the first Rift Herald in the game. Usually signals
proximity to top lane.

-`kills`: The amount of kills earned by the player in that game. In competitive play, kills usually do not happen
in a 1v1 scenario, so a kill usually implies resources were allocated to a player who got the kill.

-`opp_towers`: Towers destroyed by the player. Usually a good metric of impact for top lane duelists.

# Data Cleaning and Exploratory Data Analysis

### Data Cleaning
The original dataset has 150588 rows, but after applying some filtering, such as filtering for only the 4 major
regions (as they are the strongest regions in League and are the most representative of quality gameplay), as well as applying another function to the dataset that filters for only games where top lane on one team
was strongsided and the opposite team had bot lane strongsided, the dataset I will be working with for the remainder of this project, aside from the missingness analysis, will only have 2472 rows. Here is the head of the DataFrame in question.

````markdown
#### Split Map DataFrame Head
| gameid                | league   | side   | position   | champion   |   result |   csdiffat15 |   golddiffat15 |   turretplates |   firsttower |   firstdragon |   firstherald |   kills |   opp_towers |   strongside |
|:----------------------|:---------|:-------|:-----------|:-----------|---------:|-------------:|---------------:|---------------:|-------------:|--------------:|--------------:|--------:|-------------:|-------------:|
| ESPORTSTMNT01_2690725 | LCK      | Blue   | top        | Graves     |        0 |           38 |           1571 |            nan |          nan |           nan |           nan |       0 |          nan |            1 |
| ESPORTSTMNT01_2690725 | LCK      | Blue   | jng        | Lee Sin    |        0 |           -7 |             61 |            nan |          nan |           nan |           nan |       1 |          nan |          nan |
| ESPORTSTMNT01_2690725 | LCK      | Blue   | mid        | Syndra     |        0 |            9 |            326 |            nan |          nan |           nan |           nan |       2 |          nan |          nan |
| ESPORTSTMNT01_2690725 | LCK      | Blue   | bot        | Jhin       |        0 |          -47 |          -2120 |            nan |          nan |           nan |           nan |       1 |          nan |            0 |
| ESPORTSTMNT01_2690725 | LCK      | Blue   | sup        | Yuumi      |        0 |            2 |          -1536 |            nan |          nan |           nan |           nan |       0 |          nan |          nan |
```
````


 
##### Rationale behind some of the filtering -
 Additionally, this dataset is filtered exclusively to games in which both teams had a strongside top and weakside bot or vice versa. This tells us more than a game that was a complete blowout; we want to know in a relatively competitive game where the map was split (each team allocated resources to a given sidelane, top or bot), which teams ultimately win more often than not?

##### Missingness and Split Map -
Due to the nature of how my split map metric works, I can only determine if a game had a split map state or not with the golddiffat15 and csdiffat15 metrics. Split map means that one side favored one sidelane over the other (FOR EXAMPLE, Red Team prioritized their top lane over their bot lane while the Blue Team prioritized their bot lane over the top lane.) So therefore I cannot answer my split map question with missing data. So I've cleaned two separate dataframes, one to assess missingness, and another to assess my question of whether strongside top or bot is more optimal.


### Univariate Analysis
We performed univariate analysis on the golddiffat15 and csdiffat15 features. Here are their histograms below.
<iframe src="assets/golddiff_at_15.html" width="800" height="600" frameborder="0"></iframe>
<iframe src="assets/csdiffat15.html" width="800" height="600" frameborder="0"></iframe>
As can be seen, the distribution of the data in these histograms is mostly normal, suggesting
well behaved data that isn't out of the ordinary and is good to analyze player resource advantages or disadvantages in a professional setting.

### Bivariate Analysis

We performed bivariate analysis on the columns 'strongside' and 'result', filtered to two separate DataFrames
for top lane and bot lane, because if we conducted the operation on the whole split map DataFrame, it would
result in a 50% winrate for both sides automatically. Since the data is categorical, bar graphs were used.
<iframe src="assets/strongside_top_wr.html" width="800" height="600" frameborder="0"></iframe>
<iframe src="assets/strongside_bot_wr.html" width="800" height="600" frameborder="0"></iframe> 
So we can see that there's a negative correlation between winning and strongsiding your top laner, and that strongside top has a 37% winrate! Think twice before playing around that Fiora in your solo queue, and make sure to camp that Draven at all costs! But in all seriousness, this is likely attributable to western teams being much more skewed towards bot lane, with much better ADCs (bot laners) on average than top lane players. In Eastern regions, both carry tops and bots are equally viable strategies. It's also harder to make top lane a win condition since generally top lane carries are moreso 1v1 specialists, such as Jax and Fiora, rather than being great in 5v5 as bot laners like Jinx, Zeri, and Caitlyn are. Additionally, top lane is generally isolated from the rest of the map and the majority of early fights around dragon and bot river, only really participating in the scuttle, grub, and herald fights until the lategame.


### Interesting Aggregates
Here is an interesting aggregate: highest winrates when a champ is strongsided, found by grouping by champion
and result, then aggregating the mean and count into a winrate and games played column respectively. As can be
seen, Zeri is the highest winrate champion when strongsided, and was frankly only not played more in 2022
because she was banned by the majority of teams due to her perceived strength.

````markdown
| champion   |   win_rate |   games_played |
|:-----------|-----------:|---------------:|
| Zeri       |   0.777778 |             18 |
| Kalista    |   0.692308 |             13 |
| Caitlyn    |   0.6875   |             16 |
| Jinx       |   0.619048 |             42 |
| Aphelios   |   0.611111 |             36 |
````
# Assessment of Missingness
### Missingness Disclaimer
Going forward, I want to make a disclaimer that for the remaining steps in this project, I am only going to be working with the dataset of games with a split map. As I said previously, my metrics for constituting a split map
fails without CS difference and gold difference at 15 minutes. That said, I would still like to assess the missingness in games with missing values anyway.

````markdown
## Missingness DataFrame Head

| gameid             | league | side | position | champion   | result | csdiffat15 | golddiffat15 | turretplates | firsttower | firstdragon | firstherald | towers | kills | opp_towers | strongside |
|:------------------|:-------|:-----|:---------|:-----------|--------:|------------:|--------------:|---------------:|------------:|--------------:|--------------:|--------:|--------:|-------------:|------------:|
| 8401-8401_game_1  | LPL    | Blue | top      | Gwen       |       1 | nan        | nan          | nan           | nan        | nan          | nan          |      5 | nan    | nan         | nan         |
| 8401-8401_game_1  | LPL    | Blue | jng      | Jarvan IV  |       1 | nan        | nan          | nan           | nan        | nan          | nan          | nan    | nan    | 0           | nan         |
| 8401-8401_game_1  | LPL    | Blue | mid      | Syndra     |       1 | nan        | nan          | nan           | nan        | nan          | nan          |      3 | nan    | nan         | nan         |
| 8401-8401_game_1  | LPL    | Blue | bot      | Jinx       |       1 | nan        | nan          | nan           | nan        | nan          |      4       | nan    | nan    | nan         | nan         |
| 8401-8401_game_1  | LPL    | Blue | sup      | Nautilus   |       1 | nan        | nan          | nan           | nan        | nan          |      1       | nan    | nan    | nan         | nan         |
```
````

### Assumption
As you can see, all the gameids that have relatively arbitrary gameids with just numbers instead of containing ESPORTSTMNT are games that do not have values for csdiffat15 or golddiffat15. It's reasonable to conclude that since the gameids are markedly different, games with the label ESPORTSTMNT are matches that were broadcasted, and that it likely stands for eSports Tournament, which means that the other gameids were likely ids of scrim games or otherwise matches that were not broadcasted. Scrims are not really relevant to the conversation here because of a lack of public or stage pressure; the audience will never get to see a scrim game. The audience and analysts will make their conclusions on the metagame of League of Legends based on what happens in broadcasted games, since that's all they'll ever see.

It could superficially be argued that the data is MAR since missingness is tied to the gameid. However, the reason why the non ESPORTSTMNT games have missing data is because they were not broadcasted and the data is likely missing by design (MD) as those features were never intended to be recorded for scrim games. The data isn't MCAR because the data isn't missing independent of any context, nor is it NMAR because it isn't missing due to the value themselves.

# Hypothesis Testing
This hypothesis test aims to answer whether or not top lane or bot lane is more optimal to strongside by
analyzing their winrates via a hypothesis test.

#### Null Hypothesis:
There is no statistically significant winrate difference between strongsiding bot and strongsiding top, and they are equally viable strategies.

#### Alternate Hypothesis:
There is a significant difference between strongsiding bot and strongsiding top, and they are not equally viable strategies.

#### Test Chosen and Reasoning:
The best choice for this test is a permutation test, because the distribution under the null isn't random here. The decision to strongside a lane is not random, it's a decision made by the team. My test statistic will be the absolute difference of means, as direction doesn't matter here. A significance level of 0.05 is used here.

#### Results
<iframe
  src="assets/hypothesis_test_results.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Based on this data, we reject the null hypothesis. The winrates for bot laners and top laners aren't equal and strongsiding top and bot are not equally viable strategies, or at least not equally applicable to any situation.

# Framing a Prediction Problem
### PREDICTION PROBLEM
Whether a team will win or lose a game. This can be predicted by whether a team's resource allocation/map play strategy involves strongsiding their top or bot laner. Linear Regression is a possible model for this task. Given that the majority of our variables are either binary or numeric, there isn't any encoding that needs to be done for our model. To prevent overfitting,
the data will be split into training data and test data at an 80 to 20 ratio. Model evaluation
will be done using RMSE. 

Furthermore, due to the nature of my question, there needs to be
two separate but mostly identical models, because if the models are merged, it will ALWAYS
average out to a 50% winrate for both top and bot lane. For all intents and purposes, the models
are the same, just measuring the winrate for top and bot respectively. So while technically there
are two different models, they are almost completely identical.

# Baseline Model
For the baseline model, a simple Linear Regression model was used with only the binary feature
strongside used to track the result. After fitting the model and showing our predictions,
our RMSE was 0.4875 for top lane and 0.4962 for bot lane, which signals that the model
is not much better than just predicting the mean.

Here was our output:

Top Winrate when strongside: 47.59985558215104%

Top RMSE:  0.48751120168768197

Bot Winrate when strongside: 50.78550934807408%

Bot RMSE:  0.49616047047465045

# Final Model
In our final model, the following features were added. We added a feature 'is_strongside_top/bot_champ', which
is a binary feature that sees if the champ is traditionally strongsided in top (Jax, Fiora) or bot (Zeri, Jinx). Additionally, we added the binary features firsttower, firstdragon, firstherald, as well as the numeric features
kills, turretplates, and opp_towers. Lastly, we made a new feature to see if strongside got plates, by multiplying turretplates and strongside together, named plates_and_strongside. We are using R^2 as our evaluation metric

Our final model uses a Random Forest Regressor since our data isn't linear in nature, with the hyperparameters
max depth and minimum samples split, and using grid search to find that the best hyperparameters for top lane are 
a max depth of 4 and a minimum sample split of 10, and the best hyperparameters for bot lane are a max depth of 3 and
minimum sample split of 5.

Here was our output:

TEST SET EVALUATION: 

Top Winrate when strongside: 52.0962913022957%

Bot Winrate when strongside: 53.11458993606587%

Top Lane R^2 on test set : 0.2157064684301735

Bot Lane R^2 on test set : 0.504319025050114

So now, the bot lane predictions are fairly accurate, but the top lane predictions are lagging behind significantly.

# Fairness Analysis
In this section, the fairness of the final model will be assessed between two different groups: again, 
top laners and bot laners. The question to be answered is "Does my model perform the same for both top laners and bot
laners?"

#### Null Hypothesis (H₀):
The model is equally fair for both top laners and bot laners. The difference in RMSE is down to random chance.

#### Alternate Hypothesis (H₁):
The model is less fair for one group compared to the other. The difference in RMSE is not random.

My test statistic will be the absolute difference in RMSE, and our significance level is 0.05.

#### Result 
After performing the permutation test, we reject the null hypothesis because the p-value is 0.015, less than 0.05 and thus our observed statistic is extreme, meaning that our model is not equally fair to top and bot lane.