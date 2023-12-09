# A League of Legends Competitive Analysis

In the ever-evolving landscape of esports, one game stands out as a titan among
its peers—League of Legends. As we stride into the heart of 2023, the 
competitive scene of this multiplayer online battle arena (MOBA) continues to 
captivate audiences with its strategic depth, intense team dynamics, and it's
players' relentless pursuit of glory on the Summoner's Rift.

The year 2022 has witnessed a symphony of skill, innovation, and calculated 
risk-taking as professional teams from around the world converge to compete in 
the League of Legends global ecosystem. From the intense battles in regional 
leagues to the grandeur of international tournaments, each match serves as a 
canvas where players paint their narratives, leaving an indelible mark on the 
competitive landscape.

This analysis delves into the multifaceted dimensions of League of Legends 
esports in 2022. From champion picks and bans to macro and micro plays, we 
embark on a journey to dissect the strategies, meta shifts, and standout 
moments that have defined the competitive metagame this year. Join us as we 
unravel the narrative of League of Legends competitive matches in 2022.

As we navigate through the statistics, trends, and standout performances, 
our aim is to provide a comprehensive exploration of the evolving dynamics 
within the League of Legends esports sphere. Buckle up as we delve into the 
heart of the action, unraveling the tales of triumphs, setbacks, and the 
ever-shifting sands of competitive play in the world of League of Legends in 2022.

## Contents

- [Introduction](#introduction)
- [Cleaning and EDA](#cleaning-and-EDA)
  - [Cleaning](#cleaning)
  - [Univariate Analysis](#univariate-analysis)
  - [Bivariate Analysis](#bivariate-analysis)
  - [Interesting Aggregates](#interesting-aggregates)
- [Assessment of Missingness](#assessment-of-missingness)
  - [NMAR Analysis](#nmar-analysis)
  - [Missingness Dependency](#missingness-dependency)  
- [Hypothesis Testing](#hypothesis-testing)
- [Authors](#authors)


## Introduction

In this analysis, we wanted to analyze the best of the best: Competitive Games,
and we decided to use the most recent completed season, the 2022 season, to 
show this. In our analysis, we will analyze one specific question that we 
believe is very important to how winning games, the primary objective of 
League, is decided:

<strong>Does a winning top lane affect the probability of a 
team winning the game?</strong>

Top lane, one of the five positions in League of Legends, is typically observed
as a solo lane, meaning they often don't receive too much help from other 
players on their team and especially the jungle, which typically (at least 
recently) prioritizes objectives like Dragons, Heralds, and Barons instead of 
helping near top. 

Therefore, we wanted to ask this question, because it is especially relevant—we
observed (with our eyes) that top lanes often didn't really help a team unless 
they were doing exceptionally well and could carry the team, meaning they 
observed a very large gold lead. Therefore, we wanted to see how often they 
actually made teams better, and if they did, was it significant to winning in 
pro-play. In doing so, we looked through 149400 rows (or 12450 games) worth of 
data. In filtering our data, we realized that of the 123 columns that were 
provided, we only needed these 7 columns to help us answer our question:
```['gameid', 'datacompleteness','side', 'position', 'result', 'xpat15', 'xpdiffat15']```.

For these columns, 'gameid' allowed us to differentiate different games, 
'datacompleteness' allowed us to verify if the data was complete (and therefore
usable), 'position' allowed us to get the position we wanted (top). The two xp
columns allowed us to decide how well a top lane player was doing relative to
their opponent and also allowed us to create our test statistic for the 
hypothesis test.

Description of columns:
- ```'gameid'```: The unique identifiers for the games that occurred. Used to
differentiate each game.
- ```'datacompleteness'```: Indicator of whether the data for this row is complete or 
missing certain information. Used to check if the row may be missing data in
other relevant columns.
- ```'side'```: Differentiates the players in the game as ```'blue'``` or 
```'red'``` based on the side they were playing as.
- ```'position'```: Describes which position the player played as.
- ```'result```: Describes whether the team of the player won in the end or 
not. ```True``` when the team won, ```False``` when the team lost.
- ```'xpat15```: The amount of xp the player had by the 15-minute mark in the 
game. Using the 15-minute mark because before 15 minutes, players usually stay
in their own lane, which allows us to decide which top laner was better in the
laning phase.
- ```xpdiffat15```: The difference in xp between a player and their opponent in
the same position. The sign of the value is positive if the player was in the
lead. The sign is negative if the opponent was in the lead.

## Cleaning and EDA

One of the most important (and time-consuming) processes that data scientists 
go through is cleaning the data. Here, we explore the cleaning process as well 
as the Exploratory Data Analysis that led us to getting our results.

### Cleaning

First, we had to get the data from somewhere. Fortunately, Oracle has data of 
all the competitive matches that occur each year, so all we had to do was 
download the csv file for 2022 data and parse it into a pandas DataFrame. 

Then, we worked on creating a dataframe that allowed us to filter for the data
that was needed for the analysis. After getting our data, we converted a lot of
non-boolean values into boolean values like True and False to help our 
filtering in the future easier. For example, the columns 'datacompleteness', 
and 'result' were converted into 'True' and 'False' in order 
to make our data make more sense. Additionally, in this section, we also were 
able to locate the columns that we needed, which were 
```['gameid', 'datacompleteness','side', 'position', 'result', 'xpat15', 'xpdiffat15']```

At this point, we also wanted filter our data in order to have a dataframe that
only consisted of complete data ('datacompleteness' == True) and data that only
consisted of the top lane position ('position' == 'top'). This would allow us 
easy access the data we want in the future. 

<strong>
  Below is the head() of our cleaned and filtered DataFrame:
</strong>

| gameid                | datacompleteness   | side   | position   | result   |   xpat15 |   xpdiffat15 |
|:----------------------|:-------------------|:-------|:-----------|:---------|---------:|-------------:|
| ESPORTSTMNT01_2690210 | True               | Blue   | top        | False    |     7560 |          345 |
| ESPORTSTMNT01_2690210 | True               | Red    | top        | True     |     7215 |         -345 |
| ESPORTSTMNT01_2690219 | True               | Blue   | top        | False    |     7020 |         -652 |
| ESPORTSTMNT01_2690219 | True               | Red    | top        | True     |     7672 |          652 |
| ESPORTSTMNT01_2690227 | True               | Blue   | top        | True     |     7759 |           85 |

### Univariate Analysis

One of the most important stats regarding the performance of players in a game 
is ```'xpat15'```. It indicates how well the player did during the laning phase
(before the 15-minute mark).

Below is the histogram representing the 
distribution of the ```'xpat15'``` column and some relevant descriptive stats:

<div style = "text-align:center">
  <iframe src="assets/univariate_xpat15_hist.html" width=800 height=600 frameBorder=0></iframe>
</div>

Descriptive statistics for the ```'xpat15'``` column:
- mean   : 7278.1
- median : 7332.0
- mode   : [7721.0]
- max    : 9785.0
- min    : 1729.0
- std    : 628.6

This graph shows that the distribution of ```'xpat15'``` is left-skewed.
Putting it simply, left-skewed histograms have a 'tail' to the left, indicating
that large extreme values are more likely than small extreme values. The
explanation for this could be attributed to how players gain xp in the game.
Because xp cannot be passively gained during the game, you only become
significantly behind in xp when you are losing in lane. The skewness could also
be explained by how one player losing xp doesn't directly mean their opponent
is gaining xp. This observation on xpat15 could be useful for our future
analysis.

### Bivariate Analysis

Our research question is concerned with the stats of the top player that affect
the chances of the team winning overall. So, we can now investigate the
relationship between the team winning or not and the top player's xp level.

Below is our bar chart comparing the difference of means for player's xp between
winning teams and losing teams as well as some relevant stats:

<div style = "text-align:center">
  <iframe src="assets/bivariante_xpat15_bar.html" width=800 height=600 frameBorder=0></iframe>
</div>

Stats for the 'xpat15' column:
- Absolute difference     : 226.9
- Proportional difference : 3.07%

The fact that the proportional difference for xpat15 only differed by around 3%
is an extremely surprising observation. This seems to indicate that the xp of
the top lane is not a significant factor in determining the outcome of the game.
Which opposed our original idea that we can use the xp of the top lane to predict
if they will increase the chances of their team winning.

### Interesting Aggregates 

We can use groupby to find patterns and aggregates for categorical data. 
Unfortunately, xp as data does not come in a categorical form, and because xp 
depends on many factors, we do not have sufficient information to be able to
come up with our own categories. So on a side note, we looked at the 
```'firstblood'``` and ```'firstbloodvictim'``` columns of data. First blood
represents the first player kill of the game. Usually for top lane, based on
our own experience and observation, first blood provides a significant lead for
the player that gets it and a significant disadvantage for the victim. So, we 
thought it would be interesting to look at how frequently do top laners get 
firstblood (and get firstbloodvictim) and whether it has a relationship with 
whether the team wins.

Below is the pivot table created by grouping on the result:

|      |   firstblood |   firstbloodvictim |   neither |
|:-----|-------------:|-------------------:|----------:|
| lose |    0.0648437 |          0.068433  |  0.366818 |
| win  |    0.0955417 |          0.0485973 |  0.355767 |

Marginal sum for each column:
- firstblood:          0.160385
- firstbloodvictim:    0.117030
- neither:             0.722584

What the table shows is the proportion of top laners getting first blood,
getting first blood victim and neither, separately for losing teams and winning
teams.

As expected, the table shows that for winning teams, the top lane is more 
likely to get first blood rather than becoming the victim. The table also shows
the opposite for losing teams. What is interesting is when you look at the 
marginal sums of the columns. As shown above, top laners are more likely to get
firstblood rather than becoming the victim no matter if their team won or lost.
This means that top laners are often getting firstblood on non-top laners, 
which could be an interesting observation. Furthermore, another interesting 
observation is top laners participates in firstblood events (getting the kill 
or being the victim) 27.7% of the time. So, in terms of firstblood events, 
top laners are participating more than the average of other lanes.

## Assessment of Missingness

### NMAR Analysis

For data that could be qualified as NMAR, the data has to be essentially missing on itself. The reason why it's missing is likely to do with it's own value.

For this dataset, we looked through a lot of the columns that had missing data, and observed that columns like 'dragons' and 'opp_dragons', and their types of dragons ('infernals', 'mountains', 'clouds', 'oceans', and 'chemtechs') were missing very often. We theorize that these columns are NMAR, because if the entry in these columns are NaN, this likely means that there were no dragons or no specific dragon killed/found in that game, meaning the data was likely just not inputed instead of having '0' in the entry. For these, some additional data that could technically explain missingness could potentially be the number of dragons present in the game (which also has a lot of NaN values), because just intuitively, the more dragons there are in a game, the higher the likelihood that there is a larger variety of dragons.

Below is the table presenting the number of missing values in each dragon related column (keeping in mind we originally have 149400 rows):

|   firstdragon |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |
|--------------:|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|
|        128138 |    124500 |        124500 |            128226 |                128226 |      128138 |      128138 |   128138 |   128138 |      128226 |

### Missingness Dependency

For missingness, we wanted to analyze why some data was marked complete while others were marked partial. In our observations, we noticed that a lot of the data from the 'LPL' league, which is from China, was partially missing, especially the columns at the end of the DataFrame, which we needed. We wanted to see if this was actually a pattern, because from the mini-observations we had from looking through the data, it looked like most of the missing/incomplete data was from Chinese leagues, like the 'LPL' and the 'LDL'.

Therefore, we tested our original DataFrame, df, and assessed which columns were missing data regularly. Specifically, we noticed that one of our most important columns, 'opp_csat15', was missing very often and also at the same amounts as other columns near the end of the DataFrame.

In order to test these out, we singled-out variables that we thought would have a correlation to whether or not 'opp_csat15' was missing. First, we wanted to decide our test statistic from our normal data. In doing this, we took all of our rows where 'opp_csat15' was NaN, and filtered out to get the columns of ['league', 'split', 'playoffs', 'patch', and 'position']. 

|     | league | split  | playoffs | patch | position |
|----:|:-------|:-------|---------:|------:|:---------|
|  24 | LPL    | Spring |        0 | 12.01 | top      |
|  25 | LPL    | Spring |        0 | 12.01 | jng      |
|  26 | LPL    | Spring |        0 | 12.01 | mid      |
|  27 | LPL    | Spring |        0 | 12.01 | bot      |
|  28 | LPL    | Spring |        0 | 12.01 | sup      |
| ... | ...    | ...    |      ... |   ... | ...      |

By using the groupby() method, we were able to test out our columns and see the missingness of certain leagues. Jumping to our test statistic, we noticed that the Missingness Distribution of 'league' compared to the others that we tested seemed a lot more lopsided. 

<div style = "text-align:center">
  <iframe src="assets/league_missingness_distribution.html" width=800 height=600 frameBorder=0></iframe>
</div>

When looking at this graph, it looks like missingness is extreme in leagues like the 'LPL' and 'LDL', like we observed earlier when analyzing the dataset. In fact, with 'LDL' carrying a Missingness Proportion of 0.517867, meaning for all missing values in 'opp_csat15', the 'LDL' is the league over 50% of the time.

Although this can be explained by the 'LDL' having over 50% of all competitive games, we want to test the dataset in full by permuting the columns that we tested in order to see if it's possible to have a league have a value as high as 0.517867

We first permutate the ```'league', 'split', 'playoffs', 'patch' and 'position'```
columns.

Then, by grouping 'league' again, we randomized all of the leagues and looked at the null values once again, to see if any league could top over 50% rate of missingness. 

<iframe src = "assets/permutated_league_missingness.html" width = 800 height = 600 frameBorder = 0></iframe>

As you can see, even our league with the largest share of missing values when permutated cannot even hit 0.1 of the total missingness on column 'opp_csat15'. This implies that the missingness of this column is likely Missing at Random, dependent on the 'league' column. To test this, let's use a pair of hypotheses and a significance level of 0.01 with 1000 trial runs.


<strong>
Null Hypothesis:</strong> Data that is missing comes from the same 'league' distribution as all other data

<strong>Alternative Hypothesis:</strong> Data that is missing is significantly more likely to be one 'league' than others


We simulated this test 1000 times and returned a p-value of 0.0.

Here, our p-value suggests that we can reject our null hypothesis, meaning that it is likely that data missing from 'opp_csat15' is significantly more likely to be in at least one 'league' than others - this in turn means that 'opp_csat15' has data that is Missing At Random. 

Now, let's find a column that 'opp_csat15' does not depend on. Intuitively, 'opp_csat15' shouldn't depend on columns that are aggregated throughout our data extremely evenly; that is, columns that have no relation and are evenly spread out should not have any correlation with 'opp_csat15'. One of these columns is 'position', which will always be even regardless of the matches, since all games will have 2 of each position.

Here, let's test what the position missingness of 'opp_csat15' looks like when we groupby() on the 'position' column.

<div style = "text-align:center">
  <iframe src = "assets/position_missingness.html" width = 800 height = 600 frameBorder = 0></iframe>
</div>

Here, we can see missingness is split dead even between all the positions, which we expected. This gives us a test statistic of (1/6). Now, in order to test if 'opp_csat15' is dependent on 'position', we want to do another permutation test to test our hypothesis that the missingness of 'opp_csat15' comes from same distribution as the whole of 'opp_csat15', tested at the 0.01 significance level

<strong>Null Hypothesis:</strong> The missingness of NA values in the 'opp_csat15' column comes from the same distribution regardless of the position player's have played

<strong>Alternative Hypothesis:</strong> The missingness of NA values in 'opp_csat15' column is dependent on a position played

We performed this permutations test by shuffling the position column and then
calculating the test statistic.

Here, our position_pval equates to around ~0.5, meaning that in this case, we fail to reject the null hypothesis, meaning there is no sufficient evidence that the 'position' column plays an effect on the missingness of 'opp_csat15'. For an even more thorough example, here is a permutated version of our earlier graph, to test how often a position went over 1/6.

<div style = "text-align:center">
  <iframe src = "assets/permutated_position_missingness.html" width = 800 height = 600 frameBorder = 0></iframe>
</div>

As you can see, this happened very often, because at the end of the day, the missingness of 'opp_csat15' never relied on 'position'.

## Hypothesis Testing

Our research question since the start of this project has been:

<strong>Does the performance of the top lane player during the laning phase affect the team's winning chances?</strong>

For our hypothesis test, we wanted to continue answering this question while incorporating what we learned and observed from the previous components. Specifically, we want to find out whether ```'xpat15'```is actually a good metric to measure the performance of top laners? As such, our pair of hypotheses are:

<strong> Null : Having a winning top lane, determined as the player with higher xp, does not significantly affect the probability of the team winning overall.</strong>

<strong> Alt  : Having a winning top lane, determined as the player with higher xp, significantly increases the probability of the team winning overall.
</strong>


To further operationalize our hypothesis, we have chosen a specific test statistic that quantifies the disparity in overall team victory rates between top lanes that are considered winning and those that are not. This test statistic is the difference between the proportion of overall wins for winning top lanes and the proportion of overall wins for losing top lanes.

Performing this permutation test, we add a column to our data indicating whether
the top lane had higher xp or lower xp than the opponent. We then drop the rows
with equal xp and thus has no decisive winning side. Finally, we only keep columns
that we need for this permutation test.

Below is what the DataFrame prepared for permutations test looks like:

| result | winlane |
|:-------|:--------|
| False  | True    |
| True   | False   |
| False  | False   |
| True   | True    |
| True   | True    |
| ...    | ...     |

Finally, we perform the permutation test at a significance level of 0.05 using
1000 simulations of the test statistic.

Below is the histogram of the distribution of simulated test statistics:

<div style = "text-align:center">
  <iframe src = "assets/permutation_distribution_test_stat.html" width = 800 height = 600 frameBorder = 0></iframe>
</div>

The permutation test returns a p-value of 0.0.
Under the null hypothesis, we rarely see differences in probability as large as the observed statistic (0.0 < 0.05).

Thus, we can reject the null hypothesis and have sufficient evidence to support the alternate hypothesis: suggesting having a winning top lane, defined as the player with more xp, significantly increases the chance of the team winning overall.

## Authors

**Kevin Wong**
- <https://github.com/kev374k>
- <https://twitter.com/justkevin999>

**Andrew Yang**
- <https://github.com/AHY123>
