About datetime data in R
================

<!-- This is an [R Markdown](http://rmarkdown.rstudio.com) Notebook. When you execute code within the notebook, the results appear beneath the code.  -->
I often need to deal with date and time data in my data analysis. And sometimes I read in csv file where all the timestamps are read in as characters. I will need to process those character timestamps so I can compare, add/substract date to them later. There are three scenarios here.

-   How to combine date and time data types in R dataframe into timestamp and how to compare/do computation of the timestamps. For example, I created a sample dataframe called demo1 with three columns: date, time and value.

``` r
dt<-c('2017-11-18', '2017-11-16', '2017-11-20', '2017-11-01')
tm<-c('10:00:00', '23:59:59', '00:00:00', '00:00:02')
value<-c(10,20,30,40)
df<-data.frame(dt,tm,value,stringsAsFactors = F)
print(df)
```

    ##           dt       tm value
    ## 1 2017-11-18 10:00:00    10
    ## 2 2017-11-16 23:59:59    20
    ## 3 2017-11-20 00:00:00    30
    ## 4 2017-11-01 00:00:02    40

Now I combine dt and tm into one column called ts(timestamp) and sum up the value after 2017-11-01 20:00:00.

``` r
df$ts<-as.POSIXct(paste(df$dt,df$tm),format = '%Y-%m-%d %H:%M:%S')
print(df)
```

    ##           dt       tm value                  ts
    ## 1 2017-11-18 10:00:00    10 2017-11-18 10:00:00
    ## 2 2017-11-16 23:59:59    20 2017-11-16 23:59:59
    ## 3 2017-11-20 00:00:00    30 2017-11-20 00:00:00
    ## 4 2017-11-01 00:00:02    40 2017-11-01 00:00:02

``` r
#Now sum up the value after '2017-11-01 20:00:00'
sum(df[which(df$ts>'2017-11-01 20:00:00'),'value'])
```

    ## [1] 60

We got the right output 60.

Now I want to sum up the value 2 days after current datetime. That is, I will add 2 days to ts and sum up all the value with the timestamp larger than that. Remember that we have set ts to "POSIXct", which represents the seconds from the origin time(depends on differnt R version and OS system). So we can directly add 2 days = 48\*3600 seconds to the ts column. We get value 0 at this time because no timestamp is larger than 2017-11-20 10:00:00.

``` r
df$ts2<-df$ts+2*24*3600
print(df)
```

    ##           dt       tm value                  ts                 ts2
    ## 1 2017-11-18 10:00:00    10 2017-11-18 10:00:00 2017-11-20 10:00:00
    ## 2 2017-11-16 23:59:59    20 2017-11-16 23:59:59 2017-11-18 23:59:59
    ## 3 2017-11-20 00:00:00    30 2017-11-20 00:00:00 2017-11-22 00:00:00
    ## 4 2017-11-01 00:00:02    40 2017-11-01 00:00:02 2017-11-03 00:00:02

``` r
##Check time difference between ts and ts2
difftime(df$ts, df$ts2, units='days')
```

    ## Time differences in days
    ## [1] -2 -2 -2 -2

``` r
##Sum up the value with ts larger than the first ds2
sum(df[which(df$ts>df$ts2[1]),'value'])
```

    ## [1] 0

-   How to transform character into timestamp datatype in R dataframe. One can use `as.POSIXct(x, format = '%Y-%m-%d %H:%M:%S')` suppose x is a datatime value with character data type. <!-- Try executing this chunk by clicking the *Run* button within the chunk or by placing your cursor inside it and pressing *Cmd+Shift+Enter*.  -->

-   How to use cast to transform a character time into time type when reading data from database into R. This can be done using `cast(col_name as time)` in the SQL query.

<!-- Add a new chunk by clicking the *Insert Chunk* button on the toolbar or by pressing *Cmd+Option+I*. -->
<!-- When you save the notebook, an HTML file containing the code and output will be saved alongside it (click the *Preview* button or press *Cmd+Shift+K* to preview the HTML file).  -->
<!-- The preview shows you a rendered HTML copy of the contents of the editor. Consequently, unlike *Knit*, *Preview* does not run any R code chunks. Instead, the output of the chunk when it was last run in the editor is displayed. -->
