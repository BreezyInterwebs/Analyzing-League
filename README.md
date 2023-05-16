## Introduction
League of Legends is a highly popular MOBA developed by Riot Games. Enjoyed by millions, it has a flourishing casual and competitive gaming scene. The dataset that we are looking into contains a wealth of information about games played in the competitive environment, such as stats about each teams performance, meta information about the teams themselves, and whether certain milestones are achieved. It is important to note that competitve League is almost exclusively played on Summoner's Rift, League's most popular map. As a casual player or commentator for League, there may be too much information about the game to follow along. How can we find something simple to focus on in order to understand how the game is progressing? 
We are using Oracle Elixir's 2022 competitive match dataset, a file which contains 149,400 rows and an equally staggering 123 rows.
Luckily, we're only interested in a select few.
`firstblood, firstdragon, firstherald, firstbaron, firsttower, firstmidtower, firsttothreetowers`
These columns contain information about which team reached a certain milestone first. 
- Firstblood shows which team got the first kill of the game.
- Firstdragon shows which team got the first dragon kill of the game.
- Firstherald shows which team got the first herald of the game.
- Firstbaron shows which team killed Baron first.
- Firsttower shows which team destroyed the enemy's lane tower first.
- Firstmidtower shows which team destroyed the enemy's midlane tower first.
`teamname, teamid'
These columns are fairly self-explanatory. While they are not used extensively, they are important to keep in for interpretability.
`teamkills, teamdeaths, result, totalgold`
These columns serve as our indicators of performance and overall game state.
- Teamkills shows how many kills the team achieved during the game.
- Teamdeaths show how many deaths the team took during the game.
- Result shows whether the team won or lost.
- Totalgold shows the quantity of gold the team received over the game.

## Cleaning and EDA

