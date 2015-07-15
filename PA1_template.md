---
title: "PA1_template.Rmd"
output: html_document
---
### Loading and preprocessing the data
Show any code that is needed to  
  
1. Load the data (i.e. read.csv())  
2. Process/transform the data (if necessary) into a format suitable for your analysis  

```r
unzip("activity.zip")
raw<-read.csv("activity.csv")  
```
### What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.  
  
1. Calculate the total number of steps taken per day  
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day  
3. Calculate and report the mean and median of the total number of steps taken per day  

```r
dailySteps<-tapply(raw$steps,raw$date,sum,na.rm=TRUE)  
hist(dailySteps,xlab="Steps",main="Number of Steps per Day",breaks=20)  
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
paste("Mean number of steps per day:",mean(dailySteps))
```

```
## [1] "Mean number of steps per day: 9354.22950819672"
```

```r
paste("Median number of steps per day:",median(dailySteps))
```

```
## [1] "Median number of steps per day: 10395"
```
### What is the average daily activity pattern?  

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)  
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?  

```r
intervals<-unique(raw$interval)
intervalSteps<-tapply(raw$steps,raw$interval,mean,na.rm=TRUE)
plot(intervals,intervalSteps,type="l",
     xlab="interval",ylab="Mean Number of Steps",
     main="Mean Number of Steps by Time of Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
paste("The highest average number of steps takes place at:",intervals[which.max(intervalSteps)])
```

```
## [1] "The highest average number of steps takes place at: 835"
```
### Imputing missing values  

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.  

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)  
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.  
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.  
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?  

```r
paste("The number of NA's present in the raw data set:",sum(is.na(raw$steps)))
```

```
## [1] "The number of NA's present in the raw data set: 2304"
```

```r
modified<-raw
modified$steps[is.na(modified$steps)]<-0
# lookup<-cbind(intervals,intervalWithoutNA)
# idx<-which(is.na(raw$steps))
daily<-tapply(modified$steps,modified$date,sum)
hist(daily,xlab="Steps",main="Number of Steps per Day",breaks=20)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
paste("Mean number of steps per day:",mean(daily))
```

```
## [1] "Mean number of steps per day: 9354.22950819672"
```

```r
paste("Median number of steps per day:",median(daily))
```

```
## [1] "Median number of steps per day: 10395"
```
### Are there differences in activity patterns between weekdays and weekends?  

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.  

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.  
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.  

```r
modified$day<-weekdays(as.Date(as.character(modified$date,"%Y %m %d")))
modified$weekday<-"weekday"
modified$weekday[modified$day=="Saturday"|modified$day=="Sunday"]<-"weekend"
modified$weekday<-as.factor(modified$weekday)

weekdaySteps<-aggregate(modified$steps~modified$interval+modified$weekday, data=modified, FUN=mean)
names(weekdaySteps)<-c("interval","weekday","steps")
library(ggplot2)
g<-ggplot(weekdaySteps, aes(interval,steps))
g+geom_line()+facet_grid(weekday~.)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
