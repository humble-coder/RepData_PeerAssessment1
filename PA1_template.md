Peer Assessment 1
========================================================

##Section 1

First, the movement data is loaded via ```read.csv()```:


```r
data <- read.csv("./activity.csv")
```

Next, each item in the date column of the dataset is converted to a Date object:


```r
data$date <- as.Date(as.character(data$date))
```

##Section 2

To create the histogram of total number of steps per day, a unique dates vector and an empty numeric vector
are created:


```r
dates <- unique(data$date)
stepsVector <- numeric()
```

Then the dates vector is looped through to fill in StepsVector, which is then used to create the histogram:


```r
for(date in dates) {
     daySteps <- data[data$date == date, ]$steps
     daySteps <- daySteps[!is.na(daySteps)]
     daySum <- sum(daySteps)
     stepsVector <- c(stepsVector, daySum)
}

names(stepsVector) <- dates
barplot(stepsVector, ylab="Total Steps Taken Each Day")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

Then the mean and median total number of steps per day are calculated:


```r
mean(data$steps, na.rm = TRUE)
```

```
## [1] 37.38
```

```r
median(data$steps, na.rm = TRUE)
```

```
## [1] 0
```

##Section 3

Next, a time-series plot comparing the data's intervals and its number of steps:


```r
steps <- data$steps
intervals <- data$interval

plot(intervals, steps, type="l")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

From the plot, it appears that interval ~550 seems to contain the maximum number of steps.

##Section 4

2304 step data points are missing from the dataset:


```r
steps <- data$steps
missingSteps <- steps[is.na(steps)]
length(missingSteps)
```

```
## [1] 2304
```

A copy of the dataset is made, and the missing step data points are replaced by the date step averages (or 0 in the case of nan):


```r
newData <- data
meanStepVector <- numeric()

for(date in dates) {
   daySteps <- newData[newData$date == date, ]$steps
   newDaySteps <- daySteps[!is.na(daySteps)]
   meanSteps <- mean(newDaySteps, na.rm = TRUE)
   daySteps[is.na(daySteps)] <- meanSteps
   meanStepVector <- c(meanStepVector, daySteps)
}

newData$steps <- meanStepVector
newData$steps[is.nan(newData$steps)] <- 0
```

A histogram with the modified data is then created:


```r
stepsVector <- numeric()
for(date in dates) {
  daySteps <- newData[newData$date == date, ]$steps
  daySum <- sum(daySteps)
  stepsVector <- c(stepsVector, daySum)
}

names(stepsVector) <- dates
barplot(stepsVector, ylab="Total Steps Taken Each Day")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

The mean of the modified dataset's steps is lower than that of the original dataset. The replacement of missing data by means,or 0s, has lowered the total mean. The additional mean values and zeroes lower the distribution's mean.
The median is unchanged, as its resistant to such changes.


```r
mean(newData$steps)
```

```
## [1] 32.48
```

```r
median(newData$steps)
```

```
## [1] 0
```

##Section 5

A new factor variable that represents weekends and weekdays is created:


```r
allDays <- newData$date
weekDays <- weekdays(allDays)
weekDays <- ifelse(weekDays == "Sunday" | weekDays == "Saturday", "weekend", "weekday")
weekDays <- factor(weekDays)
newData$DayOfWeek <- weekDays
```

And then the number of steps from the weekend and its intervals, along with the number of steps from the weekdays and their intervals, are plotted in a time series:


```r
par(mfrow=c(2, 1))

weekendInterval <- newData[newData$DayOfWeek == "weekend", ]$interval
weekendSteps <- newData[newData$DayOfWeek == "weekend", ]$steps

plot(weekendInterval, weekendSteps, xlab = "Interval", ylab = "Number of steps", type="l", main = "Weekend")

weekDayInterval <- newData[newData$DayOfWeek == "weekday", ]$interval
weekDaySteps <- newData[newData$DayOfWeek == "weekday", ]$steps

plot(weekDayInterval, weekDaySteps, xlab = "Interval", ylab = "Number of steps", type="l", main = "Weekday")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 
