## Introduction
League of Legends is a highly popular MOBA developed by Riot Games. Enjoyed by millions, it has a flourishing casual and competitive gaming scene. The dataset that we are looking into contains a wealth of information about games played in the competitive environment, such as stats about each teams performance, meta information about the teams themselves, and whether certain milestones are achieved. It is important to note that competitve League is almost exclusively played on Summoner's Rift, League's most popular map. As a casual player or commentator for League, there may be too much information about the game to follow along. How can we find something simple to focus on in order to understand how the game is progressing?

There are many indicators within a match that relate to the overall strength of a team. The team who is "first" to certain milestones will most likely be doing well. Because of this, I propose the following question: Is it possible to predict the outcome of an entire match based on how each team does in reaching "firsts"?

We are using Oracle Elixir's 2022 competitive match dataset, a file which contains 149,400 rows and an equally staggering 123 columns.
Luckily, we're only interested in a select few.

`firstblood, firstdragon, firstherald, firstbaron, firsttower, firstmidtower, firsttothreetowers`<br>
These columns contain information about which team reached a certain milestone first. 
- Firstblood shows which team got the first kill of the game.
- Firstdragon shows which team got the first dragon kill of the game.
- Firstherald shows which team got the first herald of the game.
- Firstbaron shows which team killed Baron first.
- Firsttower shows which team destroyed the enemy's lane tower first.
- Firstmidtower shows which team destroyed the enemy's midlane tower first.
- Firsttothreetowers shows which team reached three enemy towers destroyed first.

`teamname, teamid`<br>
These columns are fairly self-explanatory. While they are not used extensively, they are important to keep in for interpretability.

`teamkills, teamdeaths, result, totalgold`<br>
These columns serve as our indicators of performance and overall game state.
- Teamkills shows how many kills the team achieved during the game.
- Teamdeaths show how many deaths the team took during the game.
- Result shows whether the team won or lost.
- Totalgold shows the quantity of gold the team received over the game.

## Cleaning and EDA

### Data Cleaning
The first thing I had to do was to figure out what columns were contained in the dataset. Because of its size, most programs that I had to view CSVs could not handle it. Thus, I was left to simply loop through the column names and print them out. This left me with the columns specified in the introduction that I identified as being necessary. This was done by keeping every column that contained the word "first", "team", and some manually selected columns as shown above.

The second issue that the dataset documentation noted was that the dataset contained both aggregate and individual data. Because I was only interested in team performance, I had to find a way to remove the individual rows. I did this by noting that the columns related to team performance, such as `Firsttower` were NaNs in the player rows. I simply created a filter to only keep the rows where `Firsttower` existed.

Now, the bulk of my data cleaning is here. Even though I selected a good subset of the columns, many of them were still useless. For example, `Firstbloodkill` sounds similar to `Firstblood`, but it is meant to signal which player got the first kill, rather than which team got the first kill. These types of columns had to be removed. Next, I changed the datatypes of many of the columns. The "firsts" were converted to booleans from integers (represented by objects), in order to make them more readable and usable. A `firsttotal` column was added as an aggregate stat, although each "firsts" were maintained in case a future use was necessary. I converted `teamkills, teamdeaths, totalgold` into integers in order to make them usable, and finally added a `kdr` column for exploratory purposes.

Here, I show part of my final cleaned dataset.

| firstblood   | firstdragon   | firstherald   | firstbaron   | firsttower   | firstmidtower   | firsttothreetowers   | teamname                      | teamid                                  |   teamkills |   teamdeaths | result   |   totalgold |   firsttotal |      kdr |
|:-------------|:--------------|:--------------|:-------------|:-------------|:----------------|:---------------------|:------------------------------|:----------------------------------------|------------:|-------------:|:---------|------------:|-------------:|---------:|
| True         | False         | True          | False        | True         | True            | True                 | Fredit BRION Challengers      | oe:team:68911b3329146587617ab2973106e23 |           9 |           19 | Lost     |       47070 |            5 | 0.473684 |
| False        | True          | False         | False        | False        | False           | False                | Nongshim RedForce Challengers | oe:team:d2dc3681437e2beb2bb4742477108ff |          19 |            9 | Won      |       52617 |            1 | 2.11111  |
| False        | False         | True          | False        | False        | False           | False                | T1 Challengers                | oe:team:6dcacec00a6ba7576c5ab7f30c995cd |           3 |           16 | Lost     |       57629 |            1 | 0.1875   |
| True         | True          | False         | True         | True         | True            | True                 | Liiv SANDBOX Challengers      | oe:team:5380cdbc2ad2b8082624f48f99f6672 |          16 |            3 | Won      |       71004 |            6 | 5.33333  |
| False        | True          | False         | True         | True         | True            | True                 | KT Rolster Challengers        | oe:team:b9733b8e8aa341319bbaf1035198a28 |          14 |            5 | Won      |       62868 |            5 | 2.8      |

##### Quick Note
If you are well-versed in video game theory, you may ask how a KDR column is possible if deaths=0. It is certainly possible that a team can go an entire round without dying (and indeed, the data does show that this happens multiple times.) While it is not ideal, I have elected to treat the cases where deaths=0 as deaths=1. I believe this is fair, as it is the most accurate KDR for the team that isn't infinity. However, this is only for the purposes of the `kdr` column. The `teamdeaths` column is left unmodified.

### Univariate Analysis
<iframe src="assets/firsttotal_dist.html" width=800 height=600 frameBorder=0></iframe>
While this univariate analysis may not seem particular interesting, it is the asymmetry that I think shows something unexpected in the data. Although there are 7 "firsts" contained within our dataframe, and you would expect data to be paired (for example, one team with 0 "firsts" should have a corresponding opponent of 7 "firsts"), the difference in frequency shows that this is not the case. We can say that these milestones are not always achieved within a game.

### Bivariate Analysis
<iframe src="assets/gold_firsttotal_dist.html" width=800 height=600 frameBorder=0></iframe>
Here, we plot the distribution of `totalgold`, categorized by `firsttotals`. We see an interesting trend. Each marginal distribution looks somewhat normal, with the mean and variance seemingly increasing from 0 to 3, but *decreasing* from 4 to 7. One possible explanation is that teams who get more "firsts" (and are therefore better at the game) end their rounds quicker, decreasing the amount of time that gold can be obtained. This is another potential avenue of exploration, as the dataset does contain information about the length of each game. However, I felt that time is not very indicitive of a team's performance; hence, it was left out.

### Interesting Aggregates

|   firsttotal |   teamdeaths |   teamkills |   totalgold |
|-------------:|-------------:|------------:|------------:|
|            0 |     19.6218  |     7.60144 |     45971.3 |
|            1 |     19.0026  |     9.60525 |     50631.4 |
|            2 |     17.9601  |    11.9968  |     55558.4 |
|            3 |     16.0841  |    14.1286  |     59075.8 |
|            4 |     13.977   |    16.2089  |     61038.4 |
|            5 |     11.7692  |    18.0329  |     61282.7 |
|            6 |      9.62439 |    19.0398  |     60229.6 |
|            7 |      7.86161 |    19.5742  |     58941.9 |

Here with our pivot table indexed by `firsttotal`, we can see some of the trends demonstrated earlier, specifically `totalgold`. I elected to aggregate using means of the results. Here, this pivot tables allows us to see the overall trend that the `firsttotal` has on the results, and this indeed verifies the trend that we saw in our bivariate analysis.


## Assessment of Missingness

#### NMAR Analysis
There are most likely not NMAR data in the set. Because the data is autocollected statistics about games, data which is left out will either be from outside confounding factors (for example, lower level leagues may not have as robust reporting systems) or missing by design. Since the data collection process shouldn't be biased on the data itself --unlike for example a self-reporting system-- it is missing at random.

#### Missingness Dependency
While creating the cleaned dataset, I noticed that some of the team names and IDs were NaNs. Therefore, I wanted to find out the missingness dependency of these columns. The `teamid` column was used to avoid any potential duplicate names.

For this first missingness test, I tested `teamid` against `league`, the division that each match was played in. I used the TVD as my test statistic for the permutation test.

<iframe src="assets/tvds_mar.html" width=800 height=600 frameBorder=0></iframe>

Here, I show the distribution of our observed TVD (a red dashed line) and the distribution of TVDs under random permutations (shown in blue bars). As we can see, the observed TVD is much higher than the random permutation TVDs. In fact, if we calculate the observed TVD and the p-value of the observed TVD, we get ~0. Therefore, we'd make the conclusion that `teamid`'s missingness depends on `league.`

On the other hand, let's look at this second missingness test, where I put `teamid` against a result column, `totalgold`.

<iframe src="assets/means_mcar.html" width=800 height=600 frameBorder=0></iframe>

Here, I elect to use the absolute difference in means as my test statistic. Under the random permutations, we can see that our observed absolute mean difference falls within a reasonable range. The calculated p-value in this instance is approximately ~0.13. This is a fairly common result -- therefore, `teamid`'s missingness does not depend on `totalgold`.