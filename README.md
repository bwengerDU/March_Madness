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

### Machine Learning Model
As an initial analysis, we used the RandomForestClassier model to generate an importance_list (see Figure 2), to see which of our **number of features?** were of the most predictive value with the aggregate data.

The tournament teams data was them used to train the model (win column should already be added to the dataframe at this point and will serve as the y_train).


## Results

## Conclusion
