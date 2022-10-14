---
title:  "Tasks and Questions"
layout: post
---


## How we downloaded the dataset
In the beginning we start from the unofficial website so we figured out we have to know what does mean the ten digits number XXXXXXXXXX that we need to choose as GAME_ID
We analyze them and understand that


the first 4 digits identify the season for example 2016 for the 2016-2017 season. The next 2 digits gives the type of game
- 01 = preseason
-  02 = regular season
-  03 = playoffs
-  04 = all-star
The final 4 digits identify the specific game number as follow 
- regular season and preseason games
- playoff games, the 2nd digit of the specific number gives the round of the playoffs, 
-3rd digit specifies the matchup, and 
-4th digit specifies the game (out of 7).
after that we try to figure it out the structure of the NHL database.
so we look in to some small parts and save in Jason file and try to see what actually we can do how we can sparssing data in python .
we see that we have to work on this Jason file to be able to access to the details 
we tried recursion function to go to nested dictionary in the Jason file but we had som problem about to passing all informations.