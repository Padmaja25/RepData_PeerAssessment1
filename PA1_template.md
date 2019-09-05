
## Reproducible Research

```r
##Set the current working directory which contains the dataset
#setwd("~/repdata_data_activity")
```

#### Load the required Libraries

```r
library(ggplot2)
library(dplyr)
library(tidyr)
library(lattice)
```

### Code for reading in the dataset and/or processing the data

Load the data using read.csv function

```r
dataSet<- read.csv("activity.csv")
```
Process/transform the data (if necessary) into a format suitable for your analysis

```r
date<-as.Date(dataSet$date)
## Converting the date column to required format (Date-Month-Year)
dataSet$date<-format(date,"%d-%A-%Y")
```
Omitting the NA values and saving the data to "new" variable

```r
new<-na.omit(dataSet) ## new data set 
```

### Histogram of the total number of steps taken each day

Calculate the total number of steps taken per day

```r
daywise<-tapply(new$steps,new$date,sum)
```
Histogram displays steps taken per day

```r
hist(daywise,col = "steelblue")
```

![plot of chunk unnamed-chunk-25](figure/unnamed-chunk-25-1.png)

### Mean and median number of steps taken each day

```r
summary(daywise)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      41    8841   10765   10766   13294   21194
```

### Time series plot of the average number of steps taken

```r
Timeseries<-aggregate(steps~interval,data=new,sum)
ggplot(Timeseries,aes(x=interval,y=steps))+geom_line()+ggtitle("Time series plot of the average number of steps taken")
```

![plot of chunk unnamed-chunk-27](figure/unnamed-chunk-27-1.png)

### The 5-minute interval that, on average, contains the maximum number of steps

```r
new$interval[which.max(new$steps)]
```

```
## [1] 615
```

### Code to describe and show a strategy for imputing missing data

```r
## Saving the original data to new variable
dataSet_new <- dataSet
```
Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs

```r
count<-is.na(dataSet_new$steps) %>% sum()
```
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
dataSet_new[is.na(dataSet_new)]<- mean(dataSet$steps,na.rm = TRUE)
```
Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
print(paste("No. of rows in original data set :",nrow(dataSet)))
```

```
## [1] "No. of rows in original data set : 17568"
```

```r
print(paste("No. of rows in new data set :",nrow(dataSet_new)))
```

```
## [1] "No. of rows in new data set : 17568"
```

```r
print(head(dataSet_new,10))
```

```
##      steps           date interval
## 1  37.3826 01-Monday-2012        0
## 2  37.3826 01-Monday-2012        5
## 3  37.3826 01-Monday-2012       10
## 4  37.3826 01-Monday-2012       15
## 5  37.3826 01-Monday-2012       20
## 6  37.3826 01-Monday-2012       25
## 7  37.3826 01-Monday-2012       30
## 8  37.3826 01-Monday-2012       35
## 9  37.3826 01-Monday-2012       40
## 10 37.3826 01-Monday-2012       45
```
### Histogram of the total number of steps taken each day after missing values are imputed
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
daywise<-tapply(dataSet_new$steps,dataSet_new$date,sum)
hist(daywise,col = "steelblue")
```

![plot of chunk unnamed-chunk-33](figure/unnamed-chunk-33-1.png)

### Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
dataSet_new$day<-format(as.Date(date,"%d-%A-%Y"),format="%A")
dataSet_new$Week<- ifelse(dataSet_new$day %in% c("Sunday","Saturday") ,"Weekends","Weekdays")
```
Make a panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis)

```r
stepsByDay<-aggregate(steps~Week+interval,data = dataSet_new,mean)
xyplot(steps~interval|Week,data= stepsByDay,layout=c(1,2),type="l")
```

![plot of chunk unnamed-chunk-35](figure/unnamed-chunk-35-1.png)

