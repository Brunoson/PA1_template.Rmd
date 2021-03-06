PA1_template.Rmd
================
PA1_template
Saravanan Subramaniam

Sunday, September 14, 2014

Loading and preprocessing the Data:
Dataset <- read.csv("C:/Users/Dr.Saravanan/Desktop/R/activity.csv")
Dataset$datetime <- as.POSIXct(
  with(
    Dataset,
    paste(
      date,
      paste(interval %/% 100, interval %% 100, sep=":"))
  ),
  format="%Y-%m-%d %H:%M",tz="")
require(ggplot2)
## Loading required package: ggplot2
require(scales)
## Loading required package: scales
require(grid)
## Loading required package: grid
What is mean total number of steps taken per day?
Number_of_steps_perday <- setNames(
  aggregate(
    steps~as.Date(date),
    Dataset,
    sum,
    na.rm = TRUE),
  c("Date","Steps")
)
Plotting the histogram:
plot of chunk unnamed-chunk-3 #Imputing Missing Values:

Missing_Values <- aggregate(cnt~date,cbind(Dataset[is.na(Dataset$steps),],cnt=c(1)),sum,na.rm = FALSE)
Missing_Values$dow <- weekdays(as.Date(Missing_Values$date),abbreviate=TRUE)
print(Missing_Values[,c(1,3,2)])
##         date dow cnt
## 1 2012-10-01 Mon 288
## 2 2012-10-08 Mon 288
## 3 2012-11-01 Thu 288
## 4 2012-11-04 Sun 288
## 5 2012-11-09 Fri 288
## 6 2012-11-10 Sat 288
## 7 2012-11-14 Wed 288
## 8 2012-11-30 Fri 288
unique(Missing_Values$dow)
## [1] "Mon" "Thu" "Sun" "Fri" "Sat" "Wed"
filling missing values:
Reference <- aggregate(steps~interval+weekdays(datetime,abbreviate=TRUE),Dataset,FUN=mean,na.rm=TRUE)
colnames(Reference) <- c("interval","dow","Avg_steps")
Reference$dow <- factor(Reference$dow,levels = c("Mon","Tue","Wed","Thu","Fri","Sat","Sun"))

ggplot(Reference,aes(x=interval,y=Avg_steps)) + geom_line() + facet_grid("dow ~ .")
plot of chunk unnamed-chunk-5 #Are there differences in activity patterns between weekdays and weekends?

week_diff <- aggregate(
  steps~dow+interval,  # group steps by weekend/weekday and interval to find average steps 
  with(
    Dataset,
    data.frame(
      dow = factor(
        ifelse(
          weekdays(as.Date(date)) %in% c("Sunday","Saturday"),
          "weekend",  # if sunday or saturday
          "weekday"   # else
        )
      ),
      interval,
      steps
    )
  ),
  FUN = mean,
  rm.na = TRUE
)
Plotting the result:
plot of chunk unnamed-chunk-7
