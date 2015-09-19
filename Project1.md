# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

1. Load the data (i.e. read.csv())

2. Preprocess/transform the data (if necessary) into a format suitable for your analysis


```r
library(data.table)
activity <- fread("activity.csv")
```

## What is mean total number of steps taken per day?

1. Calculate the total number of steps taken per day

```r
stepsPerDay <- activity[,sum(steps, na.rm=TRUE), by=interval]
names(stepsPerDay) <- c("interval","steps")
colnames(stepsPerDay)
```

```
## [1] "interval" "steps"
```

2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(stepsPerDay$steps, main = "Steps Taken Each Day", xlab = "Steps", border="blue", col="green", breaks=5)
```

![](Project1_files/figure-html/unnamed-chunk-3-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day



```r
summary(stepsPerDay$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     0.0   131.8  1808.0  1981.0  2800.0 10930.0
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)



```r
plot(stepsPerDay$interval, stepsPerDay$steps, type="l", col="blue", xlab="Interval", ylab="Steps", main="Average Number of Steps")
```

![](Project1_files/figure-html/unnamed-chunk-5-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
activity[which(steps == max(activity$steps, na.rm=TRUE)), interval]
```

```
## [1] 615
```
## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(!complete.cases(activity))
```

```
## [1] 2304
```


2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

- The strategy I'll be using here is to impute the missing data with mean of the columns.


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activity$steps[is.na(activity$steps)] = mean(activity$steps, na.rm=TRUE)
activity$interval[is.na(activity$interval)] = mean(activity$interval, na.rm=TRUE)
sum(!complete.cases(activity))
```

```
## [1] 0
```


4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
stepsPerDay <- activity[,sum(steps, na.rm=TRUE), by=interval]
names(stepsPerDay) <- c("interval","steps")
hist(stepsPerDay$steps, main = "Steps Taken Each Day", xlab = "Steps", border="blue", col="green", breaks=5)
```

![](Project1_files/figure-html/unnamed-chunk-9-1.png) 




```r
summary(stepsPerDay$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   299.1   430.8  2107.0  2280.0  3099.0 11230.0
```


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity$Day <- weekdays(as.Date(activity$date))
for( i in 1:nrow(activity)){
  if (activity[i,]$Day %in% c("Saturday", "Sunday")){
    activity[i,]$Day<-"weekend"
  }
  else{
    activity[i,]$Day<-"weekday"
  }
}
head(activity)
```

```
##      steps       date interval     Day
## 1: 37.3826 2012-10-01        0 weekday
## 2: 37.3826 2012-10-01        5 weekday
## 3: 37.3826 2012-10-01       10 weekday
## 4: 37.3826 2012-10-01       15 weekday
## 5: 37.3826 2012-10-01       20 weekday
## 6: 37.3826 2012-10-01       25 weekday
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
stepsPerDay <- activity[,mean(steps, na.rm=TRUE), by=list(interval, Day)]
colnames(stepsPerDay)
```

```
## [1] "interval" "Day"      "V1"
```

```r
names(stepsPerDay) <- c("interval", "Day", "steps")
with(stepsPerDay, plot(interval, steps, type="n", xlab="Interval", ylab="Steps", main="Average Number of Steps"))
with(stepsPerDay[stepsPerDay$Day == "weekday",], lines(interval, steps, col="purple"))
with(stepsPerDay[stepsPerDay$Day == "weekend",], lines(interval, steps, col="gold"))
legend("topright", lty=c(1,1), col = c("purple","gold"), legend = c("weekday", "weekend"))
```

![](Project1_files/figure-html/unnamed-chunk-12-1.png) 









