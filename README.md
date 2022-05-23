# March_Madness

## Purpose
Every Year, millions of people fill out a March Madness Bracket and try to predict the winners of each matchup, hoping that they can correctly pick the most winning teams and potentially win some money from friends, co-workers, or from online betting.  In fact, Nielsen said in a 2019 study ([study link](https://www.nielsen.com/wp-content/uploads/sites/3/2019/04/case-study-brackets-and-baskest-2016.pdf)), that "According to the American Gaming Association (AGA), 40 million people filled out approximately 70 million brackets last year, and waged $9.2 billion on March Madness through office pools, Nevada sports books, offshore sites and bookmakers.  
The desire to correctly predict a winning bracket, or even a perfect bracket, is so alluring that Google hosts an annual contest for machine learning groups to try and write computer algorithms to predict the winners of March Maddness.

The focus of this project is to use supervised machine learning models to accurately predict the number of wins a team will have during the tournament, where the predictions should be between **0-6(?)**.  Because the tournament is single elimination, the most the winning team can have is 6 wins, and a team losing in the first round will have 0 wins.  To do this, we performed the following:
* Collected and aggregated team statistics from past seasons
* Used the aggregate data from multiple seasons from all teams, to determine which team stats are the most important
* Trained a machine learning model
* Used championship team list as the training? data **combine with previous step as training not test?**
* Fed test data in and predicted the number of wins for the teams in a given seasons tournament
* Compared predicted wins versus actual wins, and calculate accuracy rates **etc?**

The following people collaborated on this project as part of the University of Denver 2021-2022 Bootcamp for Data Analytics.  Click on their names to visit their GitHub pages to learn more about them and their work:
* [Bryan Wenger](https://github.com/bwengerDU)
* [Philip Kirwin](https://github.com/pheel24)
* [Baiyu Hua](https://github.com/harryhua2021)
* [Bart Black](https://github.com/bartblack13)


## Method

### Data ETL
In order to obtain a dataset containing sufficient NCAA Basketball statistics to train a machine learning model, we downloaded (in excel format) 32 regular season team statitic tables (ex- personal fouls per game, blocked shots per game, etc) and 1 championship team statistics table (wins, losses, and win percentage for the tournament) across the 12 most recent seasons of Men's Division 1 Basketball from the NCAA official website (Link [here](https://stats.ncaa.org/rankings/change_sport_year_div)).  Because this project focuses on the ability of our model to predict the number of wins during the March Maddness Tournament, we opted not to download data from the 2019-2020 year, since there was no March Maddness games due to the COVID-19 Pandemic. Our total data sets consisted of 33 sets across 11 years (363 tables).  Each of the regular season tables consisted of all teams (335-351), and each of the championship tables consited only of the teams that made it to the tournament (68).

We then needed to merge all of the stat tables into a large aggregate file, so that we could begin the clean and transforming process, prior to running any analysis or model training.  For this we used Python via Jupyter Notebook with multiple libraries.  Among the many input variables, we were also interested if the team's conference was an important feature of predicting total wins during the championship.  The Team column was initially formatted as follows: Team (Conference), (ex - Villanova (Big East)).  We extracted the conference name from the first column values using regex, cleaned up the values to only display the Team Name, and created a "Conference" column with the extracted values.  Since the Conference column values are categorical, we set each value to a number using the **method?**.  Many of the data sets had repetative data.  For instance, there was no difference between "free throws attemps" and "free throws made" tables.  We dropped all repetitive data.  We also searched for null values, even though we did not expect to find any.  The rest of our columns had numerical values and did not require any other clean up.  **do we need to scale scale data?**

Cleaning and transforming the data resulted in **?number of columns?**  This Dataframe served as our aggregate data.

We also used the list of team names from the tournament, to shorten the aggregate data so that only the statistics of the teams in each tournament were represented and used for testing data.  This was a necessary step since we are only worried about predicting wins during the tournament (Easiest way is drop rows with null values; non tournament teams will have null values for "Tournament Wins" column).

### Logistic Regression

Running a successful logistic regression model required a large amount of tinkering to our initial approach. In order to win the March Madness tournament a team must win 6 times while the runner up will have 5 wins, there will be two teams with 4 wins, 4 teams with 3 wins, 8 teams with 2 wins, and 16 teams with one win. Across the NCAA we will have 324 teams with 0 wins. This significant imbalance between the 1 teams with 6 wins and the 324 teams with 0 wins means that we will have a very high success rate in determining teams that will NOT win, but we will have very little chance of successfully predicting a team that can achieve 6 wins. Wins are the only outcome we aim to predict so we must adjust the data to make it more suitable for analysis. In order to maximize the effectiveness of prediction we instead adopted a round-by-round approach in which we examined whether a team achieved a win depending on which round it is. For instance, the national championship team will no longer be a 6 win team, but instead a team that has 1 win in each of the tournament's six rounds. This change significantly improved our ability to predict the success of teams across all rounds. 

In the first round we are attempting to predict the 32 wins in the round as a 1, compared to the 32 teams that will have a loss as a 0. The second issue we run across is that the number of teams predicted to win will be halfed every round while the number of losses, 0, will increase every round and we will begin to have similar issue as before as 0s will begin to overwhelm the data. To combat this we dropped the 0 values from the previous round so that we were only examining the teams that were capable of advancing to the second round in order to examine how well those first round winners would perform to advance to the next round. Applying these two adjustments to our data allows us to significantly improve our predictions. 

### Machine Learning Model
As an initial analysis, we used the RandomForestClassier model to generate an importance_list (see Figure 2), to see which of our **number of features?** were of the most predictive value with the aggregate data.

The tournament teams data was them used to train the model (win column should already be added to the dataframe at this point and will serve as the y_train).


## Results

The odds of predicting a perfect brack are 1 in 9.2 Quintillion. Bearing this in mind we have tamped our expectations down from the expectation of a perfectly predicted bracket. We instead feel the more realistic gauge of our model would be to compare it to several different groups of categorized brackets in order to see how well we would perform against different levels of competition. I simulated 5 different brackets that were predicted by the flipping of a coin, one group was comprised of celebrity brackets, one group was comprised of sports writers and analysts, and the last group was from my office pool. I also created a "Super Group" in which I took the top three performers from each group and compared them to three of our models in order to determine how we would perform against the best of all possible competitor types. 


## Conclusion
