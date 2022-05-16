### Week 1:

searched for possible data sets, brainstormed with the team on project topics, once we decided on march maddness, I spent some time trying to find usable datasets that had multiple years worth of data across various stat variables (rebounds, free-throw percent, etc).  We found some data sets, including a json file, but most were either not what we were looking for, or required api subscriptions to access.  I investigated the NCAA webpage and found team statistics for seasons going back to 2009-10, for both the regular season and the March Madness tournament.
From there we decided to download statistics from 18 different categories.  Each stat file was for the regular season for all teams, and included team names and overall rank.  We also downloaded the win-loss stats for the tournament teams only.  All files were downloaded in excel format.  We copied and pasted the ranks for each category into their respective columns in excel.  I then copy and pasted the tournament team names and win-loss lists into the master files along side their respective season master stats.  I used the Index function paird with the Match function in excel to reference each team name in the master stats, then look up the matching name in the tournament teams list, and pulled their respective wins into a new column, titled "Tournament Wins".  This left null values which were delt with later via jupyter notebook.  The team name is formated as follows: Team (Conference), and some were formatted with a state as follows: Team (State) (Conference).  I worked on writing two different regular expressoin scripts (Regex) to extract the conference name out of the team name column, and then to delete the conference name (including the parenthesis) from the team column.  We then copied and pasted each season into a master file.  Since we were each responsible for different seasons, we each ended up with a single personal master file.  I was responsible for seasons 2020-21 and 2021-22.

Bryan created a repository page for our project on his Git Hub profile.  I wrote an initial readme file with Purpose, Method, Results, and Conclusion sections.  This will be updated as we go.


### Week 2:

I worked on doing some basic ETL for my personal master file, using jupyter notebook.  This included using the regex code from week one, and filling in null values.  I uploaded this to the group via Slack (NCAA_ETL.ipnynb).  Everyone sent me their personal master files which I then merged into a single All Teams master file (2011-22_clean_NCAA_stats.csv).  There was a problem with Baiyu's dataset and the Tournover Margin Rank, and the values were mismatched with the teams, so I had to go back and re-download some data sets to fix his column, using the Excel index/match functions mentioned above in Week 1.  The new file had the same name and was uploaded via slack to the group.
The columns of our final master files were as follows:
* Team 	
* Assists Per. Game Rank 	
* Assist Turnover Ratio Rank 	
* BlocksPerGame Rank 	
* Field Goal % Rank 	
* Field Goal % Defense Rank 	
* Fouls Per Game Rank 	
* Free Throw % Rank 	
* Rebound Margin Rank 	
* Scoring Defense Rank 	
* Scoring Margin Rank 
* Scoring Offense Rank 	
* Steals Per Game Rank 	
* Three Pt FG Defense Rank 	
* Three Pt FG % Rank 	
* Three Pt FG Per Game Rank 	
* Turnover Margin Rank 	
* Turnover Per Game Rank 	
* Win-Loss Rank 	
* Tournament Wins 	
* Conference

I then used the clean master dataset to run the RandomForestClassifier Model and used the OneHotEncoder to convert the Conference column into binary clasifier columns.  The RandomForest model showed some promise, with almost perfect predictions for the 0wins, but all the other win values had terrible success.  We concluded this was becasue the imbalance in data, with 0s being a super majority and each value 1-6 becoming a smaller minority to where there's eventually only 66 6s.
Trying to resolve the imbalance in win-predictions, we decided to delete non-tournament teams.  I worked on this, which proved to be a lot more involved than I had expected.  In order to achieve this, I did the following: 
* deleted out the conference columns, 
* added back in a new team column with original unclean values, 
* downloaded each season's tournament win-loss team stats,
* matched teams names, team ranks, and win values to new unclean team names (this is why I had to add that 2nd team column with unclean data back in)
* uploaded the new csv stats file into jupyter notebook, cleaned the team columns, created conference columns, then dropped all rows with null values in the tournament win column
* filled in very few remaining null values in various other columns with 0, and manually had to go back and add some conference values to a few teams

The resulting file (mm_teams.csv) was uploaded to the group via slack.  I used this new tournament-teams-only file to start running machine learning models and generated the following files:

* NCAA_ClusterCentroids_LogRegr.ipynb
* NCAA_Decision_Tree.ipynb
* NCAA_Gradient_Boosting_Classifier.ipynb
* NCAA_LinearRegr.ipynb
* NCAA_Neural_Network.ipynb
* NCAA_RandomOverSampling_LogRegr.ipynb
* NCAA_RandomUndersampling_LogRegr.ipynb
* NCAA_SMOTEENN_LogRegr.ipynb
* NCAA_SMOTE_LogRegr.ipynb
* NCAA_machine_learning.ipynb
* NCAA_randomoversampler_randomforest.ipynb

Met with the group on Saturday via Zoom during office hours.  Talked about problems with having non-binary classifier target data, since we essectially hve 7 different classifiers (wins 0-6).  Discussed options to problem solve, and possibility of changing course to a new data set topic.  We presented our ideas and concerns to Simon (our TA) and he gave us some really good feed back and encouragement.  I had really high accuracy in predicting 0wins with the RandomForestClassifier (RFC), so he suggested we us RFC to predict 0 wins as 0, and predict 1-6 wins 1, turning our multi-classifier into binary.  Because of the high accuracy of predicting 0s, we could then feel confident dropping any teams with a predicted 0 wins, then re-run out model with the truncated dataset.  We would then predict 1 win as 0, and 2-6wins as 1.  We would then drop all the teams with 1 wins, then repeat until we finally predicted 5 wins as 0 and 6 wins as 1 with the most shortened data.
I have not had a chance to run this model but am very hopeful that this strategy will work.  Bryan has tried some preliminary testing with logistic regression and went from an overall accuracy score of 38.46% up to 86.67%, which seems great.  

Running the RFC on the data with all teams (data was not scaled), I had the following scores:
* Overall accuracy score: 0.9009
* Precision for prediction 0: 0.90 
* Precision for prediction 0: 1.00

Running the RFC on the data with only tournament teams (data was scaled), I had the following scores:
* Overall accuracy score: 0.4615
* Precision for prediction 0: 0.52 
* Precision for prediction 0: 0.86

Running the RFC on the data with only tournament teams (data was scaled, random oversampling was applied), I had the following scores:
* Overall accuracy score: 0.4260
* Precision for prediction 0: 0.51 
* Precision for prediction 0: 0.82

It seems like random oversampling hurt our metrics.  Not sure why. Tomorrow (monday 5/16/22) my goal is to run the RFC with scaled data, **without** random oversampling applied, and progress through the 6 iterations of predict-delete.


I also worked on creating this progress journal for future reference.