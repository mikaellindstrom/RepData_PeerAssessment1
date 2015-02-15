# Reproducible Research: Peer Assessment 1

##Introduction.
The dataset in this assignment contains data about number of steps per 5 minute intervals during the months of October and November, 2012 as collected by a personal activity monitoring device.


##Prerequisites
* The dataset file is unzipped
* The unzipped file is located in the current working directory where this file is located.
* library timeDate is installed (install.packages('timeDate'))

Note: Changed number format to avoid scientific format for the mean/median values in the text.


```r
options(scipen=999)
```

##Loading and preprocessing the data

1 The data is loaded into memory:

```r
df<- read.csv("activity.csv")
```
2. All rows with NA values in the steps column are removed

```r
df_no_na<-df[!is.na(df$steps),]
```

##What is mean total number of steps taken per day?

To calculate (and plot) the mean number of steps taken per day, the number of steps per day is first calculated by  summing up all steps taken per day using the tapply function.


```r
totalsteps<-tapply(df_no_na$steps,
                   df_no_na$date,
                   sum)
```
This data is then displayed in the form of a histogram where you can see the frequency of total steps taken per day.

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

This plot shows that the mean=***10766.1886792*** and the median=***10765*** for the total number of steps taken per day.
 (Mean = ```{r} mean(totalsteps,na.rm=TRUE) ``` and median=```{r} median(totalsteps,na.rm=TRUE) ```)

##What is the average daily activity pattern?

A time series plot of the 5-minute interval vs the average number of steps taken averaged across all days is accomplished by first creating an array with the mean number of steps per interval using the tapply function:

```r
averagestepsperinterval<-tapply(df_no_na$steps,
                                df_no_na$interval,
                                mean)
```

This array is then plotted below.

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

Interval ***835*** contains the maximum average for an interval with a step count of ***``206.1698113``***. (Interval = ```{r unlist(dimnames(averagestepsperinterval)[1])[which.max(averagestepsperinterval)]} ``` and maximum average = ```{r} averagestepsperinterval[which.max(averagestepsperinterval)]``` )


##Imputing missing values

1. The total number of missing values(NA values) **2304** can be calculated using this R code:

```r
length(which(is.na(df$steps)))
```

2. Some days/intervals have missing values (steps column has NA values) and to fill in these missing values, any NA values are replaced with the mean over all days for that perticular interval.

3. The code to create a new dataset with the NA steps values filled in is:


```r
library(plyr)
f.mean<-function(x) replace(x,
                            is.na(x),
                            mean(x,na.rm=TRUE))
df_mean<-ddply(df,
               ~interval,
               transform,
               steps=f.mean(steps))
```

4. The total number of steps taken each day is shown below in a histogram (first calculating the total steps per day):


```r
totalsteps_mean<-tapply(df_mean$steps,
                        df_mean$date,
                        sum)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

The mean=***10766.1886792*** and the median=***10766.1886792*** for the number of steps taken per day. (Mean = ```{r} mean(totalsteps_mean,na.rm=TRUE) ``` and median=```{r} median(totalsteps_mean,na.rm=TRUE) ```)

This shows that the mean value does not change when replacing the NA values with the mean value for that interval but the median value changes due to that there are many more samples at the mean value than with the NA values.


##Are there differences in activity patterns between weekdays and weekends?

1. First create a factor with levels "weekday" and "weekend" to indicate whether the sample day is on a weekday or on a weekend.


```r
library(timeDate)
df_mean$dayofweek<-weekdays(as.Date(df_mean$date))
df_mean$daytype<-factor(sapply(df_mean$date,
                               function(x) all(isWeekday(x))),
                        levels=c(FALSE, TRUE),
                        labels=c("Weekend", "Weekday"))
```

2. Create a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png) 


