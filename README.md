# Power Outage Analysis
### By: Bianca Grunbaum

Exploratory Data Analysis of U.S. Power Outages - DSC 80 @ UCSD

## Introduction

The data that I chose to explore in this analysis is a dataset recording power outages that have occurred across the United States from January 2000 - July 2016. This data includes a row for each power outage that occurs in the U.S. during this time frame, accumulating to a total of 1534 rows (consequently, power outages).

The outage data was originally provided as a XLSX file, so I downloaded it as a CSV file and then dropped the first five rows (which had descriptive information, not actual data). I also dropped the first column, which was a blank column titled "Units" but held no data.

I decided to retain the following columns in my DataFrame, renamed for easier referral.

| Renamed Column | Original Column Name | Description |
|----------|----------|----------|
| `year` | `YEAR` | Indicates the year when the outage event occurred |
| `month` | `MONTH` | Indicates the month when the outage event occurred |
| `state` | `U.S._STATE` | The U.S. state where the outage occurred |
| `start_date` | `OUTAGE.START.DATE` | The day of the year when the outage event started |
| `start_time` | `OUTAGE.START.TIME` | The time of day when the outage event started |
| `restoration_date` | `OUTAGE.RESTORATION.DATE` | The day when power was restored to all customers |
| `restoration_time` | `OUTAGE.RESTORATION.TIME` | The time of day when power was restored to all customers |


As I looked at the data, I came up with a few questions about it. I was particularly curious about the duration of the power outages, and how those might change with different factors. In looking at the different columns, I noticed that the start date/time and restoration date/time varied pretty frequently. I then began to wonder if there is a difference in the duration of power outages that begin at night (between 8pm and 6am) and those that begin during the day, as restoration crews or services might not be able to get to the site as quickly.

This was the question that I have decided to address and answer through this analysis: Do power outages that begin at night last for longer than those that begin during the day?

My goal in addressing this question is to see then what reforms and progress can be made to help fix this discrepancy---whether it be increasing the number of overnight crews available or having better understanding of which outages are likely to occur at night. The following is my statistical analysis of this question, in addition to creating a model to predict duration.

## Data Exploration and Cleaning

I first renamed the columns to the names above, and assigned the new DataFrame to the name `select_data`. 

The first data cleaning that I did, upon determining what my question was, was to drop any rows for which any of the month column, start times or dates or restoration times or dates were NA. If either of those categories were unavailable, I would not be able to complete my analysis. Therefore, I removed those rows, bringing my total DataFrame length from 1534 to 1476.

After doing that, I then made the function `classify_time`, which would take in the start date and start time and return whether or not the start date/time occured in the daytime (after 6am to 8pm) or nighttime (after 8pm to before 6am). This returns "day" if the former criteria is true, and "night" if the latter one is. I then assigned the column `daynight` to `select_data`, which has "day" or "night" corresponding to the start date and time. 

My next step was to calculate the duration of the outage, which I did by combining the start date and time strings into a pd.Datetime object, and comparing that time with that of the restoration date and time. I made the function `find_duration` to do so, which took in those four dates and times, and returned a pd.Timedelta object for each outage's duration.

Once I completed the implementation of the duration column, I converted it into hours using the total_seconds() function and then dividing it by 3600, which is the total number of seconds in an hour. You can see the plots below.

<iframe
  src="assets/total_dur_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I then was curious to see if the distribution of duration varied by cause. It seems to be the case that by and large, it does not---besides potentially the more intentional outages, whether it be because of attack or public appeal, perhaps because they can prepare for them or they know how to fix it better than just trying to fix an accident. 

<iframe
  src="assets/box_cause_duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I then wanted to look at the difference in mean durations, which I did by using groupby. This was the resulting table:

| daynight | duration                  |
|:----------|:-------------------------|
| day      | 1 days 20:19:47.900552486 |
| night    | 1 days 18:09:08.769230769 |


As we can see, there doesn't appear to be that much of a difference, only around 2 hours longer for night power outages. However, the data is extremely skewed, as we saw above. There are extremely large values that could be skewing our data; my next idea was to try to groupby and look at medians and see if we see a difference. The results are below:

| daynight | duration                  |
|:----------|:-------------------------|
| day      | 0 days 07:47:00 |
| night    | 	0 days 19:59:30 |

The median shows a far greater difference, of about 12 hours. Based off of these statistics, my idea was to try to see if this difference is something that we see across the data, or if this is a unique case to our dataset here.


### Assignment of Missingness

I suspected that the column "OUTAGE.DURATION" in the outage dataset might be NMAR, as it contains 58 NA values. The reason that I suspect that the duration column might be NMAR is because "OUTAGE.RESTORATION.TIME" also has 58 NA values---leading me to believe that potentially, the missing values are due to the fact that the outages have not been fixed yet, therefore the missing value depends on itself and would be NMAR.

When looking at other columns that the duration and time might depend on, I thought about U.S._STATE (state in the select_data dataframe) as a potential option for dependency because it is possible that a certain state just suffered a big storm and didn't have the chance yet to restore it, or perhaps some specific states have worse restoration teams.

In terms of a column that I doubted had a relation with duration, I chose month, since I didn't think that the specific month should have any bearing on whether the duration was missing or not, since any month could have an outage that didn't get resolved. I did permutation tests on both of these columns, and my ideas were correct---for the state column, the p-value is .015, meaning that it actually is statistically significant at the 0.05 level (as .015 < 0.05), and the missigness of duration does actually depend on state, as some states may have more data collecting issues or are worse at fixing the power outages (or maybe one state suffered a very large power outage, causing many that haven't been fixed yet). However, for the month column, the permutation test proves that the duration column is not dependent on the month column; meaning that there is no correlation between the missing values in duration and that in month (i.e., one month isn't missing a ton of values or has a lot of unsolved outages). We can conclude this due to the p-value of .114, which is greater than our threshold of 0.05. See plots below for distributions.

<iframe
  src="assets/month_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/state_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing

### Null Hypothesis: 
The duration for outages that begin during the day and the duration for outages that begin during night are approximately the same.

### Alternative Hypothesis:
The duration for outages that begin during the day are shorter than the duration for outages that begin at night.

I believe these hypotheses to be good answers to the question that I am asking about the duration of the outage, since they directly respond to whether or not the length of the duration has anything to do with what time of day it starts during.

### Test Statistic:

For my test statistic, I have decided to use the difference in medians between the night and the day durations. Given the fact that the mean values will be extremely skewed by the large outliers, I have decided to instead use the median as I believe it should be much more representative of a "middle ground" of our data, as seen in the exploration above.

I have also decided to pursue a permutation test where I shuffled the `daynight` column, and then computed the difference in the median from those shuffled labels and their corresponding duration distributions. I ran this bootstrapping test 10000 times.

### P-Value and Statistical Significance:

I have chosen the p-value threshold of .05, which is for a 95% confidence interval/test significance. 

### Conclusion

After running the bootstrapping test and comparing it to the observed statistic (seeing which values were greater than or equal to the observed statistic), I got a p-value of .0001. This is far below the significance threshold of .05, so yes, we can reject our null hypothesis. This allows us to believe that there is a statistically significant positive difference in the difference in medians between the durations for the night and the day power outages; meaning that the median duration for night is found to be generally higher than the median duration for daytime power outages.

## Prediction Problem

Since the results of the permutation test were so strong, and they showed that there is a clear difference between the median durations of outages that began during the night versus those which began during the daytime, I thought that trying to predict the duration given certain factors of a power outage could be really interesting.

## Baseline Model

My baseline model to predict the `duration` of a power outage depends on two categorical columns: `daynight` and `cause`. `daynight` is as described above, and `cause` is the root cause of the power outage. I chose these columns because I thought that they were the categorical columns that could best explain the duration of the power outage---for example, we know that generally, nighttime durations tend to be longer, and potentially a storm could make it more difficult to restore power than just a power malfunction. I am choosing to use the RMSE and the Median Absolute Error as my statistics, focusing more heavily on the latter as it is less susceptible to outliers.

`daynight` and `cause` columns are both nominal variables, but in my model, I use a OneHotEncoder transformer to make the nominal variables into different binary variables. I then use a RandomForestRegressor in my pipeline (which I chose due to its ability to predict well on non-linear data), and fit it on the training data using an 80/20 split. After performing this baseline model, my MAE is 20.80 hours and my RMSE is 31.89 hours. I don't think this is an absolutely terrible model, given that there is a lot of variability in the duration (again, values range from below 10 to over 1750). I do think though, that the limited amount of features that I am using is inhibiting my MAE and RMSE from becoming lower and from fitting the data better. It also only has categorical values that are OneHotEncoded, so it can only predict constant values for every different combination of the two features.

One issue that I ran into and I fixed in this process was the ability of mitigating for large outliers. When I kept all durations in my analysis (including outliers such as over 1750 hours), my MAE was 40.68 hours and my RMSE was 115.51 hours. This means that it nearly doubled my MAE and almost quadrupled my RMSE. To remedy this, I decided to cut out the top 5% of the duration values in the dataset to get a model that can more accurately predict the large majority of durations, instead of trying to fit to some extremely high outliers.

After creating this baseline model, my median absolute error is 9.56 hours.

### Final Model

In the final model that I made, I did a few things differently from my baseline. The main change that I made is the addition of three interaction variables, all interacting with one another---["anomaly_level", "pop_den_rur", "year"].  `anomaly_level` represents the El Nino/La Nina index, essentially becoming a measure of climate activity. `pop_den_rur` is the population density of the rural areas of the state, and `year` is the year in which the outage occured.

I chose these variables because they individually had some of the highest coefficients on different models that I tried, which made me consider making them into an interaction model. I also think that these features, particularly `pop_den_rur` and `anomaly_level`, have direct effects on duration: the more rural a state, the more difficult it may be to get help out there, and the more active the climate, the more frequent the storms or more difficult the conditions. When these variables interact with the `year`, they help account for changes in these variables over time.

I found the ideal hyperparameters by running GridSearchCV (with cv = 5 and the train-test split being 80/20), and it ultimately found the hyperparameters of 10 maximum levels of depth and 1 leaf needed to be found to stop the tree. I experimented with this by trying different combinations and ultimately seeing what lead to the overall best parameters.

From this point, I was able to calculate my test statistics. Importantly, all of them decreased across my new model compared to the baseline. These are the statistics for my final model (Mean Absolute Value, RMSE, Median Absolute Value): 17.7702639762892,30.279432773053017, 8.340653700529222. The statistics for my baseline model were in the same foramt, but higher across the board: 20.796890843590763, 31.894029114867173, 9.561896488887488. 

I think this speaks to the effectiveness of the new model, but also to the restrictions of the data. While the model does its best to fit to the data, it is ultimately a difficult task to predict duration, as it can be impacted by a lot of different factors that are not available in our dataset (number of personnel available, etc).

### Fairness Analysis

I tested whether or not my model creating approximately equal predictions for both warm seasons (Spring and Summer) and cold seasons (Fall and Winter). My evaluation statistic was the difference in RMSE between the two groups (warm - cold), as it can generally better fit to non-linear patterns.

#### Null Hypothesis:
My model is fair; its RMSE for warm seasons (summer and spring) and for cold seasons (fall and winter) are roughly the same, with any differences being due to random chance.

#### Alternative Hypothesis:
My model is unfair; its RMSE for warm seasons (summer and spring) is lower than its RMSE for cold seasons (fall and winter).

I chose a p-value significance level of .05, but it was around .526, indicating that we cannot reject the null hypothesis at the .05 significance level, meaning that our model should be relatively fair, at least when it comes to seasons and how it evaluates cold versus warm seasons. The p-value of .526 means that assuming the null hypothesis is true, around .526 of the data is above or equal to the observed difference in RMSE.
