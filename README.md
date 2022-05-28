# March_Madness

## Purpose
Every Year, millions of people fill out a March Madness Bracket and try to predict the winners of each matchup, hoping that they can correctly pick the most winning teams and potentially win some money from friends, co-workers, or from online betting.  In fact, Nielsen said in a 2019 study ([study link](https://www.nielsen.com/wp-content/uploads/sites/3/2019/04/case-study-brackets-and-baskest-2016.pdf)), that "According to the American Gaming Association (AGA), 40 million people filled out approximately 70 million brackets last year, and waged $9.2 billion on March Madness through office pools, Nevada sports books, offshore sites and bookmakers.  
The desire to correctly predict a winning bracket, or even a perfect bracket, is so alluring that Google hosts an annual contest for machine learning groups to try and write computer algorithms to predict the winners of March Maddness.  While the idea of using computer algorithms to increase the odds of a perfect bracket seems like an obvious solution, the reality is the odds of predicting a perfect bracket are infinitesimally small.  According to numbers from the NCAA ([found here](https://www.ncaa.com/news/basketball-men/bracketiq/2022-03-10/perfect-ncaa-bracket-absurd-odds-march-madness-dream)), the odds are as follows:

* if you just guess or flip a coin - 1 in 9,223,372,036,854,775,808 - 1 in 9.2 quintillion (a quintillion = a billion billion)
* if you know a little something about basketball - 1 in 120.2 billion


The focus of this project was to use supervised machine learning models to accurately predict the teams that would advance/win in each of the 6 rounds of the tournament, eventually picking the correct team to win the tournament.  As a secondary goal, we were interested in seeing if our machine learning model could win an office pool, or at minimum even beat a coin flip.  

The following people collaborated on this project as part of the University of Denver 2021-2022 Bootcamp for Data Analytics.  Click on their names to visit their GitHub pages to learn more about them and their work:
* [Bryan Wenger](https://github.com/bwengerDU)
* [Philip Kirwin](https://github.com/pheel24)
* [Baiyu Hua](https://github.com/harryhua2021)
* [Bart Black](https://github.com/bartblack13)


## Method

### Data ETL
In order to obtain a dataset containing sufficient NCAA Basketball statistics to train a machine learning model, we downloaded (in excel format) 19 regular season team statitic tables (ex- personal fouls per game, blocked shots per game, etc) and 1 championship team statistics table (wins, losses, and win percentage for the tournament) across the 11 most recent seasons of Men's Division 1 Basketball from the NCAA official website (Link [here](https://stats.ncaa.org/rankings/change_sport_year_div)).  Because this project focuses on the ability of our model to predict the teams that advance through the tournament, we omitted data from the 2019-2020 year, since there was no March Maddness games due to the COVID-19 Pandemic. Our total data sets consisted of 19 sets across 11 years (209 tables).  Each of the regular season tables consisted of all teams (335-351), and each of the championship tables consited only of the teams that made it to the tournament (68).  Each table included team names and overall rank for the category.  We used Excel to transfer the rank list of each category to an aggregate file, which was then saved as a CSV file.

We then carried out the cleaning and transforming process on our data file.  For this we used Python via Jupyter Notebook with multiple libraries.  Among the many input variables, we were also interested if the team's conference was an important feature of predicting total wins during the championship.  The Team column was initially formatted as follows: Team (Conference), (ex - Villanova (Big East)).  We extracted the conference name from the first column values using regex, cleaned up the values to only display the Team Name, and created a "Conference" column with the extracted values (**See Figure 1?**).  We also converted the "Tournament Wins" column, containing values 0-6, into binary columns for each round, for win and loss (1 and 0, respectively). Since the "Conference" column values were categorical, we set each value to a number using the OneHotEncoder Method.   

Cleaning and transforming the data resulted in 53 columns, including the team name.

### Model Choice and Training
Having 6 different binary win-loss columns required that we have 6 different targets, or 6 different rounds of training and predictions, etc.  We ran various different models, focusing on the classification models (Logistic Regression, Decision Tree, Random Forest, and Gradient Booster).  Based on prelimiary model metrics we decided to use the RandomForestClassifier model to make our predictions.  To do this, we performed the following for each of 6 rounds:

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

During the training phase of our project, we were hopeful that the RandomForest Model was a great choice.  When we compared the model metrics to that of other models, including the Logistic regression model, the RandomForestClassifier outperformed (**See Figure - insert table**).  As can be seen from table/graph/image, our model started off with a 66.45% accuracy rate and eventually dropped to a 33.33% accuracy.  We argue that even with significant drop and a low accuracy score of 33.33% our model still out performs a person selecting the winner at random, which would have a 1.56% (1/64) chance of a correct pick.
When comparing our model to the Logistic Regression model, we had slightly better perfomance for 4 rounds.  The biggest downfall of the Logistic Regression model was that it failed to predict any teams winning the championship.  Although the Random Forest was able to predict winners for the last round, it had it's own prediction problems.  
On average, March Madness starts off with 64 teams.  Every round is single elimination, cutting the number of advancing teams by 1/2.  This repeats 6 rounds until a single winner emerges.  However, our model generated 41 winning teams for the first round, when there should have only been 32.  This trend of predicting a surplus of winning teams persisted through the final round, where round 5 had 5 teams instead of 2, and round 6 had 3 teams predicted to win the final game, instead of 1.  In the end, our model predicted Arizona, Gonzaga, and Murray St. to win.  In reality, none of those teams made it to the final game, and Kansas was the champion team. 

Since we wanted to also see how our model's bracket would do in an office pool, we needed to calculate our bracket's score.  We did this in two ways: a theoretical calculation, and an actual calculation. To score a bracket, any correctly predicted winning teams for each round are given a point value.  The point scheme for a correct prediction is as follows: 1pt, 2pt, 4pt, 8pt, 16pt, and 32pts, for rounds 1-6, respectively.  
Before we could calculate scores we had to drop teams from our prediction list.  In order to do this without human bias, we developed a mechanism of randomly dropping teams from our prediction list by using a deck of cards to randomly drop teams from the list.  Doing so resulted in matchups where one team was droped, both teams were dropped, or neither teams were dropped. We then employed flipping a coin for matchups with either two dropped teams or two advancing teams after the card deck elimination.  
Now that we had the correct number of teams, we calculated out our bracket scores.  Our theoretical calculation was obtained by multiplying our model's accuracy score by the total number of available points per round, and summing the rounds' scores.  Each round has 32 available points.  Our theoretical score for the model was ~98pts.  Our actual score was generated by using the above scoring rubrik for each round/matchup.  Our actual score was 44pts.  This represents a 55% loss in points.  This loss is due to the combined effects of using a deck of card and a coin flip, and their respective probabilities, for randomly dropping teams.  The other point that is worth noting, is that we might randomly drop teams that were accurately predicted to advance.  In fact, by using these methods, Kansas was able to make it through 3 rounds, but our model predicted Kansas being eliminated in the 2nd round.

When we load these scores into our office pool, our Theoretical bracket won 3rd place with 98 points.  When the points per round are graphed for all the brackets we see that our model is in the lead until the very last round, where Frank and Paige pull ahead with 114pts and 103pts, respectively.  Our Actual bracket with 44pts ranks near the bottom at 3rd from last, only beating the 2 coin flip brackets (**see image**)

As an initial analysis, we used the RandomForestClassier model to generate an importance_list (see Figure 2), to see which of our **number of features?** were of the most predictive value with the aggregate data.

The tournament teams data was them used to train the model (win column should already be added to the dataframe at this point and will serve as the y_train).

### Logistic Regression
Running a successful logistic regression model required a large amount of tinkering to our initial approach. In order to win the March Madness tournament a team must win 6 times while the runner up will have 5 wins, there will be two teams with 4 wins, 4 teams with 3 wins, 8 teams with 2 wins, and 16 teams with one win. Across the NCAA we will have 324 teams with 0 wins. This significant imbalance between the 1 teams with 6 wins and the 324 teams with 0 wins means that we will have a very high success rate in determining teams that will NOT win, but we will have very little chance of successfully predicting a team that can achieve 6 wins. Wins are the only outcome we aim to predict so we must adjust the data to make it more suitable for analysis. In order to maximize the effectiveness of prediction we instead adopted a round-by-round approach in which we examined whether a team achieved a win depending on which round it is. For instance, the national championship team will no longer be a 6 win team, but instead a team that has 1 win in each of the tournament's six rounds. This change significantly improved our ability to predict the success of teams across all rounds. 

In the first round we are attempting to predict the 32 wins in the round as a 1, compared to the 32 teams that will have a loss as a 0. The second issue we run across is that the number of teams predicted to win will be halfed every round while the number of losses, 0, will increase every round and we will begin to have similar issue as before as 0s will begin to overwhelm the data. To combat this we dropped the 0 values from the previous round so that we were only examining the teams that were capable of advancing to the second round in order to examine how well those first round winners would perform to advance to the next round. Applying these two adjustments to our data allows us to significantly improve our predictions. 




## Results

The odds of predicting a perfect brack are 1 in 9.2 Quintillion. Bearing this in mind we have tamped our expectations down from the expectation of a perfectly predicted bracket. We instead feel the more realistic gauge of our model would be to compare it to several different groups of categorized brackets in order to see how well we would perform against different levels of competition. I simulated 5 different brackets that were predicted by the flipping of a coin, one group was comprised of celebrity brackets, one group was comprised of sports writers and analysts, and the last group was from my office pool. I also created a "Super Group" in which I took the top three performers from each group and compared them to three of our models in order to determine how we would perform against the best of all possible competitor types. 

calculated out theoretical scores and actual scores of our model's bracket

## Conclusion
