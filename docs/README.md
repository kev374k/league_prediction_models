# league_prediction_models

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

- [Problem Identification](#problem_identification)
- [Baseline Model](#baseline_model)
  - [Reasoning](#reasoning)
  - [Model Features](#baseline_model_features)
- [Final Mode](#final_mode)
  - [New Features](#new_features)
  - [Player Relationships](#player_relationships)
  - [Team Relationships](#team_relationships)  
  - [Model Description](#model_description)
- [Fairness Analysis](#fairness_analysis)
- [Authors](#authors)

## Problem Identification

One problem that capsulates the most important question in League of Legends is about who is going to win? Each and every game has a singular answer, yet it is more complex than most people think.

There are a variety of factors that go into each and every game, which is why we analyzed a dataset of competitive games from the year 2022. In this data, each game was 12 lines long. 10 for the players (5 from each team), and 2 for the teams (where data was summarized). 

Essentially, each line of data was an individual/team that was playing in a certain game, and eahc line had columns that gave stats for each player. This included important stats like Kills, Deaths, Assists, as well as others that only become more complicated as the game goes on. 

To start, we did some data cleaning. We got rid of columns that we thought weren't incredibly important to our question (like the types of dragons, champion bans, game length, etc), and took columns that we thought would be important to deciding the winner of a specific match. Particularly, columns like ```'teamname', 'position', 'kills', 'deaths', 'assists', 'firstblood'```, and more.

Additionally, in order to get more complete and overall better data for predictions, we trained our model only on completed data (where the column ```datacompleteness``` was True)

Since winning a game has a value of either True or False (1s and 0s), we decided that this was a classification problem, a binary classification. We are trying to predict the response variable, ```result```, as either True or False scores, since it's the most important variable to success in League of Legends. 

On the other hand, the metric we are using the evaluate our model is accuracy, because we want to determine how well our model can predict if a team wins or loses. We wanted to use accuracy rather than something like an F1-score because there wasn't really a larger significance to having false positives or false negatives as compared to the true positives/negatives. We didn't want to place an emphasis on other things like precision or recall, because those weren't significant in the long run. 

During our "time of prediction", we could only use data from the set given to us, so there were no outside sources other than our downloaded data. This also means that our predictions have no influence from previous/future seasons, meaning this is an isolated dataset that we can use to predict on.