# March_Madness

## Authors
The following people collaborated on this project as part of our final grade for the University of Denver 2021-2022 (24wk) Bootcamp for Data Analytics.  Click on their names to visit their GitHub pages to learn more about them and their work:
* [Bryan Wenger](https://github.com/bwengerDU)
* [Philip Kirwan](https://github.com/pheel24)
* [Baiyu Hua](https://github.com/harryhua2021)
* [Bart Black](https://github.com/bartblack13)

You can also visit the project's deployed website here and visit the public Tableau page here. 

## Purpose
Every Year, millions of people fill out a March Madness Bracket and try to predict the winners of each matchup, hoping that they can correctly pick the most winning teams and potentially win some money from friends, co-workers, or from online betting.  In fact, Nielsen said in a 2019 study ([study link](https://www.nielsen.com/wp-content/uploads/sites/3/2019/04/case-study-brackets-and-baskest-2016.pdf)), that "According to the American Gaming Association (AGA), 40 million people filled out approximately 70 million brackets last year, and waged $9.2 billion on March Madness through office pools, Nevada sports books, offshore sites and bookmakers.  
The desire to correctly predict a winning bracket, or even a perfect bracket, is so alluring that Google hosts an annual contest for machine learning groups to try and write computer algorithms to predict the winners of March Maddness.  While the idea of using computer algorithms to increase the odds of a perfect bracket seems like an obvious solution, the reality is the odds of predicting a perfect bracket are infinitesimally small.  According to numbers from the NCAA ([found here](https://www.ncaa.com/news/basketball-men/bracketiq/2022-03-10/perfect-ncaa-bracket-absurd-odds-march-madness-dream)), the odds are as follows:

* if you just guess or flip a coin - 1 in 9,223,372,036,854,775,808 - 1 in 9.2 quintillion (a quintillion = a billion billion)
* if you know a little something about basketball - 1 in 120.2 billion


**The focus of this project** was to use a supervised machine learning model to accurately predict the teams that would advance/win in each of the 6 rounds of the tournament, eventually picking the correct team to win the tournament.  As a secondary goal, we were interested in seeing if our machine learning model could win an office pool, or at minimum even beat a coin flip.  


## Method

### Data ETL
In order to obtain a dataset containing sufficient NCAA Basketball statistics to train a machine learning model, we downloaded (in excel format) 19 regular season team statistic tables (ex- personal fouls per game, blocked shots per game, etc) and 1 championship team statistics table (wins, losses, and win percentage for the tournament) across the 11 most recent seasons of Men's Division 1 Basketball from the NCAA official website (Link [here](https://stats.ncaa.org/rankings/change_sport_year_div)).  Because this project focuses on the ability of our model to predict the teams that advance through the tournament, we omitted data from the 2019-2020 year, since there was no March Maddness games due to the COVID-19 Pandemic. Our total data sets consisted of 19 sets across 11 years (209 tables).  Each of the regular season tables consisted of all teams (335-351), and each of the championship tables consited only of the teams that made it to the tournament (68).  Each table included team names and overall rank for the category.  We used Excel to transfer the rank list of each category to an aggregate file, which was then saved as a CSV file.

We then carried out the cleaning and transforming process on our data file.  For this we used Python via Jupyter Notebook with multiple libraries.  Among the many input variables, we were also interested if the team's conference was an important feature of predicting total wins during the championship.  The Team column was initially formatted as follows: Team (Conference), (ex - Villanova (Big East)).  We extracted the conference name from the first column values using regex, cleaned up the values to only display the Team Name, and created a "Conference" column with the extracted values (**See Figure 1a, 1b, and 1c**).  We also converted the "Tournament Wins" column, containing values 0-6, into binary columns for each round, for win and loss (1 and 0, respectively) (**See Figure 2**). Since the "Conference" column values were categorical, we set each value to a number using the OneHotEncoder Method.   

Cleaning and transforming the data resulted in 53 columns, including the team name.

![This is an image](https://github.com/bwengerDU/March_Madness/blob/main/Data_Creation/Bart/Regex_team_name_conference.png)<br><br>
**Figure 1a:** Regex script for extracting the conference name from between the parenthesis out into a new column; tested with www.regex101.com <br><br>

![This is an image](https://github.com/bwengerDU/March_Madness/blob/main/Data_Creation/Bart/Regex_extract_replace_combo.png)<br><br>
**Figure 1b & c:** Python script using Regex script for extracting Conference name (top); Python script using Regex script for "deleting" Conference name and parentheses (bottom) <br><br>

![This is an image](https://github.com/bwengerDU/March_Madness/blob/main/Data_Creation/Bart/binary_wins-loss_func.png)<br><br>
**Figure 2:** Python script to convert tournament wins to binary win-loss category for each round <br><br>


### Model Choice and Training
Having 6 different binary win-loss columns required that we have 6 different targets, or 6 different rounds of training and predictions, etc.  We ran various different models, focusing on the classification models (Logistic Regression, Decision Tree, Random Forest, and Gradient Boosting).  Based on prelimiary model metrics we decided to use the RandomForestClassifier model to make our predictions.  To do this, we performed the following for each of 6 rounds:

* Dropped the name column and set the corresponding win-loss column to the y-data, and used the remaning columns as our X-data
* Scaled the data, split it into test and training data sets, initiated a RandomForest instance, and fit the data to our model
* Generated win-loss predictions
* Generated model metrics: accuracy scores, precision scores, recall/sensitivity scores, F1-scores, and confusion matrices
* Determined which team stats were the most important per round (feature importance lists)

### Running Model on Unknown Data
Once our model was trained for each round, we loaded our unknown data set (from the 2021-2022 season).  After loading the unknown data into Jupyter Notebook, we followed a similar approach per round as when we trainied out model, doing the following:

* Scaled the data, loaded the data into our model
* Generated win-loss predictions 
* Generated a list of advancing team names per round
* Used the predicted losses in combination with their corresponding team names to drop non-advancing teams from the data input for the subsequent round


## Results
As initially mentioned, we set out to develop a machine learning model that could predict a list of advancing teams per round throughout the March Madness Tournament, hoping to pick the winning team.  We also determined if our model could win an office pool. 

During the training phase of our project, we were hopeful that the RandomForest Model was a great choice.  When we compared the model metrics to that of other models, including the Logistic regression model, the RandomForestClassifier outperformed the other models, but achieving higher accurcy scores per round prooved to be challenging.  As can be seen from the graphs, our model started off with a 66.45% accuracy rate, but eventually dropped to a 33.33% accuracy (**See Figure 3**).  This was still better than the Logistic Regression on average.  We argue that even with significant drop and a low accuracy score of 33.33%, that our model still out performs a person selecting the winner at random, which would have a 1.56% (1/64) chance of a correct pick.
When comparing our model to the Logistic Regression model, we had slightly better perfomance for 4 rounds.  The biggest downfall of the Logistic Regression model was that it failed to predict any teams winning the championship.  Although the Random Forest was able to predict winners for the last round, it had it's own prediction problems.<br><br>  

![This is an image](https://github.com/bwengerDU/March_Madness/blob/main/Data_Analysis/Bart/LogReg_vs_RFC_graph_accuracy.png)<br><br>
**Figure 3:** Model accuracy scores for Random Forest (left) and Logistic Regression (right) for each round <br><br>

On average, March Madness starts off with 64 teams.  Every round is single elimination, cutting the number of advancing teams by 1/2.  This repeats 6 rounds until a single winner emerges.  However, our model generated 41 winning teams for the first round, when there should have only been 32.  This trend of predicting a surplus of winning teams persisted through the final round, where round 5 had 5 teams instead of 2, and round 6 had 3 teams predicted to win the final game, instead of 1 (**See Figure 4**).  In the end, our model predicted Arizona, Gonzaga, and Murray St. to win.  In reality, none of those teams made it to the final game, and Kansas was the champion team. <br><br> 

![This is an image](https://github.com/bwengerDU/March_Madness/blob/main/Data_Analysis/Bart/Team_advancers.png)<br><br>
**Figure 4:** Teams predicted to win each round <br><br>

Since we wanted to also see how our model's bracket would do in an office pool, we needed to calculate our bracket's score.  We did this in two ways: a theoretical calculation, and an actual calculation. Our theoretical calculation was obtained by multiplying our model's accuracy scores by the total number of available points per round, and summing the rounds' scores.  Each round has 32 available points.  Our theoretical score for the model was 98pts. 
Our actual score was generated by using the traditional scoring rubrik for each round/matchup. Tradionally to score a bracket, any correctly predicted winning teams for each round are given a point value.  The point scheme for a correct prediction is as follows: 1pt, 2pt, 4pt, 8pt, 16pt, and 32pts, for rounds 1-6, respectively.
However, before we could calculate the actual score of our model, we had to drop teams from our prediction list.  In order to do this without human bias, we developed a mechanism of randomly dropping teams from our prediction list by using a deck of cards.  Doing so resulted in matchups where one team was droped, both teams were dropped, or neither teams were dropped. We then employed flipping a coin for matchups that had either two dropped teams or two advancing teams after the card deck elimination.
Now that we had the correct number of teams, we calculated out our actual bracket score which was 44pts.  In comparing the theoretical to actual scores, we suffered a 55% loss in points.  This loss is due to the combined effects of using a deck of card and a coin flip, and their respective probabilities, for randomly dropping teams.  The other point that is worth noting, is that we might have also randomly drop teams that were accurately predicted to advance.  In fact, by using these methods, Kansas advanced through 3 rounds, even though our model predicted Kansas only advancing 1 round.

Our office pool contained 16 brackets consisting of celebrities, experts, Bryan's Friends, purely coin flip brackets, theoretical score for the Logistic Regression model, and a theoretical and actual score for the Random Forest Model.  When we loaded these scores into our office pool, our Theoretical bracket won 3rd place with 98 points, but our Actual bracket, with 44pts, ranked 3rd from last, only beating the 2 coin flip brackets.  When the points per round are graphed for all the brackets we see that our theoretical bracket is in the lead until the very last round, where Frank and Paige pull ahead with 114pts and 103pts, respectively (**See Figure 5**). Again, comparing the theoretical scores Logistic Regression model and the Random Forest model, we see that they are similar, with the Random Forest losing ground in rounds 3 and 4, but making a come back in round 5 and 6 (**See Figure 6**).

<img src="https://github.com/bwengerDU/March_Madness/blob/main/Data_Analysis/Bart/top3_performers.png" width="600"><br><br>
**Figure 5:** Office pool bracket Scores for each round <br><br>

<img src="https://github.com/bwengerDU/March_Madness/blob/main/Data_Analysis/Bart/LogReg_vs_RFC_graph.png" width="600"><br><br>
**Figure 6:** Theoretical bracket Scores for both Logistic Regression (Pink) and Random Forest (Blue) for each round <br><br>

We performed all of the above analysis with a data set that included columns for each Conference, generated by the OneHotEncoder method.  Due to our model's low performance in the later rounds, we questioned if our model could be improved by removing the Conference columns.  We did so, then re-ran the training portion of our code for each round.  This resulted in a loss of accuracy in every round, and a final prediction of 6 teams winning the championship game.  We then aborted any further analysis with the conference data removed.

Knowing that our model was unable to correctly predict the accurate number of teams per round, in addition to our decreasing accuracy as rounds progressed, we were interested in knowing which features could be used to make a statistically based judgment call on who to drop or advance.  To do this we generated feature importance lists for each round, then sorted the rounds according to their scores, where score and importance are proportional.  Importance values were calculated out 19 decimal places.  Although evaluated, the importance of a team’s conference, on average was less than a percent, many of which were rounded to zero.  In Round 6, however, being part of the Big 10 or ACC scored 0.0331 and 0.0297, respectively, which was more important than Win-Loss Rank, Field Goal % Rank, and Turnover Per Game Rank.  We then filtered out the top 5 features for each round (**See Figure 7**).  The categories were seemingly random with the most frequent features  in the top 5 being: Win-Loss Rank (5 rounds), Turnover Margin Rank (3 rounds), and Field Goal % Defense Rank (3 Rounds).


<img src="https://github.com/bwengerDU/March_Madness/blob/main/Data_Analysis/Bart/Importance_List_2.png" width="500"><br><br>
**Figure 7:** Top 5 most important features, by round <br><br>


## Conclusion
In considering the above results, our group is left with one conclusion: that there is no better name than March Madness for a tournament, where single elimination widdles down 64 teams, in 6 rounds, to crown a single team as the champion.  Why? Because as the name suggests, it is madness!  On the surface, anything can happen, and upsets are common place, with mediocre teams beating number 1 ranked teams by a single point at times.  It's almost as if your only hope to win a bracket against your co-workers is to flip a coin, or choose a team based on which one has the cooler mascot!  Even when you dig deeper, even as a college basketball guru, even when you pit a machine learning algorithm against 11yrs of statistical data for 351 teams displayed across 53 columns, even then, it is still incredibly challenging to accurately pick a perfect bracket.

When our team first decided to take on this data analysis problem, we knew that we would be challenged. This project did not disappoint in creating challenges for us to overcome.  Initially, we intended to carry out one round of model training, where are only target would be the Tournament Wins column, containing integers 0-6.  We initially started off by running linear regression on various single features, to see what the data might reveal, and if we might be able to narrow down our feature list.  We also envisoned geting numerical predictions for wins, calculated out as a decimal, where a match up might come down to rounding the thousandths place to predict a 4 versus a 5, for example.  But this was not the case.  When graphed, our predictions were cleanly segregated on 6 separated horizontal values, y = 0, 1, 2, 3, 4, 5, 6.  We did not initially understand why.  We now know it's because out target data, while apparently numerical, is really 6 different categories that a team could be predictively binned into.  

Although we didn't understand this at the time, we decided to try the Random Forest model, to see if we could use the importance list to trim our feature list.  This initial model run resulted in an incredibly high accuracy rate for predicting 0s, but failed miserably with predicting any other number.  Our data was imbalanced!  We started off running our models with 350+ teams across 11years.  Since there is only one champion per season, there should only be a single 6 for each season. There should be two teams with 4 wins, 4 teams with 3 wins, 8 teams with 2 wins, and 16 teams with one win, per season.  There should also be 32 teams that make the tournament that have a 0.  The rest of the ~286+ non-tournament teams will also have a 0.  

To address this we reprocessed our data and cut out all of the non-tournament teams, then re-ran some of the models.  Because we had not yet realized that we had categorical target data, our model metrics continued to crash, and so did our models.  For example, when running neural networking with tensor flow and keras, we obtained negative loss values in the 10-100k range.  

Eventually, we figured out our error, re-strategized and came up with a new plan.  We decided to convert the Tournament Wins column into 6 different binary win-loss columns.  This meant training our model 6 different times.  This worked well, but we still had imbalanced data.  We decided to drop the predicted 0s from each round, before proceeding to the next round of predictions.  This required linking predictions back to team names, another problem that need to be solved.  In the end, we got our Random Forest model up an running and carried out the analysis on the 2021-22 unknown data, as detailed above.  

It became obvious once our model was trained, that we needed to tamp down our expectations of a perfectly predicted bracket.  Any good model should at minumum have accuracy scores in greater than 75%.  We only had 66.45% in the 1st round and a dismal 33.33%.  We still hold this is better than randomly guessing at the winner, but we also realize that these numbers are not great.  We then began to wonder if these number could even beat a coin flip, so we used a coin flip to fill out 5 different brackets.  When it came to scoring our model's bracket, we realized that there was 2 different, but very scientifically valid approaches, a theoretical score, and an actual score.  As a group, we are please that even with an actual score of 44points, we beat all the coin flip brackets.  We are even more proud of our model for coming in 3rd place out of a 16 bracket office pool, by using the theoretical score. 

We were intrigued by the fact that a team's conference was not a significant predictor for round advancement, but yet, when removed, all of our metrics suffered.  

Considering the limitations of time due to our class schedule, we are happy with our model's performance, but also believe that it could be made better.  A lot of our time was used up by reformatting our input data and developing new strategies to clear hurdles along the way.  The biggest victory for us, is that the Random Forest model, detailed above, serves as a proof of concept for our input format and analysis strategy.  We are confident that it would help to tune the paramenters of the Random Forest model.  We could also go back and re-run some of the other models, focusing on the binary classifiers, such as Logistic Regression, Decision Tree, and Gradient Boosting, all of which can also be tuned.  We could also try using a different classifier for each round, to see if a combination of them might make for aa more powerful prediction.

There are several challenges that our model faces.  The first is that our data is limited in nature.  We can only obtain a set number of rows of data for each season, limited on the amount of teams.  There is always going to be an imbalance of losses versus wins, since the goal of a tournament is to have only 1 winner.  The data already being limited, is truncacted every round, causing a loss of statistical power.  With the current strategy and approach, poor model metrics and poor predictive power will result in incorrect number of advancing teams per round.  This is a problem, because then we have to add in unbiased methods of dropping or advancing teams, and these randomized methods have their own probabilities that when combined with our model, decrease our odds of success even further.  Even by using the list of top 5 most important features to make an educated guess, the predictors seem random, except for Win-Loss Rank.  However, we know from history that even number 1 ranked teams can be defeated in the first round by low ranked teams, so again, our confidence in this predictor dwindles.  To ensure the correct number of teams per round, one option could be to redesign our model to predict a winner from a 2-team a match up.

In conclusion, we are proud of our model, and the challenges that we overcame to develop it and to proove the concept.  We realize that our model can be improved, and we have a good idea of how we could approach that. We argue, given the stagering odds against us, that the ability of our model to come in 3rd place with a theortical score, is a victory.  In the end, however, we are humbled and reminded that March Madness doesn't care about statistics, and to us that is pure insanity!
     

## Delete below?


The odds of predicting a perfect brack are 1 in 9.2 Quintillion. Bearing this in mind we have tamped our expectations down from the expectation of a perfectly predicted bracket. We instead feel the more realistic gauge of our model would be to compare it to several different groups of categorized brackets in order to see how well we would perform against different levels of competition. I simulated 5 different brackets that were predicted by the flipping of a coin, one group was comprised of celebrity brackets, one group was comprised of sports writers and analysts, and the last group was from my office pool. I also created a "Super Group" in which I took the top three performers from each group and compared them to three of our models in order to determine how we would perform against the best of all possible competitor types. 

calculated out theoretical scores and actual scores of our model's bracket





As an initial analysis, we used the RandomForestClassier model to generate an importance_list (see Figure 2), to see which of our **number of features?** were of the most predictive value with the aggregate data.

The tournament teams data was them used to train the model (win column should already be added to the dataframe at this point and will serve as the y_train).

### Logistic Regression
Running a successful logistic regression model required a large amount of tinkering to our initial approach. In order to win the March Madness tournament a team must win 6 times while the runner up will have 5 wins, there will be two teams with 4 wins, 4 teams with 3 wins, 8 teams with 2 wins, and 16 teams with one win. Across the NCAA we will have 324 teams with 0 wins. This significant imbalance between the 1 teams with 6 wins and the 324 teams with 0 wins means that we will have a very high success rate in determining teams that will NOT win, but we will have very little chance of successfully predicting a team that can achieve 6 wins. Wins are the only outcome we aim to predict so we must adjust the data to make it more suitable for analysis. In order to maximize the effectiveness of prediction we instead adopted a round-by-round approach in which we examined whether a team achieved a win depending on which round it is. For instance, the national championship team will no longer be a 6 win team, but instead a team that has 1 win in each of the tournament's six rounds. This change significantly improved our ability to predict the success of teams across all rounds. 

In the first round we are attempting to predict the 32 wins in the round as a 1, compared to the 32 teams that will have a loss as a 0. The second issue we run across is that the number of teams predicted to win will be halfed every round while the number of losses, 0, will increase every round and we will begin to have similar issue as before as 0s will begin to overwhelm the data. To combat this we dropped the 0 values from the previous round so that we were only examining the teams that were capable of advancing to the second round in order to examine how well those first round winners would perform to advance to the next round. Applying these two adjustments to our data allows us to significantly improve our predictions. 






