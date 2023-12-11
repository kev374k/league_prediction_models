# league_prediDction_models

League of Legends stands as a titan in esports. Arguably the most popular esport in the world, it requires skill, patient, and talent to master. Yet how do teams actually win?

Pro teams and players require a lot of studying in order to predict correctly. Identifying gaps in vision, making sure you kill all the minions, and practice all take a long time to understand and figure out — yet all are essential aspects for teams winning games in professional play. So, how can we make a model to predict what team or even player would win a game?

This prediction model delves into how we can better predict a League of Legends team or player winning each individual match. We got this data from the publicly available pro match data from Oracle's Elixir, and thank those who gave us the data to play with. 

## Contents

- [Problem Identification](#problem-identification)
- [Baseline Model](#baseline-model)
  - [Reasoning](#reasoning)
  - [Model Features](#baseline-model-features)
- [Final Mode](#final-mode)
  - [New Features](#new-features)
  - [Player Relationships](#player-relationships)
  - [Team Relationships](#team-relationships)  
  - [Model Description](#model-description)
- [Fairness Analysis](#fairness-analysis)
- [Authors](#authors)

## Problem Identification

One problem that capsulates the most important question n League of Legends is about who is going to win? Each and every game has a singular answer, yet it is more complex than most people think.

There are a variety of factors that go into each and every game, which is why we analyzed a dataset of competitive games from the year 2022. In this data, each game was 12 lines long. 10 for the players (5 from each team), and 2 for the teams (where data was summarized). 

Essentially, each line of data was an individual/team that was playing in a certain game, and each line had columns that gave stats for each player. This included important stats like Kills, Deaths, Assists, as well as others that only become more complicated as the game goes on. 

To start, we did some data cleaning. We got rid of columns that we thought weren't incredibly important to our question (like the types of dragons, champion bans, game length, etc), and took columns that we thought would be important to deciding the winner of a specific match. Particularly, columns like ```'teamname', 'position', 'kills', 'deaths', 'assists', 'firstblood'``` were columns we thought we important for our overall prediction.

Additionally, in order to get more complete and overall better data for predictions, we trained our model only on completed data (where the column ```datacompleteness``` was True) because we observed that rows that weren't complete often had missing data.

Since winning a game has a value of either True or False (1s and 0s), we decided that this was a classification problem, a binary classification. We are trying to predict the response variable, ```result```, as either True or False scores, since it's the most important variable to success in League of Legends. 

On the other hand, the metric we are using the evaluate our model is accuracy, because we want to determine how well our model can predict if a team wins or loses. We wanted to use accuracy rather than something like an F1-score because there wasn't really a larger significance to having false positives or false negatives as compared to the true positives/negatives. There's an equal amount of wins and losses (because a team either has to win or lose each game), and by treating both false negatives and false positives as equally important, we can make sure that accuracy makes the most sense for our predictin.

Finally, during our "time of prediction", we could only use data from the set given to us, so there were no outside sources/biases other than our downloaded data. This also means that our predictions have no influence from previous/future seasons, meaning this is an isolated dataset that we can use to predict on.

## Baseline Model

For our baseline model, we wanted to create something that would be able to predict wins based on only what we deemed were the most important individual player stats that happen to be available through almost every game that is played in League of Legends.

<strong>Quantitative Features:</strong> Kills, Deaths, and Assists (commonly known collectively as KDA)

<strong>Response Variable:</strong> Result (Win (1) / Lose (0))

For our encoding of these variables, since they were already discrete values, they could easily be placed into a Pipeline and into our model to train and predict data. 

<strong>Performance Baseline:</strong> For our model, we decided that a DecisionTreeClassifier would make the most sense for this simple prediction, while having a maximum depth of 3, because we presumed that a simpler model shouldn't be as complicated, only allowing for more simple and therefore straighter results. 

With this data in mind, we received an accuracy of approximately ~83%. We think this is a good baseline model that is decently accurately — it also is simple and logical in the fact that it is more likely for a team to win when players individually do well in aspects like Kills and Assists (more kills and more assists equal more gold and time) while also lowering eaths (Deaths are bad, because they give the enemy team gold and time to get objectives). Overall, we were satisifed with this baseline models' performance because of how well it did with so little to train on. 

## Final Model

<h3>New Features Added:</h3> Position, Teamname, Dragons, Barons, Heralds, Turret Plates, Towers, Inhibitors, First Blood, Double Kills, Triple Kills, Earned GPM, golddiffat15, xpdiffat15, DPM, CSPM, and VSPM. 

<strong>Why Position and Teamname Matter</strong>
As strong observes of esports ourselves, we obviously noticed a pattern as to how some teams did versus others in competitive matches. Instinctively, we understand that only one team will win Worlds, which also means that they are likely better than many other teams. For teams like T1 and DRX (both of which were in the Worlds Final), it would make sense that they would be predicted to win any given match.

On the other hand, we also postured that position matters a lot, because different positions prioritize different objectives. For instance, a position like a support is more likely to prioritize assists and helping their team with objectives, while a position like top more values kills, because for most of the early game, they are separated from the rest of their team, meaning we need to treat different positions in a different matter. 

<strong>Why Objectives Matter</strong>
Objectives like Dragons, Barons, Heralds, Turret Plates, Towers, and Inhibitors are all objectives that give teams gold and/or buffs that either help in teamfights or by taking other objectives, both of which are primary objectives and goals in League of Legends. These provide champions gold (to buy items and empower themselves), while also potentially giving them boosts in stats (i.e. Killing the Infernal Drake gives players stacking attack damage/ability power), meaning they can scale themselves and be more likely to win in future teamfights.

<strong>Why Kills, Gold, and XP Matter</strong>
For features like First Blood, Double Kills, Triple Kills, Earned GPM, golddiffat15, and xpdiffat15, we wanted to emphasis why gold and kills were both important. 

Killing also gives advantage to certain players and teams that gain the advantage. For instance, First Blood enables the killer to gain 100 gold, while the person who assisted them also gains 50 gold. Additionally, this allows the player who killed the other to gain an XP advantage, which is large, especially in the laning phase of League. 

To add on, gold is one of the most important factors in winning in League of Legends. Gold enables the usage of the shop, as well as purchasing stronger items, vision-related items like wards, speed-related items like boots, and more. These are all large parts as to why gold is extremely important, because it allows champions to snowball. Similarly, XP also allows champions to gain levels and with that, new and stronger abilities that allow both solo fighting and team fighting to be stronger.

<strong>Why Damage, CS, and Vision Score Matter</strong>

Damage, CS and Vision Score are all amplifies that help us understand why certain teams do better. Damage, of course, is self-explanatory; the more damage you do, the more likely you are to win games. Similarly, CS (killing minions) allows you to generate more gold, so more CS theoretically means a larger gold advantage. Finally, for Vision Score, although it is not felt as much compared to a stat like Gold, it measures how well a team can see the map and identify their opponents within the Fog of War (areas where you can't normally see the opponent). Having a higher vision score naturally means that a team can see the other team and find weaknesses in pathing and ganking, allowing for stronger teamfights and objective taking, all of which are found earlier.

<h3>Hyperparameters</h3>
To test whether these features are important or redundant, we examine these features and make sure that these are the right ones to choose. To do so, we graphed them out and observed the difference between winning and losing teams/players. During our testing for hyperparamters, we also observed features that we originally thought we important to the model that were later excluded because we realized they provided little value. One example is the column ```wpm```, or Wards Per Minute. Originally, we thought wards per minute would be important, because vision is such a high priority in pro play. However, when we observed the graph below, we realized that both teams that lost and won typically placed an equal amount of wards, and that it didn't ultimately play a significant role in our model.

<div style = "text-align:center">
  <iframe src="assets/wpm_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

During our process, we identified what we thought were more solo relational features; these included things like CSPM, Kills, Deaths, Assists, and more, all of which we tested on filtered data that only included players (not teams' general stat summary)

<div style = "text-align:center">
  <iframe src="assets/kills_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/deaths_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/assists_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/doublekills_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/triplekills_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/firstbloodkill_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/cspm_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/dpm_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/earned_gpm_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/vspm_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/golddiffat15_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/xpdiffat15_result_player.html" width=800 height=600 frameBorder=0></iframe>
</div>

On the other hand, we then observed those that we observed were more team-reliant features, like barons, dragons, heralds, and more, which were then tested on a filtered dataframe that only had the teams' summary stats.

<div style = "text-align:center">
  <iframe src="assets/barons_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/dragons_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/inhibitors_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/towers_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

<div style = "text-align:center">
  <iframe src="assets/turretplates_result_relationship.html" width=800 height=600 frameBorder=0></iframe>
</div>

<h3>Model Description</h3>
Ultimately, we decided to make a model based on either the DecisionTreeClassifier or LogisticRegression. First, we tested the DecisionTreeClassifier by using the GridSearchCV function from sklearn to find the best parameters for our model, which was a maximum depth of 10 and had 5 samples required to split. This created an accuracy of ~90.5%. Then, while testing the LogisticRegression model, we got our final model, which reached a <strong>~91.5% accuracy rate</strong>. Overall, we were satisfied with this accuracy while adding what we deemed as good features to our model. Our optimization of the hyperparameters and features helped us create a model that can predict a match a very high percentage of the time.

<strong>Quantitative Varaibles</strong>
Our discrete variables included ```'kills', 'deaths', 'assists', 'dragons', 'barons', 'heralds', 'firstblood', 'doublekills', 'triplekills', 'dpm', 'cspm', 'vspm', 'earned gpm', 'turretplates', 'towers', and 'inhibitors'```. For these, we standardized ```'dpm', 'cspm', 'vspm', 'golddiffat15', and 'xpdiffat15'```, because we deemed them to show a fluctuation and difference especially in regards to explaining how much better a player/team does compared to others.

<strong>Qualitative Variables</strong>
For our qualitative varibales, we chose ```'position', and 'teamname'```. We then used the OneHotEncoder in order to convert them into quantitative columns, because they were both ordinal data types.

## Authors

**Kevin Wong**
- <https://github.com/kev374k>
- <https://twitter.com/justkevin999>

**Andrew Yang**
- <https://github.com/AHY123>


