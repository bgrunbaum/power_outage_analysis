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

Once I completed the implementation of the duration column, I converted it into hours using...

DIST Plot

Comparing 

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

## Hypothesis Testing




