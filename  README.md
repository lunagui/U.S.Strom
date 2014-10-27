# U.S. weather events damage data explore
Luna Gui  
22 October 2014  

## synopsis

**This project explored the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. The main purpose of this project is try to find out which types of events are most harmful with respect to population health, and which types of events have the greatest economic consequences.** 

**I use Macbook pro OS X version 10.9.5 and RStudio Version 0.98.1062. My working environment detail:**


```r
sessionInfo()
```

```
## R version 3.1.1 (2014-07-10)
## Platform: x86_64-apple-darwin10.8.0 (64-bit)
## 
## locale:
## [1] en_NZ.UTF-8/en_NZ.UTF-8/en_NZ.UTF-8/C/en_NZ.UTF-8/en_NZ.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## loaded via a namespace (and not attached):
## [1] digest_0.6.4    evaluate_0.5.5  formatR_1.0     htmltools_0.2.6
## [5] knitr_1.6       rmarkdown_0.3.3 stringr_0.6.2   tools_3.1.1    
## [9] yaml_2.1.13
```

##Data Processing

You can download the file from the course web site: [Storm Data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2) [47Mb] (Or you can run my code chunk to download file.)

Here you will find how some of the variables are constructed/defined:  
1. National Weather Service [Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf)  
2. National Climatic Data Center Storm Events [FAQ](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf)


```r
zipfileurl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
if(!file.exists("StormData.csv.bz2")){
        download.file(zipfileurl, destfile = "StormData.csv.bz2", method = "curl")
        dataDownloaded <- date()
        unzip("StormData.csv.bz2")
}
```


## Explore data
**Whole data set looks quite big, we just read first 100 row to check data structure.**

```r
StormDataEx <- read.csv("StormData.csv", nrows = 100)
str(StormDataEx)
```

```
## 'data.frame':	100 obs. of  37 variables:
##  $ STATE__   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_DATE  : Factor w/ 51 levels "1/20/1953 0:00:00",..: 32 32 14 49 7 7 8 2 11 11 ...
##  $ BGN_TIME  : int  130 145 1600 900 1500 2000 100 900 2000 2000 ...
##  $ TIME_ZONE : Factor w/ 1 level "CST": 1 1 1 1 1 1 1 1 1 1 ...
##  $ COUNTY    : num  97 3 57 89 43 77 9 123 125 57 ...
##  $ COUNTYNAME: Factor w/ 40 levels "BALDWIN","BARBOUR",..: 29 1 17 27 13 24 4 37 38 17 ...
##  $ STATE     : Factor w/ 1 level "AL": 1 1 1 1 1 1 1 1 1 1 ...
##  $ EVTYPE    : Factor w/ 3 levels "HAIL","TORNADO",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ BGN_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ BGN_AZI   : logi  NA NA NA NA NA NA ...
##  $ BGN_LOCATI: logi  NA NA NA NA NA NA ...
##  $ END_DATE  : logi  NA NA NA NA NA NA ...
##  $ END_TIME  : logi  NA NA NA NA NA NA ...
##  $ COUNTY_END: num  0 0 0 0 0 0 0 0 0 0 ...
##  $ COUNTYENDN: logi  NA NA NA NA NA NA ...
##  $ END_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ END_AZI   : logi  NA NA NA NA NA NA ...
##  $ END_LOCATI: logi  NA NA NA NA NA NA ...
##  $ LENGTH    : num  14 2 0.1 0 0 1.5 1.5 0 3.3 2.3 ...
##  $ WIDTH     : num  100 150 123 100 150 177 33 33 100 100 ...
##  $ F         : int  3 2 2 2 2 2 2 1 3 3 ...
##  $ MAG       : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 3 levels "","K","M": 2 2 2 2 2 2 2 2 2 2 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: logi  NA NA NA NA NA NA ...
##  $ WFO       : logi  NA NA NA NA NA NA ...
##  $ STATEOFFIC: logi  NA NA NA NA NA NA ...
##  $ ZONENAMES : logi  NA NA NA NA NA NA ...
##  $ LATITUDE  : num  3040 3042 3340 3458 3412 ...
##  $ LONGITUDE : num  8812 8755 8742 8626 8642 ...
##  $ LATITUDE_E: num  3051 0 0 0 0 ...
##  $ LONGITUDE_: num  8806 0 0 0 0 ...
##  $ REMARKS   : logi  NA NA NA NA NA NA ...
##  $ REFNUM    : num  1 2 3 4 5 6 7 8 9 10 ...
```

**We select useful colum to build our dataset. We choose BGN_DATE which is date of event happened, STATE for state short name, EVTYPE for event type, FATALITIES for fatality numbers, INJURIES for injury numbers and PROPDMG for property damage. **


```r
## read useful data
mycols <- rep("NULL", 37); mycols[c(2,7,8,23,24,25)] <- NA
StormData <- read.csv("StormData.csv", sep=",", colClasses=mycols)
str(StormData)
```

```
## 'data.frame':	902297 obs. of  6 variables:
##  $ BGN_DATE  : Factor w/ 16335 levels "1/1/1966 0:00:00",..: 6523 6523 4242 11116 2224 2224 2260 383 3980 3980 ...
##  $ STATE     : Factor w/ 72 levels "AK","AL","AM",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
```

```r
head(StormData)
```

```
##             BGN_DATE STATE  EVTYPE FATALITIES INJURIES PROPDMG
## 1  4/18/1950 0:00:00    AL TORNADO          0       15    25.0
## 2  4/18/1950 0:00:00    AL TORNADO          0        0     2.5
## 3  2/20/1951 0:00:00    AL TORNADO          0        2    25.0
## 4   6/8/1951 0:00:00    AL TORNADO          0        2     2.5
## 5 11/15/1951 0:00:00    AL TORNADO          0        2     2.5
## 6 11/15/1951 0:00:00    AL TORNADO          0        6     2.5
```

```r
tail(StormData)
```

```
##                  BGN_DATE STATE         EVTYPE FATALITIES INJURIES PROPDMG
## 902292 11/28/2011 0:00:00    TN WINTER WEATHER          0        0       0
## 902293 11/30/2011 0:00:00    WY      HIGH WIND          0        0       0
## 902294 11/10/2011 0:00:00    MT      HIGH WIND          0        0       0
## 902295  11/8/2011 0:00:00    AK      HIGH WIND          0        0       0
## 902296  11/9/2011 0:00:00    AK       BLIZZARD          0        0       0
## 902297 11/28/2011 0:00:00    AL     HEAVY SNOW          0        0       0
```

**Frome head and tail data, We can see whole data include approximately 60 years data.**  


```r
summary(StormData)
```

```
##               BGN_DATE          STATE                      EVTYPE      
##  5/25/2011 0:00:00:  1202   TX     : 83728   HAIL             :288661  
##  4/27/2011 0:00:00:  1193   KS     : 53440   TSTM WIND        :219940  
##  6/9/2011 0:00:00 :  1030   OK     : 46802   THUNDERSTORM WIND: 82563  
##  5/30/2004 0:00:00:  1016   MO     : 35648   TORNADO          : 60652  
##  4/4/2011 0:00:00 :  1009   IA     : 31069   FLASH FLOOD      : 54277  
##  4/2/2006 0:00:00 :   981   NE     : 30271   FLOOD            : 25326  
##  (Other)          :895866   (Other):621339   (Other)          :170878  
##    FATALITIES     INJURIES         PROPDMG    
##  Min.   :  0   Min.   :   0.0   Min.   :   0  
##  1st Qu.:  0   1st Qu.:   0.0   1st Qu.:   0  
##  Median :  0   Median :   0.0   Median :   0  
##  Mean   :  0   Mean   :   0.2   Mean   :  12  
##  3rd Qu.:  0   3rd Qu.:   0.0   3rd Qu.:   0  
##  Max.   :583   Max.   :1700.0   Max.   :5000  
## 
```

**Above summary shows that some max value stands out. We check it by plot.**


```r
plot(StormData$EVTYPE, StormData$FATALITIES, main = "Fatalities" , xlab = "Event Type")
```

![plot of chunk plot1](./USStom_files/figure-html/plot11.png) 

```r
plot(StormData$EVTYPE, StormData$INJURIES, main = "Injuries" , xlab = "Event Type")
```

![plot of chunk plot1](./USStom_files/figure-html/plot12.png) 

```r
plot(StormData$EVTYPE, StormData$PROPDMG, main = "Property Damage" , xlab = "Event Type")
```

![plot of chunk plot1](./USStom_files/figure-html/plot13.png) 

**We can see some events' damage extremely high.**


```r
subset(StormData, StormData$FATALITIES==max(StormData$FATALITIES) )
```

```
##                 BGN_DATE STATE EVTYPE FATALITIES INJURIES PROPDMG
## 198704 7/12/1995 0:00:00    IL   HEAT        583        0       0
```

```r
subset(StormData, StormData$INJURIES==max(StormData$INJURIES), )
```

```
##                 BGN_DATE STATE  EVTYPE FATALITIES INJURIES PROPDMG
## 157885 4/10/1979 0:00:00    TX TORNADO         42     1700     250
```

```r
subset(StormData, StormData$PROPDMG==max(StormData$PROPDMG), )
```

```
##                  BGN_DATE STATE            EVTYPE FATALITIES INJURIES
## 778568  7/26/2009 0:00:00    NC THUNDERSTORM WIND          0        0
## 808182  5/13/2010 0:00:00    IL       FLASH FLOOD          0        0
## 808183  5/13/2010 0:00:00    IL       FLASH FLOOD          0        0
## 900685 10/29/2011 0:00:00    AM        WATERSPOUT          0        0
##        PROPDMG
## 778568    5000
## 808182    5000
## 808183    5000
## 900685    5000
```

## Results
**For now, we just get some information by single data. How about total number of fatalities and injuries caused by event type? We can list top 10 damage event. **


```r
## Get sum value and make new data for us
sumFatalities <- tapply(StormData$FATALITIES, StormData$EVTYPE, sum)
sumInjuries <- tapply(StormData$INJURIES, StormData$EVTYPE, sum)
sumPropdmg <- tapply(StormData$PROPDMG, StormData$EVTYPE, sum)
eventType <- levels(StormData$EVTYPE)
Sumdata <- data.frame(eventtype = eventType, sum_fatalities = as.vector(sumFatalities), sum_injuries = as.vector(sumInjuries), sum_propdmg = as.vector(sumPropdmg))
Maxfatalities <- Sumdata[order(-Sumdata$sum_fatalities, -Sumdata$sum_injuries),][1:10,1:3]
Maxpropdmg <- Sumdata[order(-Sumdata$sum_propdmg),][1:10,-c(2,3)]
## Top 10 event types which are most harmful with respect to population health.
library(xtable)
print(xtable(Maxfatalities), type = "html")
```

<!-- html table generated in R 3.1.1 by xtable 1.7-4 package -->
<!-- Mon Oct 27 17:27:12 2014 -->
<table border=1>
<tr> <th>  </th> <th> eventtype </th> <th> sum_fatalities </th> <th> sum_injuries </th>  </tr>
  <tr> <td align="right"> 834 </td> <td> TORNADO </td> <td align="right"> 5633.00 </td> <td align="right"> 91346.00 </td> </tr>
  <tr> <td align="right"> 130 </td> <td> EXCESSIVE HEAT </td> <td align="right"> 1903.00 </td> <td align="right"> 6525.00 </td> </tr>
  <tr> <td align="right"> 153 </td> <td> FLASH FLOOD </td> <td align="right"> 978.00 </td> <td align="right"> 1777.00 </td> </tr>
  <tr> <td align="right"> 275 </td> <td> HEAT </td> <td align="right"> 937.00 </td> <td align="right"> 2100.00 </td> </tr>
  <tr> <td align="right"> 464 </td> <td> LIGHTNING </td> <td align="right"> 816.00 </td> <td align="right"> 5230.00 </td> </tr>
  <tr> <td align="right"> 856 </td> <td> TSTM WIND </td> <td align="right"> 504.00 </td> <td align="right"> 6957.00 </td> </tr>
  <tr> <td align="right"> 170 </td> <td> FLOOD </td> <td align="right"> 470.00 </td> <td align="right"> 6789.00 </td> </tr>
  <tr> <td align="right"> 585 </td> <td> RIP CURRENT </td> <td align="right"> 368.00 </td> <td align="right"> 232.00 </td> </tr>
  <tr> <td align="right"> 359 </td> <td> HIGH WIND </td> <td align="right"> 248.00 </td> <td align="right"> 1137.00 </td> </tr>
  <tr> <td align="right"> 19 </td> <td> AVALANCHE </td> <td align="right"> 224.00 </td> <td align="right"> 170.00 </td> </tr>
   </table>

```r
##Top 10 event types which have the greatest economic consequences.
print(xtable(Maxpropdmg), type = "html")
```

<!-- html table generated in R 3.1.1 by xtable 1.7-4 package -->
<!-- Mon Oct 27 17:27:12 2014 -->
<table border=1>
<tr> <th>  </th> <th> eventtype </th> <th> sum_propdmg </th>  </tr>
  <tr> <td align="right"> 834 </td> <td> TORNADO </td> <td align="right"> 3212258.16 </td> </tr>
  <tr> <td align="right"> 153 </td> <td> FLASH FLOOD </td> <td align="right"> 1420124.59 </td> </tr>
  <tr> <td align="right"> 856 </td> <td> TSTM WIND </td> <td align="right"> 1335965.61 </td> </tr>
  <tr> <td align="right"> 170 </td> <td> FLOOD </td> <td align="right"> 899938.48 </td> </tr>
  <tr> <td align="right"> 760 </td> <td> THUNDERSTORM WIND </td> <td align="right"> 876844.17 </td> </tr>
  <tr> <td align="right"> 244 </td> <td> HAIL </td> <td align="right"> 688693.38 </td> </tr>
  <tr> <td align="right"> 464 </td> <td> LIGHTNING </td> <td align="right"> 603351.78 </td> </tr>
  <tr> <td align="right"> 786 </td> <td> THUNDERSTORM WINDS </td> <td align="right"> 446293.18 </td> </tr>
  <tr> <td align="right"> 359 </td> <td> HIGH WIND </td> <td align="right"> 324731.56 </td> </tr>
  <tr> <td align="right"> 972 </td> <td> WINTER STORM </td> <td align="right"> 132720.59 </td> </tr>
   </table>

**Ok, it's no surprise that TORNADO caused hightest damage for people health and property lose. But it's quite surprised that HEAT caused so much higher damage for people health.**
