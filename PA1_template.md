# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data

* Load the data


```r
fileURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(fileURL, destfile = "./activity.zip" )
unzip("activity.zip")
```

* Process/transform the data (if necessary) into a format suitable for your analysis


```r
activity <- read.csv("activity.csv", header = TRUE)
```

## What is mean total number of steps taken per day?

* Calculate the total number of steps taken per day


```r
sum_steps <- aggregate (steps ~ date, data = activity, FUN = sum, na.rm=TRUE)
```

* Make a histogram of the total number of steps taken each day


```r
hist(sum_steps$steps, xlab = "Steps", main = "Histogram of steps", breaks = 50)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

* Calculate and report the mean and median of the total number of steps taken per day


```r
mean(sum_steps$steps)
```

```
## [1] 10766.19
```

```r
median(sum_steps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

* Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
mean_steps_interval <- aggregate (steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(steps ~ interval, data = mean_steps_interval, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
i <- which.max(mean_steps_interval$steps)
mean_steps_interval[i,]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nas <- sum(is.na(activity$steps))
```

The total amount of NAs values is 2304.

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activity2 <- merge(activity, mean_steps_interval, by = "interval", all = TRUE)
activity2$steps.x <- ifelse(is.na(activity2$steps.x), activity2$steps.y, activity2$steps.x)
```

Make a histogram of the total number of steps taken each day.


```r
sum_steps2 <- aggregate (steps.x ~ date, data = activity2, sum)
hist(sum_steps2$steps.x, xlab = "Steps", main = "Histogram of steps", breaks = 50)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

Calculate and report the mean and median total number of steps taken per day.


```r
mean(sum_steps2$steps.x)
```

```
## [1] 10766.19
```

```r
median(sum_steps2$steps.x)
```

```
## [1] 10766.19
```

Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

From the histogram we can see that the frequencies of values are higher we have now more available values. The mean is still the same, and the median is a little bit higher and matches the mean.

## Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
weekend <- c('Saturday', 'Sunday')
activity2$weekday_type <- factor((weekdays(as.Date(activity2$date)) %in% weekend), levels=c(FALSE, TRUE), labels=c('weekday', 'weekend'))
```

Make a panel plot containing a time series plot (i.e. type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
mean_steps_weekday <- aggregate (steps.x ~ interval + weekday_type, data = activity2, mean)

library(lattice)
xyplot(steps.x ~ interval | factor(weekday_type), data = mean_steps_weekday, main="Mean for Weekdays by Interval", xlab = "Interval", ylab = "Number of steps", layout = c(1,2), panel = panel.lines)
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
