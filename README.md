# Power Outage Analysis
### By: Bianca Grunbaum

Exploratory Data Analysis of U.S. Power Outages - DSC 80 @ UCSD

### Introduction

The data that I chose to explore in this analysis is a dataset recording power outages that have occurred across the United States from January 2000 - July 2016. This data includes a row for each power outage that occurs in the U.S. during this time frame, accumulating to a total of 1534 rows (consequently, power outages).

The outage data was originally provided as a XLSX file, so I downloaded it as a CSV file and then dropped the first five rows (which had descriptive information, not actual data). I also dropped the first column, which was a blank column titled "Units" but held no data.

I decided to retain the following columns in my DataFrame, renamed for easier referral.

| Renamed Column | Original Column Name | Description |
|----------|----------|----------|
| "year"  | "YEAR"  | 	Indicates the year when the outage event occurred  |
| Row 2C1  | Row 2C2  | Row 2C3  |


As I looked at the data, I came up with a few questions about it. I was particularly curious about the duration of the power outages, and how those might change with different factors. In looking at the different columns, I noticed that the start date/time and restoration date/time varied pretty frequently. I then began to wonder if there is a difference in the duration of power outages that begin at night (between 8pm and 6am) and those that begin during the day, as restoration crews or services might not be able to get to the site as quickly.

This was the question that I have decided to address and answer through this analysis: Do power outages that begin at night last for longer than those that begin during the day?

My goal in addressing this question is to see then what reforms and progress can be made to help fix this discrepancy---whether it be increasing the number of overnight crews available or having better understanding of which outages are likely to occur at night. The following is my statistical analysis of this question, in addition to creating a model to predict duration

From this point, through exploring some of the data and the column descriptions.


