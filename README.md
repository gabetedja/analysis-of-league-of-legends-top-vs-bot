# League of Legends: Top vs Bot.
League of Legends: Top vs Bot is a data science project conducted at UCSD that aims to cover analysis of professional League of Legends games in major regions (LCK, LPL, LCS, LEC) in 2022.  It encompasses processes such as exploratory data analysis, permutation testing, regression models, and fairness analysis.

This project aims to answer the question: is top lane or bot lane the stronger lane to give resources to in competitive play?


# Introduction
### About League of Legends
League of Legends is a popular multiplayer online battle arena game released by Riot Games in 2009. Ever since the game came out, the game has had a bustling eSports scene. 5 players per team are assigned to 5 different roles: Top, Jungle, Mid, Bottom (Bot), and Support. The objective of the game is to work together with your team to destroy the enemy team's Nexus. 

Of note, there is a limited amount of resources on the map to allocate to each player on the map, and they can't be everywhere at once, which leads us into the question we're trying to answer: between the two positions on the opposite sides of the map, top lane and bot lane, which position is it more ideal to allocate resources to? To answer this, I am looking at a dataset of League of Legends games from 2022, from the website Oracle's Elixir, to answer this question.

### Introduction of Columns Used

-`gameid`: Identifier of game played

-`league`: The region the game was played in

-`side`: Side of map each team was on (Blue team and Red team)

-`position`: Position played by each player in a game. We are focusing only on top and bot in this analysis, but mid, jungle, and support have been kept in for completeness.

-`result`: Which team won or lost. 1 signifies win, 0 signifies loss.

-`csdiffat15`: The difference of minions/monsters killed by the player compared to their opponent. Leads are usually acquired by exploiting lane pressure in a 1v1 matchup or receiving assistance from the team.

-`golddiffat15`: The difference of gold earned by the player compared to their opponent. Gold, when spent on items, makes the player's champion stronger.

-`strongside`: Whether top or bot lane was strongsided, or prioritized/facilitated by their team

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
regions, as well as applying another function to the dataset that filters for only games where top lane on one team
was strongsided and the opposite team had bot lane strongsided, the dataset I will be working with for the remainder of this project, aside from the missingness analysis, will only have 2472 rows. Here is the head of the DataFrame
in question.

##### Strongside - 
 Term used to describe which side lane (top or bot) gets more attention from the team in a given game. This metric is measured using CS difference at 15 minutes and gold difference at 15 minutes (CS difference greater than 20, gold difference greater than 500). The opposite of this is weakside, in which a lane gets relatiely little attention
 from their team.
 
##### Rationale behind some of the filtering -
 Additionally, this dataset is filtered exclusively to games in which both teams had a strongside top and weakside bot or vice versa. This tells us more than a game that was a complete blowout; we want to know in a relatively competitive game where the map was split (each team allocated resources to a given sidelane, top or bot), which teams ultimately win more often than not?

##### Missingness and Split Map -
Due to the nature of how my split map metric works, I can only determine if a game had a split map state or not with the golddiffat15 and csdiffat15 metrics. Split map means that one side favored one sidelane over the other (FOR EXAMPLE, Red Team prioritized their top lane over their bot lane while the Blue Team prioritized their bot lane over the top lane.) So therefore I cannot answer my split map question with missing data. So I've cleaned two separate dataframes, one to assess missingness, and another to assess my question of whether strongside top or bot is more optimal.

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

### Univariate Analysis

### Bivariate Analysis

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
This hypothesis test aims to answer whether or not bot lane 
#### Null Hypothesis:
There is no statistically significant winrate difference between strongsiding bot and strongsiding top, and they are equally viable strategies.

#### Alternate Hypothesis:
There is a significant difference between strongsiding bot and strongsiding top, and they are not equally viable strategies.

#### Test Chosen and Reasoning:
The best choice for this test is a permutation test, because the distribution under the null isn't random here. The decision to strongside a lane is not random, it's a decision made by the team. My test statistic will be the absolute difference of means, as direction doesn't matter here. A significance level of 0.05 is used here.

# Framing a Prediction Problem
### PREDICTION PROBLEM
Whether a team will win or lose a game. This can be predicted by whether a team's resource allocation/map play strategy involves strongsiding their top or bot laner. Linear Regression is a possible model for this task.

# Baseline Model

# Final Model

# Fairness Analysis