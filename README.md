NBA Skills Improvement Project
==============================

In this project, the goal was to determine what was the easiest to improve upon in a player's first few years in the league: three point shooting, finishing at the basket, or defense. In this project, I showed examples of data scraping with Beautiful Soup, data cleaning, logistic regressions with Scikit-Learn, K Means Clustering, and visualization with Tableau.

Summary
-------

[Here is a visualization](https://public.tableau.com/app/profile/kevin.le8759/viz/NBAPlayerImprovementProject/BasicImprovedRatios) I put together in Tableau as a way to explore the results. The first dashboard shows a very basic breakdown of how many players improved vs. how many players didn't improve amongst those eligible for the data sets. This is broken out by a 3 point shooting data set, a paint finishing data set, and a defensive BPM data set. The second dashboard shows a plot of players that improved or didn't improve correlated with a specific feature that my model deemed to be significant. You will notice this dashboard only has plots for the paint finishing data set and the defensive BPM data set because my model did not determine any features in the three point shooting data set to be significant.

This project is broken down into four parts, in which you can find the links to below for full code and project.

-   [1\. Data Scraping](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/blob/main/1.%20NBA%20Skills%20Improvement%20Project%20Part%201%20-%20Data%20Scraping.ipynb)

-   [2\. Data Cleaning](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/blob/main/2.%20NBA%20Skills%20Improvement%20Project%20Part%202%20-%20Data%20Cleaning.ipynb)

-   [3\. Data Manipulation](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/blob/main/3.%20NBA%20Skills%20Improvement%20Project%20Part%203%20-%20Data%20Manipulation.ipynb)

-   [4\. Modeling](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/blob/main/4.%20NBA%20Skills%20Improvement%20Project%20Part%204%20-%20Modeling.ipynb)

**Main takeaways:**

-   Looking at just the topline, three point shooting is the skill that has the highest ratio of players improving.

-   None of the features for the three point shooting model were statistically significant, but the closest was FG%, which had a positive coefficient. This suggests a more efficient player in their first two seasons is more likely to improve their three point shooting percentage. Perhaps this indicates a higher skill level that just needs more reps from a further line.

-   In the model for paint finishing, there are three features that are statistically significant. Games and minutes played (positive coefficients) both have very low p-values, indicating that this is a skill that improves from getting more playing time. Additionally, the paint scoring FG% of the first two years is statistically significant, which understandably indicates if a player shows potential that they will likely get better. This is unlike three point shooting, which can be more of an acquired skill.

-   Playing more and increasing paint efficiency is often what leads to creating an All-Star. The rugged defender type is also a common theme in the clusters across both three point shooting and paint scoring. It might be an archetype teams go after as having a better chance to improve.

-   There are four statistically significant features in the defensive model, with three of them being win shares stats (total win shares, defensive win shares, and offensive win shares). The fact it is all phases of the game suggests better or smarter players in general improve at defense. This is also shown by the statistically significant feature, block %. There has been a theory for evaluating pre-draft players that block percentage and steal percentage would be good indicators of success in the NBA because those stats are often obtained with basketball IQ, knowing the right positions and predicting what other players on the court will do.

-   In all models, the athlete/defender type was one of the clusters, so the typical strategy of drafting for upside does have some credibility. However, early efficiency and overall basketball IQ may be better indicators of improvement if you can catch on that a player has that within their first two seasons to try to trade for them.

Data Scraping
-------------

Using Beautiful Soup, I scraped the [Shooting](https://www.basketball-reference.com/leagues/NBA_2023_shooting.html) and [Advanced](https://www.basketball-reference.com/leagues/NBA_2023_advanced.html) stats pages from Basketball Reference for the 2014-2023 seasons. I also scraped player data from the [NBA API](https://github.com/swar/nba_api) so that I can merge a player's stats to their measurables like height and weight.

Data Cleaning
-------------

In this section, I did some basic data cleaning. I consolidated player stats down to just one row if they played for multiple teams in a season, removed special characters from names so any merges and lookups would work correctly, and removed any columns with null values that were not worth replacing. For numeric columns, if there were a small amount of null values I would replace the value with a 0, because it likely means a situation like, for example, a player not attempting a shot from that range.

Data Manipulation
-----------------

Here in this section, this is an extension of data cleaning where I create columns displaying the improvement results.

For each of the three main stats that I am looking to analyze, I found the quartiles league-wide per year. An example of this is for the three point shooting data set. For the year 2022, the 25th percentile shot 25.7% from three, the 50th percentile should 33.3% from three, and the 75th percentile shot 37.2% from three. With these quartiles, I am able to assign a quartile to every player for every season. For example, if a player shot 30% from three in 2022, they would fall in the second quartile. Using this, I would be able to determine if a player improved. This is more effective than just calculating if a player's three point shooting percentage increased through the years because league wide trends show an increase in shooting accuracy over the last decade.

![three point shooting quartiles](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/c10261a1-dea7-48a3-94d6-db4f77cbc37e)

I also filtered my training set down to only players that played at least 4 seasons. The reason for this is because for my model, I am determining someone to be improved if they can improve the quartile they are in from their first two seasons to their next two seasons in whatever stat is being judged (3PT %, 0-3 FG%, DBPM). Because of this, I also needed to make a column for the max quartile reached in the first two seasons for a player and another column for max quartile reached in seasons three and four. If a player had at least once reached the fourth quartile (highest) in their first two seasons, they were also removed from the model because they would not have had an opportunity to improve theoretically under this model's logic.

Modeling
--------

For my model, I aggregated a player's first two seasons into one row to use as the training set. The training set would have the average of each stat a player accumulated in their first two seasons, then the Improved column as a "Yes" or "No" as the target column.

Results
-------

Here are the results from the three models.

**Three Point Shooting:**

-   56% of players from the training set improved from their first two seasons. (jumping quartiles)

-   The model that was created had a 65% accuracy score, which means the model is better than if you just predicted everyone would improve (56%).

-   Of the features, none had significant p-values.

-   I performed a cluster analysis on players that improved and this is what I found

-   Cluster 0: guys who didn't play much (Abdel Nader, Chandler Hutchison)

-   Cluster 1: lottery pick type of players (Andrew Wiggins, Ben McLemore, Trey Burke)

-   Cluster 2: undersized guards and forwards known to have a capable jumper (Coby White, D'Angelo Russell, Gary Harris)

-   Cluster 3: players who don't shoot (Isaiah Hartenstein, Romeo Langford)

-   Cluster 4: tough guy archetype, players known for defense (Aaron Gordon, Grant Williams, De'Anthony Melton)

**Shooting Test set predictions:**

![shooting test predictions](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/a53a6e6d-4299-4efc-a4b6-3739baa41003)

**Shooting Model Feature Significance:**

![shooting features](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/0006e58b-7cab-475b-bdee-889928777ac4)

**Paint Finishing:**

-   49% of players from the training set improved from their first two seasons

-   The model that was created had a 65% accuracy score, which means the model is better than if you just predicted everyone would improve (49%).

-   Of the features, the ones with significant p-values were games played, minutes played, and 0-3 FG %.

-   Cluster analysis on players that improved

-   Cluster 0: guards with lower shot tendency (Jevon Carter, Malik Beasley, Joe Harris)

-   Cluster 1: high usage players (Darius Garland, De'Aaron Fox, Giannis Antetokounmpo)

-   Cluster 2: the rugged defender archetype (Aaron Gordon, Grant Williams, Kris Dunn)

**Finishing Test set predictions:**

![finishing test predictions](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/866a9e3d-3d0b-43e2-b9cb-3d314ca01618)

**Finishing Model Feature Significance:**

![finishing features](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/054f81d4-b880-49a6-8816-1234380b55fe)

**Defensive BPM:**

-   48% of players from the training set improved from their first two seasons

-   The model that was created had a 67% accuracy score, which means the model is better than if you just predicted everyone would improve (48%).

-   Of the features, the ones with significant p-values were defensive win shares (DWS), win shares, block %, and offensive win shares.

-   Cluster analysis on players that improved

-   Cluster 0: players that don't play much (Adreian Payne, Chasson Randle)

-   Cluster 1: high usage players (D'Angelo Russell, Ja Morant, Tyler Herro)

-   Cluster 2: plus athletes (Ben McLemore, Collin Sexton, Victor Oladipo)

-   Cluster 3: also players who don't play much.

**Defensive Test set predictions:**

![defensive test predictions](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/008636e2-8001-472e-bd94-556665e9d4c3)

**Defensive Model Feature Significance:**

![defensive features](https://github.com/kevinkietle/NBA-Skills-Improvement-Project/assets/82183590/d886c97b-ce20-419f-867c-6f98626d8199)

There is more analysis on these results in the Main Takeaways section above.

Hope you enjoyed reading through this project!

**Feel free to contact me with any questions or inquiries.**

**LinkedIn - <https://www.linkedin.com/in/kevinkietle/>**

**Email - <kevinkietle@gmail.com>**

**My portfolio of data analysis projects  can be found at: <https://kevinkietle.github.io/Bootstrap-Website-Portfolio/>**
