# League of Legends: Top vs Bot.
League of Legends: Top vs Bot is a data science project conducted at UCSD that aims to cover analysis of professional League of Legends games in major regions (LCK, LPL, LCS, LEC) in 2022.  It encompasses processes such as exploratory data analysis, permutation testing, regression models, and fairness analysis.

This project aims to answer the question: is top lane or bot lane the stronger lane to give resources to in competitive play?


# Introduction
### About League of Legends
League of Legends is a popular multiplayer online battle arena game released by Riot Games in 2009. Ever since the game came out, the game has had a bustling eSports scene. 5 players per team are assigned to 5 different roles: Top, Jungle, Mid, Bottom (Bot), and Support. The objective of the game is to work together with your team to destroy the enemy team's Nexus. 

Of note, there is a limited amount of resources on the map to allocate to each player on the map, and they can't be everywhere at once, which leads us into the question we're trying to answer: between the two positions on the opposite sides of the map, top lane and bot lane, which
position is it more ideal to allocate resources to? To answer this, I am looking at a dataset of League of Legends games from 2022, from the website Oracle's Elixir, to answer this question.

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

# Assessment of Missingness
### Missingness Disclaimer
Going forward, I want to make a disclaimer that for the remaining steps in this project, I am only going to be working with the dataset of games with a split map. As I said previously, my metrics for constituting a split map
fails without CS difference and gold difference at 15 minutes. That said, I would still like to assess the missingness in games with missing values anyway.

#### Missingness DataFrame Head
| gameid           | league   | side   | position   | champion   |   result |   csdiffat15 |   golddiffat15 |   turretplates |   firsttower |   firstdragon |   firstherald |   towers |   kills |   opp_towers |   strongside |
|:-----------------|:---------|:-------|:-----------|:-----------|---------:|-------------:|---------------:|---------------:|-------------:|--------------:|--------------:|---------:|--------:|-------------:|-------------:|
| 8401-8401_game_1 | LPL      | Blue   | top        | Gwen       |        1 |          nan |            nan |            nan |          nan |           nan |           nan |      nan |       5 |          nan |          nan |
| 8401-8401_game_1 | LPL      | Blue   | jng        | Jarvan IV  |        1 |          nan |            nan |            nan |          nan |           nan |           nan |      nan |       0 |          nan |          nan |
| 8401-8401_game_1 | LPL      | Blue   | mid        | Syndra     |        1 |          nan |            nan |            nan |          nan |           nan |           nan |      nan |       3 |          nan |          nan |
| 8401-8401_game_1 | LPL      | Blue   | bot        | Jinx       |        1 |          nan |            nan |            nan |          nan |           nan |           nan |      nan |       4 |          nan |          nan |
| 8401-8401_game_1 | LPL      | Blue   | sup        | Nautilus   |        1 |          nan |            nan |            nan |          nan |           nan |           nan |      nan |       1 |          nan |          nan |

### Assumption
As you can see, all the gameids that have relatively arbitrary gameids with just numbers instead of containing ESPORTSTMNT are games that do not have values for csdiffat15 or golddiffat15. It's reasonable to conclude that since the gameids are markedly different, games with the label ESPORTSTMNT are matches that were broadcasted, and that it likely stands for eSports Tournament, which means that the other gameids were likely ids of scrim games or otherwise matches that were not broadcasted. Scrims are not really relevant to the conversation here because of a lack of public or stage pressure; the audience will never get to see a scrim game. The audience and analysts will make their conclusions on the metagame of League of Legends based on what happens in broadcasted games, since that's all they'll ever see.

It could superficially be argued that the data is MAR since missingness is tied to the gameid. However, the reason why the non ESPORTSTMNT games have missing data is because they were not broadcasted and the data is likely missing by design (MD) as those features were never intended to be recorded for scrim games. The data isn't MCAR because the data isn't missing independent of any context, nor is it NMAR because it isn't missing due to the value themselves.

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis