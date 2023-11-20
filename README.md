# Bellabeat Wellness Data Analysis with R
Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market.


### Step 01: ASK
We want to analyze smart device usage data in order to gain insight into how consumers use non-Bellabeat smart devices. We will select one Bellabeat product to apply these insights to in your presentation.

Question of the client:
1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

**Stakeholders**
Urška Sršen - Bellabeat cofounder and Chief Creative Officer
Sando Mur - Bellabeat cofounder and key member of Bellabeat executive team
Bellabeat Marketing Analytics team

#### Deliverables:
1. A clear summary of the business task
2. A description of all data sources used
3. Documentation of any cleaning or manipulation of data
4. A summary of your analysis
5. Supporting visualizations and key findings
6. Your top high-level content recommendations based on your analysis

**Business Task:** The main task is to identify the usage trends of other smart devices and find a way to implement those in our devices.

**Data Source:** This dataset generated by respondents to a distributed survey via Amazon Mechanical Turk between 03.12.2016-05.12.2016. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. Individual reports can be parsed by export session ID (column A) or timestamp (column B). Variation between output represents use of different types of Fitbit trackers and individual tracking behaviors / preferences.


   
### Step 02: PREPARE
The data has been downloaded from Kaggle and stored in a new and separate folder so that it is easily accessible.

**Data Organization and verification:**
Available to us are 18 CSV documents. Each document represents different quantitative data tracked by Fitbit. The data is considered long since each row is one time point per subject, so each subject will have data in multiple rows.Every user has a unique ID and different rows since data is tracked by day and time.

Due to the small size of sample I sorted and filtered tables creating Pivot Tables in Excel. I was able to verify attributes and observations of each table and relations between tables. Counted sample size (users) of each table and verified time length of analysis - 31 days.

| Table Name                  | Type                   | Description                                                                                             |
|-----------------------------|------------------------|---------------------------------------------------------------------------------------------------------|
| dailyActivity_merged        | Microsoft Excel CSV   | Daily Activity over 31 days of 33 users. Tracking daily: Steps, Distance, Intensities, Calories          |
| dailyCalories_merged        | Microsoft Excel CSV   | Daily Calories over 31 days of 33 users                                                                |
| dailyIntensities_merged     | Microsoft Excel CSV   | Daily Intensity over 31 days of 33 users. Measured in Minutes and Distance, dividing groups in 4 categories: Sedentary, Lightly Active, Fairly Active, Very Active |
| dailySteps_merged            | Microsoft Excel CSV   | Daily Steps over 31 days of 33 users                                                                   |
| heartrate_seconds_merged     | Microsoft Excel CSV   | Exact day and time heartrate logs for just 7 users                                                     |
| hourlyCalories_merged        | Microsoft Excel CSV   | Hourly Calories burned over 31 days of 33 users                                                        |
| hourlyIntensities_merged     | Microsoft Excel CSV   | Hourly total and average intensity over 31 days of 33 users                                           |
| hourlySteps_merged           | Microsoft Excel CSV   | Hourly Steps over 31 days of 33 users                                                                  |
| minuteCaloriesNarrow_merged  | Microsoft Excel CSV   | Calories burned every minute over 31 days of 33 users (Every minute in single row)                    |
| minuteCaloriesWide_merged    | Microsoft Excel CSV   | Calories burned every minute over 31 days of 33 users (Every minute in single column)                 |
| minuteIntensitiesNarrow_merged| Microsoft Excel CSV   | Intensity counted by minute over 31 days of 33 users (Every minute in single row)                     |
| minuteIntensitiesWide_merged  | Microsoft Excel CSV   | Intensity counted by minute over 31 days of 33 users (Every minute in single column)                  |
| minuteMETsNarrow_merged      | Microsoft Excel CSV   | Ratio of the energy you are using in a physical activity compared to the energy you would use at rest. Counted in minutes |
| minuteSleep_merged           | Microsoft Excel CSV   | Log Sleep by Minute for 24 users over 31 days. Value column not specified                                |
| minuteStepsNarrow_merged     | Microsoft Excel CSV   | Steps tracked every minute over 31 days of 33 users (Every minute in single row)                       |
| minuteStepsWide_merged       | Microsoft Excel CSV   | Steps tracked every minute over 31 days of 33 users (Every minute in single column)                    |
| sleepDay_merged              | Microsoft Excel CSV   | Daily sleep logs, tracked by: Total count of sleeps a day, Total minutes, Total Time in Bed             |
| weightLogInfo_merged         | Microsoft Excel CSV   | Weight track by day in Kg and Pounds over 30 days. Calculation of BMI. 5 users report weight manually, 3 users not. In total, there are 8 users |


**Data Credibility and Integrity:**
Due to the limitation of size (30 users) and not having any demographic information we could encounter a sampling bias. We are not sure if the sample is representative of the population as a whole. Another problem we would encounter is that the dataset is not current and also the time limitation of the survey (2 months long). That is why we will give our case study an operational approach.


### Step 03: PROCESS
I will focus my analysis in R due to the accessibility, amount of data and to be able to create data visualization to share my results with stakeholders.

We will choose the packages that will help us on our analysis and open them. We will use the following packages for our analysis:

1. tidyverse
2. here
3. skimr
4. janitor
5. lubridate
6. ggpubr
7. ggrepel

**Code**
```
library(ggpubr)
library(tidyverse)
library(here)
library(skimr)
library(janitor)
library(lubridate)
library(ggrepel)
```


**Importing datasets**
Knowing the datasets we have, we will upload the datasets that will help us answer our business task. On our analysis we will focus on the following datasets:

Daily_activity
Daily_sleep
Hourly_steps
Due to the the small sample we won't consider for this analysis Weight (8 Users) and heart rate (7 users)

**Code**

daily_activity <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
daily_sleep <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
hourly_steps <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv")



We will preview our selected data frames and check the summary of each column.
