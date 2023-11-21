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

```
daily_activity <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
daily_sleep <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
hourly_steps <- read_csv(file = "D:/Desktop/Data Analysis/Data Sets and Analysis/Capstone Project/bellabeta/Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv")
```


We will preview our selected data frames and check the summary of each column.
```
head(daily_activity)
str(daily_activity)

head(daily_sleep)
str(daily_sleep)

head(hourly_steps)
str(hourly_steps)
```


**Cleaning and formatting**

Now that we got to know more about our data structures we will process them to look for any errors and inconsistencies.

Before we continue with our cleaning we want to make sure how many unique users are per data frame. Even though 30 is the minimal sample size we will still keep the sleep dataset for practice only.

```
n_unique(daily_activity$Id)
n_unique(daily_sleep$Id)
n_unique(hourly_steps$Id)
```

**Duplicates**

We will now look for any duplicates:

```
sum(duplicated(daily_activity))
sum(duplicated(daily_sleep))
sum(duplicated(hourly_steps))
```


**Remove duplicates and N/A**

Knowing the length of our observations (daily_sleep 413) we are able to delete duplicates for daily_sleep.

```
daily_activity <- daily_activity %>%
  distinct() %>%
  drop_na()

daily_sleep <- daily_sleep %>%
  distinct() %>%
  drop_na()

hourly_steps <- hourly_steps %>%
  distinct() %>%
  drop_na()
```

We will verify that duplicates have been removed

```
sum(duplicated(daily_sleep))
```

**Clean and rename columns**

We want to ensure that column names are using right syntax and same format in all datasets since we will merge them later on. We are changing the format of all columns to lower case.

```
clean_names(daily_activity)
daily_activity<- rename_with(daily_activity, tolower)
clean_names(daily_sleep)
daily_sleep <- rename_with(daily_sleep, tolower)
clean_names(hourly_steps)
hourly_steps <- rename_with(hourly_steps, tolower)
```

**Consistency of date and time columns**

Now that we have verified our column names and change them to lower case, we will focus on cleaning date-time format for daily_activity and daily_sleep since we will merge both data frames. Since we can disregard the time on daily_sleep data frame we are using as_date instead as as_datetime

```
daily_activity <- daily_activity %>%
  rename(date = activitydate) %>%
  mutate(date = as_date(date, format = "%m/%d/%Y"))

daily_sleep <- daily_sleep %>%
  rename(date = sleepday) %>%
  mutate(date = as_date(date,format ="%m/%d/%Y %I:%M:%S %p" , tz=Sys.timezone()))
```

We will check our cleaned datasets

```
head(daily_activity)
head(daily_sleep)
```

For our hourly_steps dataset we will convert date string to date-time.

```
hourly_steps<- hourly_steps %>% 
  rename(date_time = activityhour) %>% 
  mutate(date_time = as.POSIXct(date_time,format ="%m/%d/%Y %I:%M:%S %p" , tz=Sys.timezone()))

head(hourly_steps)
```

**Merging Datasets**

We will merge daily_activity and daily_sleep to see any correlation between variables by using id and date as their primary keys.

```
daily_activity_sleep <- merge(daily_activity, daily_sleep, by=c ("id", "date"))
glimpse(daily_activity_sleep)
```

### Step 04: PROCESS

We will analyze trends of the users of FitBit and determine if that can help us on BellaBeat's marketing strategy.

**5.1 Type of users per activity level**

since we don't have any demographic variables from our sample we want to determine the type of users with the data we have. We can classify the users by activity considering the daily amount of steps. We can categorize users as follows:

Sedentary - Less than 5000 steps a day.
Lightly active - Between 5000 and 7499 steps a day.
Fairly active - Between 7500 and 9999 steps a day.
Very active - More than 10000 steps a day.
Classification has been made per the following article https://www.10000steps.org.au/articles/counting-steps/

First we will calculate the daily steps average by user.

```
daily_average <- daily_activity_sleep %>%
  group_by(id) %>%
  summarise (mean_daily_steps = mean(totalsteps), mean_daily_calories = mean(calories), mean_daily_sleep = mean(totalminutesasleep))

head(daily_average)
```

We will now classify our users by the daily average steps.

```
user_type <- daily_average %>%
  mutate(user_type = case_when(
    mean_daily_steps < 5000 ~ "sedentary",
    mean_daily_steps >= 5000 & mean_daily_steps < 7499 ~ "lightly active", 
    mean_daily_steps >= 7500 & mean_daily_steps < 9999 ~ "fairly active", 
    mean_daily_steps >= 10000 ~ "very active"
  ))

head(user_type)
```


Now that we have a new column with the user type we will create a data frame with the percentage of each user type to better visualize them on a graph.

Below we can see that users are fairly distributed by their activity considering the daily amount of steps. We can determine that based on users activity all kind of users wear smart-devices.

![image](https://github.com/thearqamj/bellabeat-wellness-data-analysis/assets/135017364/278e2b51-dbed-4ec2-9303-ed22ba8ed7b7)

```
user_type_percent %>%
  ggplot(aes(x="",y=total_percent, fill=user_type)) +
  geom_bar(stat = "identity", width = 1)+
  coord_polar("y", start=0)+
  theme_minimal()+
  theme(axis.title.x= element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(), 
        panel.grid = element_blank(), 
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, size=14, face = "bold")) +
  scale_fill_manual(values = c("#85e085","#e6e600", "#ffd480", "#ff8080")) +
  geom_text(aes(label = labels),
            position = position_stack(vjust = 0.5))+
  labs(title="User type distribution")
```

![image](https://github.com/thearqamj/bellabeat-wellness-data-analysis/assets/135017364/8b560144-4d85-403d-b721-1928da4e4a21)


**5.2 Steps and minutes asleep per weekday**

We want to know now what days of the week are the users more active and also what days of the week users sleep more. We will also verify if the users walk the recommended amount of steps and have the recommended amount of sleep.

Below we are calculating the weekdays based on our column date. We are also calculating the average steps walked and minutes sleeped by weekday.

```
weekday_steps_sleep <- daily_activity_sleep %>%
  mutate(weekday = weekdays(date))

weekday_steps_sleep$weekday <-ordered(weekday_steps_sleep$weekday, levels=c("Monday", "Tuesday", "Wednesday", "Thursday",
"Friday", "Saturday", "Sunday"))

 weekday_steps_sleep <-weekday_steps_sleep%>%
  group_by(weekday) %>%
  summarize (daily_steps = mean(totalsteps), daily_sleep = mean(totalminutesasleep))

head(weekday_steps_sleep)
```


```
ggarrange(
    ggplot(weekday_steps_sleep) +
      geom_col(aes(weekday, daily_steps), fill = "#006699") +
      geom_hline(yintercept = 7500) +
      labs(title = "Daily steps per weekday", x= "", y = "") +
      theme(axis.text.x = element_text(angle = 45,vjust = 0.5, hjust = 1)),
    ggplot(weekday_steps_sleep, aes(weekday, daily_sleep)) +
      geom_col(fill = "#85e0e0") +
      geom_hline(yintercept = 480) +
      labs(title = "Minutes asleep per weekday", x= "", y = "") +
      theme(axis.text.x = element_text(angle = 45,vjust = 0.5, hjust = 1))
  )
```


In the graphs above we can determine the following:

Users walk daily the recommended amount of steps of 7500 besides Sunday's.
Users don't sleep the recommended amount of minutes/ hours - 8 hours.


![image](https://github.com/thearqamj/bellabeat-wellness-data-analysis/assets/135017364/d6ad8aa2-ed18-4cca-b4fa-5888d6ddbfa5)


**5.3 Hourly steps throughout the day**

Getting deeper into our analysis we want to know when exactly are users more active in a day.

We will use the hourly_steps data frame and separate date_time column.


```
hourly_steps %>%
  group_by(time) %>%
  summarize(average_steps = mean(steptotal)) %>%
  ggplot() +
  geom_col(mapping = aes(x=time, y = average_steps, fill = average_steps)) + 
  labs(title = "Hourly steps throughout the day", x="", y="") + 
  scale_fill_gradient(low = "green", high = "red")+
  theme(axis.text.x = element_text(angle = 90))
```
![image](https://github.com/thearqamj/bellabeat-wellness-data-analysis/assets/135017364/b07dcea4-e961-4288-9ef8-fc3f1a8c3ead)


We can see that users are more active between 8am and 7pm. Walking more steps during lunch time from 12pm to 2pm and evenings from 5pm and 7pm.

**5.4 Correlations**

We will now determine if there is any correlation between different variables:

```
Daily steps and daily sleep
Daily steps and calories
ggarrange(
ggplot(daily_activity_sleep, aes(x=totalsteps, y=totalminutesasleep))+
  geom_jitter() +
  geom_smooth(color = "red") + 
  labs(title = "Daily steps vs Minutes asleep", x = "Daily steps", y= "Minutes asleep") +
   theme(panel.background = element_blank(),
        plot.title = element_text( size=14)), 
ggplot(daily_activity_sleep, aes(x=totalsteps, y=calories))+
  geom_jitter() +
  geom_smooth(color = "red") + 
  labs(title = "Daily steps vs Calories", x = "Daily steps", y= "Calories") +
   theme(panel.background = element_blank(),
        plot.title = element_text( size=14))
)
`geom_smooth()` using method = 'loess' and formula 'y ~ x'

`geom_smooth()` using method = 'loess' and formula 'y ~ x'
```
![image](https://github.com/thearqamj/bellabeat-wellness-data-analysis/assets/135017364/6079e6b5-aa5e-41f9-b1eb-1f933e4b6584)

**Per our plots:**

There is no correlation between daily activity level based on steps and the amount of minutes users sleep a day.

Otherwise we can see a positive correlation between steps and calories burned. As assumed the more steps walked the more calories may be burned.

**5.5 Use of smart device**

**5.5.1 Days used smart device**

Now that we have seen some trends in activity, sleep and calories burned, we want to see how often do the users in our sample use their device. That way we can plan our marketing strategy and see what features would benefit the use of smart devices.

We will calculate the number of users that use their smart device on a daily basis, classifying our sample into three categories knowing that the date interval is 31 days:

high use - users who use their device between 21 and 31 days.
moderate use - users who use their device between 10 and 20 days.
low use - users who use their device between 1 and 10 days.
First we will create a new data frame grouping by Id, calculating number of days used and creating a new column with the classification explained above.

```
daily_use <- daily_activity_sleep %>%
  group_by(id) %>%
  summarize(days_used=sum(n())) %>%
  mutate(usage = case_when(
    days_used >= 1 & days_used <= 10 ~ "low use",
    days_used >= 11 & days_used <= 20 ~ "moderate use", 
    days_used >= 21 & days_used <= 31 ~ "high use", 
  ))
  
head(daily_use)
```

We will now create a percentage data frame to better visualize the results in the graph. We are also ordering our usage levels.
```
daily_use_percent <- daily_use %>%
  group_by(usage) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(usage) %>%
  summarise(total_percent = total / totals) %>%
  mutate(labels = scales::percent(total_percent))

daily_use_percent$usage <- factor(daily_use_percent$usage, levels = c("high use", "moderate use", "low use"))

head(daily_use_percent)
```
Now that we have our new table we can create our plot:

```
daily_use_percent %>%
  ggplot(aes(x="",y=total_percent, fill=usage)) +
  geom_bar(stat = "identity", width = 1)+
  coord_polar("y", start=0)+
  theme_minimal()+
  theme(axis.title.x= element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(), 
        panel.grid = element_blank(), 
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, size=14, face = "bold")) +
  geom_text(aes(label = labels),
            position = position_stack(vjust = 0.5))+
  scale_fill_manual(values = c("#006633","#00e673","#80ffbf"),
                    labels = c("High use - 21 to 31 days",
                                 "Moderate use - 11 to 20 days",
                                 "Low use - 1 to 10 days"))+
  labs(title="Daily use of smart device")
```

Analyzing our results we can see that

50% of the users of our sample use their device frequently - between 21 to 31 days.
12% use their device 11 to 20 days.
38% of our sample use really rarely their device.
5.5.2 Time used smart device 
Being more precise we want to see how many minutes do users wear their device per day. For that we will merge the created daily_use data frame and daily_activity to be able to filter results by daily use of device as well.

```
daily_use_merged <- merge(daily_activity, daily_use, by=c ("id"))
head(daily_use_merged)
```


We need to create a new data frame calculating the total amount of minutes users wore the device every day and creating three different categories:

All day - device was worn all day.
More than half day - device was worn more than half of the day.
Less than half day - device was worn less than half of the day.

```
minutes_worn <- daily_use_merged %>% 
  mutate(total_minutes_worn = veryactiveminutes+fairlyactiveminutes+lightlyactiveminutes+sedentaryminutes)%>%
  mutate (percent_minutes_worn = (total_minutes_worn/1440)*100) %>%
  mutate (worn = case_when(
    percent_minutes_worn == 100 ~ "All day",
    percent_minutes_worn < 100 & percent_minutes_worn >= 50~ "More than half day", 
    percent_minutes_worn < 50 & percent_minutes_worn > 0 ~ "Less than half day"
  ))

head(minutes_worn)
```
