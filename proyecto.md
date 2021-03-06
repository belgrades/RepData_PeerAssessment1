Reproducible Research: Peer Assessment 1
========================================
Fernando Crema   
Central University of Venezuela  
Wireless and Mobile Networks Institute  

## Loading and preprocessing the data

To make the project reproducible, I unzip the original raw data so I don't make any changes


```r
unzip("activity.zip")
```

```
## Error: cannot open file './activity.csv': Permission denied
```

```r
activity <- read.csv("activity.csv")
```

## What is the mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
merge <- aggregate(steps ~ date, data=activity, FUN=sum)
barplot(merge$steps, names.arg=merge$date, xlab="Dates in date format", ylab="Number of steps",main="Number of steps per day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

2. Calculate and report the **mean** and **median** total number of
   steps taken per day
   
This data is strange because the 50th percentile is almost equal to the mean of the data.


```r
mean(merge$steps)
```

```
## [1] 10766
```

```r
median(merge$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute
   interval (x-axis) and the average number of steps taken, averaged
   across all days (y-axis)


```r
merge_interval <- aggregate(steps ~ interval, data=activity, FUN=mean)
plot(merge_interval, type="l")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

2. Which 5-minute interval, on average across all the days in the
   dataset, contains the maximum number of steps?

* This is the index of the interval. The question explicitly says which is. But its hard to say anything about this without comparing with other intervals.


```r
merge_interval$interval[which.max(merge_interval$steps)]
```

```
## [1] 835
```


## Imputing missing values

1. Calculate and report the total number of missing values in the
   dataset (i.e. the total number of rows with `NA`s)


```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the
   dataset. The strategy does not need to be sophisticated. For
   example, you could use the mean/median for that day, or the mean
   for that 5-minute interval, etc.

I will put 0 to all entries this is why: If we don't have the measure the intuitive alternative is that we didn't do the analysis. For example, the first day we don't have any measure. Later on, we compare weekends to weekdays. Filling the data with zeros will have an impact between the differences between the two types of days.

3. Create a new dataset that is equal to the original dataset but with
   the missing data filled in.


```r
nas <- is.na(activity$steps)
activity$steps[nas] <- 0
```

4. Make a histogram of the total number of steps taken each day and
   Calculate and report the **mean** and **median** total number of
   steps taken per day. Do these values differ from the estimates from
   the first part of the assignment? What is the impact of imputing
   missing data on the estimates of the total daily number of steps?


```r
merge <- aggregate(steps ~ date, data=activity, FUN=sum)
barplot(merge$steps, names.arg=merge$date, xlab="Dates in date format", ylab="Number of steps",main="Number of steps without NA's")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(merge$steps)
```

```
## [1] 9354
```

```r
median(merge$steps)
```

```
## [1] 10395
```

Now, we see a difference between the median and the mean. Which is normal in this kind of experiments.


## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels --
   "weekday" and "weekend" indicating whether a given date is a
   weekday or weekend day.

Standard function to add an factor into activity


```r
daytype <- function(date) {
    if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
        "weekend"
    } else {
        "weekday"
    }
}
activity$daytype <- as.factor(sapply(activity$date, daytype))
```

2. Make a panel plot containing a time series plot (i.e. `type = "l"`)
   of the 5-minute interval (x-axis) and the average number of steps
   taken, averaged across all weekday days or weekend days
   (y-axis).


```r
par(mfrow=c(2,1))

#for WeekDays
mytype <- aggregate(steps ~ interval,data=activity,subset=activity$daytype=="weekday",FUN=mean)
plot(mytype, type="l", main=c("Series with weekday"))

#For WeekEnds
mytype <- aggregate(steps ~ interval,data=activity,subset=activity$daytype=="weekend",FUN=mean)
plot(mytype, type="l", main=c("Series with weekend"))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 

We can see that in weekends there's more activity than in weekdays. This is normal, because is in work hours so
the person we are analizying probably works and uses weekends to do more steps.
