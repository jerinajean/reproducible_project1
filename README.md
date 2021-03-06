# reproducible_project1

---
title: "Project 1"
author: "Jerina Ecleo"
date: "January 19, 2019"
output: html_document
---
For this project, I load first the packages that will be used.

```{r}
library(knitr)
library(ggplot2)
library(dplyr)
library(plyr)
```

Loading and preprocessing the data.
In loading the data for the project, I use the read.csv() function to read in the designated csv file. I used the str() function to determine the class types of the data.

```{r}
activity <- read.csv("activity.csv",header=T)
totalstepsperday <- aggregate(steps ~ date, data = activity, FUN = sum, na.rm = TRUE)
totalstepsperday
```

Process the data (if necessary) into a format suitable for your analysis

1. Calculate the total number of steps taken per day?

(For this part of the assignment, you can ignore the missing values in the dataset.)

```{r}
totalstepsperday <- aggregate(steps ~ date, data = activity, FUN = sum, na.rm = TRUE)
totalstepsperday
```

2. Make a histogram of the total number of steps taken each day.convert dates first

```{r}
## converting dates to Y-M-D format
activity$date <- as.Date(activity$date, "%Y-%m-%d")
## calculate steps as it relates to date using SUM (per day)
hist(totalstepsperday$steps, 
    main="Total Steps per Day", 
    xlab="Number of Steps per Day", 
    ylab = "Interval",
    col="blue",
    breaks=50)
```

3. Calculate and report the mean and median total number of steps taken per day.

```{r}
## mean of total steps per day
msteps <- mean(totalstepsperday$steps)
msteps
```


```{r}
## median of total steps per day
medsteps <- median(totalstepsperday$steps)
medsteps
```

```{r}
## check work using summary
summary(totalstepsperday)
```

4. What is the average daily activity pattern?
Make a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```{r}
## five minute average using steps to interval - FUN = mean instead of sum
fivemin <- aggregate(steps ~ interval, data = activity, FUN = mean, na.rm = TRUE)
## line chart
plot(x = fivemin$interval, 
    y = fivemin$steps, 
    type = "l", 
    col = "blue",
    xlab = "5-minute Intervals",
    ylab = "Average Steps Taken ~ Days",
    main = "Average Daily Activity Pattern")
```

5. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
```{r}
maxsteps <- fivemin$interval[which.max(fivemin$steps)]
maxsteps
```

Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

6. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
```{r}
## sum of all the NA in data = activity
impute <- sum(is.na(activity$steps))
impute
```

7. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Replace NA values with the mean results for five minute intervals
8. Create a new dataset that is equal to the original dataset but with the missing data filled in.
```{r}
activity2 <- activity
nas <- is.na(activity2$steps)
avg_interval <- tapply(activity2$steps, activity2$interval, mean, na.rm=TRUE, simplify = TRUE)
activity2$steps[nas] <- avg_interval[as.character(activity2$interval[nas])]
names(activity2)
```

```{r}
## Check for no-NA
sum(is.na(activity2))
```

9. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
```{r}
## Similar analysis without NAs now
totalstepsperday2 <- aggregate(steps ~ date, data = activity2, FUN = sum, na.rm = TRUE)
totalstepsperday2
```

```{r}
## Histogram without the NA values
hist(totalstepsperday2$steps, 
    main = "Total Steps per Day (no-NA)", 
    xlab = "Number of Steps per Day", 
    ylab = "Interval",
    col="green",
    breaks=50)
```

```{r}
## What is the impact of imputing data?
summary(totalstepsperday)
```

```{r}
summary(totalstepsperday2)
```

Mean and median values are almost identical, but the quantiles are significantly different.

10. Are there differences in activity patterns between weekdays and weekends? For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
```{r}
## Data has three fields, and we will add a new one in the next step - 11
head(activity2)
```

11. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
```{r}
## Add the new weekend/weekday field
activity2<- activity2%>%
        mutate(typeofday= ifelse(weekdays(activity2$date)=="Saturday" | weekdays(activity2$date)=="Sunday", "Weekend", "Weekday"))
head(activity2)
```

12. Make a panel plot containing a time series plot (i.e. type = “l”) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).
```{r}
## Time to plot - Line chart
fivemin2<- aggregate(steps ~ interval, data = activity2, FUN = mean, na.rm = TRUE)
head(fivemin2)
```

```{r}
ggplot(activity2, aes(x =interval , y=steps, color=typeofday)) +
       geom_line() +
       labs(title = "Ave Daily Steps (type of day)", x = "Interval", y = "Total Number of Steps") +
       facet_wrap(~ typeofday, ncol = 1, nrow=2)
```

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

