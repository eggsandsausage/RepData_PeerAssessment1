# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to

1.Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis



```r
library("ggplot2")
library("data.table")
```

```
## data.table 1.9.2  For help type: help("data.table")
```

```r
library("lubridate")
```

```
## 
## Attaching package: 'lubridate'
## 
## The following objects are masked from 'package:data.table':
## 
##     hour, mday, month, quarter, wday, week, yday, year
```

```r

steps <- fread(path, header = T, sep = ",")
```

```
## Error: object 'path' not found
```

```r
steps$date <- ymd(steps$date)
```

```
## Error: object 'steps' not found
```

```r
steps[, `:=`(datetime, sprintf("%04d", interval))]
```

```
## Error: object 'steps' not found
```

```r
steps[, `:=`(datetime, sub("(\\d{1,2})(\\d{2})$", "\\1:\\2", datetime))]
```

```
## Error: object 'steps' not found
```

```r
steps[, `:=`(datetime, date + hm(datetime))]
```

```
## Error: object 'steps' not found
```

```r
steps[, `:=`(steps, as.numeric(steps))]
```

```
## Error: object 'steps' not found
```


## What is mean total number of steps taken per day?

1.Make a histogram of the total number of steps taken each day



```r
qplot(date, sum.steps, data = steps[, list(sum.steps = sum(steps)), by = date], 
    geom = "bar", stat = "identity")
```

```
## Error: object 'steps' not found
```


2.Calculate and report the mean and median total number of steps taken per day


```r
steps[, j = list(steps = sum(steps)), by = date][, j = list(mean = mean(steps, 
    na.rm = T), median = as.double(median(steps, na.rm = T)))]
```

```
## Error: object 'steps' not found
```


## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
qplot(x = interval, y = average.steps, data = steps[, j = list(average.steps = mean(steps, 
    na.rm = T)), by = interval], geom = "line")
```

```
## Error: object 'steps' not found
```


Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
steps[, j = list(average.steps = mean(steps, na.rm = T)), by = interval][which.max(average.steps), 
    ]
```

```
## Error: object 'steps' not found
```




## Imputing missing values
1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(steps[is.na(steps), ])
```

```
## Error: object 'steps' not found
```


2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc. 

*I've used the mean for the 5-minute interval*


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? 



```r
steps[, `:=`(int.mean.steps, mean(steps, na.rm = T)), by = interval]
```

```
## Error: object 'steps' not found
```

```r
steps[is.na(steps), `:=`(steps, int.mean.steps)]
```

```
## Error: object 'steps' not found
```

```r
qplot(date, sum.steps, data = steps[, list(sum.steps = sum(steps)), by = date], 
    geom = "bar", stat = "identity")
```

```
## Error: object 'steps' not found
```

```r

```


What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps[, j = list(steps = sum(steps)), by = date][, j = list(`new mean` = mean(steps, 
    na.rm = T), `new median` = as.double(median(steps, na.rm = T)))]
```

```
## Error: object 'steps' not found
```

## Are there differences in activity patterns between weekdays and weekends?
Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


