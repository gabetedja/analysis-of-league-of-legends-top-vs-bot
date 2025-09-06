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

