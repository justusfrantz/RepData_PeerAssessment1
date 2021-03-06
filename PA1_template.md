---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Ng Peng Hong


##Loading and preprocessing the data
##### 1. Load the data (i.e. read.csv()) & 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
if (!file.exists("activity.csv")) {
  unzip("activity.zip")
}
activity <- read.csv("activity.csv", colClass=c('integer', 'Date', 'integer'))
```

##What is mean total number of steps taken per day?
##### 1. Calculate the total number of steps taken per day

```r
totsteps <- aggregate(steps ~ date, activity, sum,na.rm=T)
```

##### 2. Make a histogram of the total number of steps taken each day

```r
hist(totsteps$steps,main= "Histogram of Total Number of Steps Taken Each Day",xlab= "Total Steps Each Day",col="Grey")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

##### 3. Calculate and report the mean and median total number of steps taken per day

```r
totstepsmean <- mean(totsteps$steps)
totstepsmedian <- median(totsteps$steps)
```
* Mean of the total number of steps taken per day: 1.0766189 &times; 10<sup>4</sup>
* Median of the total number of steps taken per day:  10765

----
  
##What is the average daily activity pattern?
##### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
intsteps <- aggregate(steps ~ interval, activity, mean,na.rm=T)
plot(intsteps, xlab = "Intervals from 0 to 2355", ylab = "Steps", type = "l", main = "Mean Number of Steps by Interval",col="Blue")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png) 

##### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
maxintsteps <- intsteps$interval[which.max(intsteps$steps)]
```
* On average across all the days in the dataset, the max number of steps is at interval: 835

----
  
##Imputting missing values
##### 1. Calculate and report the total number of missing values in the dataset 

```r
missingval <- sum(is.na(activity))
```

* Number of missing values in the dataset: 2304

##### 2. Devise a strategy for filling in all of the missing values in the dataset.
#####The strategy for filling in all of the missing values in the dataset is to use mean of the interval.

```r
mean_interval <- tapply(activity$steps, activity$interval, mean, na.rm=T, simplify=T)
```


##### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_complete <- activity
nas <- is.na(activity_complete$steps)
activity_complete$steps[nas] <- mean_interval[as.character(activity_complete$interval[nas])]
```

##### 4. a. Make a histogram of the total number of steps taken each day 

```r
totstepscomplete <- aggregate(steps ~ date,activity_complete,sum)

hist(totstepscomplete$steps,main= "Histogram of Total Number of Steps Taken Each Day 
     (missing values replaced with mean of interval)",xlab= "Total Steps Each Day",col="Yellow")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 

##### 4. b. Calculate and report the mean and median total number of steps taken per day.

```r
comptotstepsmean <- mean(totstepscomplete$steps)
comptotstepsmedian <- median(totstepscomplete$steps)
```

* Mean (Imputted): 1.0766189 &times; 10<sup>4</sup>
* Median (Imputted):  1.0766189 &times; 10<sup>4</sup>

##### 4. c. Do these values differ from the estimates from the first part of the assignment?

```r
meandiff <- mean(totstepscomplete$steps)-mean(totsteps$steps)
mediandiff <- median(totstepscomplete$steps)-median(totsteps$steps)
```

* Mean (Differences): 0
* Median (Differences):  1.1886792

##### 4. d. What is the impact of imputing missing data on the estimates of the total daily number of steps?
##### The impact of inputting missing data is minimal as result shown that only the median differ by just over one step.

----
  
##Are there differences in activity patterns between weekdays and weekends?
##### 1.Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day

```r
activity_complete$days <- weekdays(activity_complete$date)
activity_complete$wd_we <-as.factor(c("Weekday","Weekend"))
activity_complete[activity_complete$days == "Sunday" | activity_complete$days == "Saturday" ,5]<- factor("Weekend")
activity_complete[!(activity_complete$days == "Sunday" | activity_complete$days == "Saturday"),5 ]<- factor("Weekday")
```

##### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
intstepscomplete <- aggregate(steps ~ interval + wd_we, activity_complete, mean)
xyplot(steps ~ interval | wd_we, intstepscomplete, layout=c(1,2), type='l')
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png) 
##### From the plot, it is observed that the number of steps are almost uniformly distributed for all intervals during weekend days excluding the nights. Whereas for weekdays, the number of steps are mostly concentrated in the morning hours in which a clear peak can be observed.
