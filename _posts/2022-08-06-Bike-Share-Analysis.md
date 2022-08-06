---
layout: post
title: Analyzing Bike Share Data
image: "/posts/bicycles_image.jpg"
tags: [R, Bike Share]
---

In this post I'm going to run through the codes used in R to answer a Bike sharing company's (Cyclistic) question:
How do annual members and casual riders use Cyclistic bikes differently?

I am tasked with the following:
  1- Identify how annual cyclists and casual ones use the company's bikes differently
  2- Provide recommendations that will help converting casual riders to annual members

To answer this business question, I've completed the following: 
collect data, wrangle & combine it, cleaning & modifying, conducting decriptive analysis, data vizualization, and finally data exportation.

The public data was collected from Divvy, for a company based in Chicago. Below are the license agreement and data location links:

https://ride.divvybikes.com/data-license-agreement 
&
https://divvy-tripdata.s3.amazonaws.com/index.html

---
The data used for this project was available in csv format on monthly basis from the period from July-2021 to June-2022.
Each month's data was downloaded and extracted from the zp folder, then uploaded to RStudio path. The csv files are named in this format:
yyyymm-divvy-tripdata.csv 

to conduct the analysis, the below packages and libraries were installed: 

```ruby
# installing required packages for: 

install.packages("tidyverse")     # wrangling data
install.packages("skimr")         # to provide quick descriptive analysis
install.packages("lubridate")     # wrangling data attributes
install.packages("ggplot2")       # for visualization


library(tidyverse)  # wrangle data
library(skimr)      # quick descriptive analysis
library(lubridate)  # wrangle date attributes
library(ggplot2)    # visualize data
library(readr)      # read files
library(dplyr)      # join data frames

getwd()             #displays working directory
```

#####STEP 1: Collecting data

The first step was to collect the data from the csv files and add the datasets to the working environment:

```ruby
# Uploading datasets (csv files)

cyclistic_202107 <- read_csv("202107-divvy-tripdata.csv")
cyclistic_202108 <- read_csv("202108-divvy-tripdata.csv")
cyclistic_202109 <- read_csv("202109-divvy-tripdata.csv")
cyclistic_202110 <- read_csv("202110-divvy-tripdata.csv")
cyclistic_202111 <- read_csv("202111-divvy-tripdata.csv")
cyclistic_202112 <- read_csv("202112-divvy-tripdata.csv")
cyclistic_202201 <- read_csv("202201-divvy-tripdata.csv")
cyclistic_202202 <- read_csv("202202-divvy-tripdata.csv")
cyclistic_202203 <- read_csv("202203-divvy-tripdata.csv")
cyclistic_202204 <- read_csv("202204-divvy-tripdata.csv")
cyclistic_202205 <- read_csv("202205-divvy-tripdata.csv")
cyclistic_202206 <- read_csv("202206-divvy-tripdata.csv")

```
Then, to ensure that the datasets can be combined into one large datase, the column names and types were checked to ensure that they are matching.

```ruby
# Comparing column names of each file
# the names don't have to be in order, but they must match *perfectly* to join the data


colnames(cyclistic_202107)
colnames(cyclistic_202108)
colnames(cyclistic_202109)
colnames(cyclistic_202110)
colnames(cyclistic_202111)
colnames(cyclistic_202112)
colnames(cyclistic_202201)
colnames(cyclistic_202202)
colnames(cyclistic_202203)
colnames(cyclistic_202204)
colnames(cyclistic_202205)
colnames(cyclistic_202206)

# all column names match


# inspecting data types
str(cyclistic_202107)
str(cyclistic_202108)
str(cyclistic_202109)
str(cyclistic_202110)
str(cyclistic_202111)
str(cyclistic_202112)
str(cyclistic_202201)
str(cyclistic_202202)
str(cyclistic_202203)
str(cyclistic_202204)
str(cyclistic_202205)
str(cyclistic_202206)

# all column data types match
```

#####STEP 2: Data wrangling and combining into a single file

and to combine the data into one data frame:

```ruby

# Stacking all monthly data into one big data frame

cyclistic <- bind_rows(                       cyclistic_202107,
                                              cyclistic_202108,
                                              cyclistic_202109,
                                              cyclistic_202110,
                                              cyclistic_202111,
                                              cyclistic_202112,
                                              cyclistic_202201,
                                              cyclistic_202202,
                                              cyclistic_202203,
                                              cyclistic_202204,
                                              cyclistic_202205,
                                              cyclistic_202206)
```
Checking that the data was combined successfully, the number of rows were compared before and after combinging the data
```ruby

# to ensure that all rows have been combined the below should return 0

zero_rows = nrow(cyclistic_202107) +  
            nrow(cyclistic_202108) + 
            nrow(cyclistic_202109) + 
            nrow(cyclistic_202110) + 
            nrow(cyclistic_202111) + 
            nrow(cyclistic_202112) + 
            nrow(cyclistic_202201) + 
            nrow(cyclistic_202202) + 
            nrow(cyclistic_202203) + 
            nrow(cyclistic_202204) + 
            nrow(cyclistic_202205) + 
            nrow(cyclistic_202206) -
            nrow(cyclistic)

print(zero_rows)

  >>> [1] 0
```
There are some unneessar columns that can be removed without impacting the final outcome:

```ruby
# longitude and latitude columns won't help answering the business question,
# so it is better to remove them and save space and processing power

cyclistic <- cyclistic %>% 
  select(-c(start_lat, start_lng, end_lat, end_lng))
```

#####STEP 3: Cleaning and adding data to prepare for analysis


To inspect a newly created table: 

```ruby
colnames(cyclistic)                # List of column names

  >>> [1] "ride_id"            "rideable_type"      "started_at"         "ended_at"           "start_station_name"
      [6] "start_station_id"   "end_station_name"   "end_station_id"     "member_casual"  
```

```ruby
nrow(cyclistic)                    # How many rows are in data frame?

  >>> [1] 5900385
```

```ruby
dim(cyclistic)                     # Dimensions of the data frame?

  >>> [1] 5900385       9
```

```ruby
head(cyclistic)                    # See the first 6 rows of data frame.  Also tail(cyclistic)

  >>> # A tibble: 6 × 9
        ride_id        rideable_type started_at          ended_at            start_station_n… start_station_id end_station_name
        <chr>          <chr>         <dttm>              <dttm>              <chr>            <chr>            <chr>           
      1 0A1B623926EF4… docked_bike   2021-07-02 14:44:36 2021-07-02 15:19:58 Michigan Ave & … 13001            Halsted St & No…
      2 B2D5583A5A5E7… classic_bike  2021-07-07 16:57:42 2021-07-07 17:16:09 California Ave … 17660            Wood St & Hubba…
      3 6F264597DDBF4… classic_bike  2021-07-25 11:30:55 2021-07-25 11:48:45 Wabash Ave & 16… SL-012           Rush St & Hubba…
      4 379B58EAB20E8… classic_bike  2021-07-08 22:08:30 2021-07-08 22:23:32 California Ave … 17660            Carpenter St & …
      5 6615C1E4EB08E… electric_bike 2021-07-28 16:08:06 2021-07-28 16:27:09 California Ave … 17660            Elizabeth (May)…
      6 62DC2B32872F9… electric_bike 2021-07-29 17:09:08 2021-07-29 17:15:00 California Ave … 17660            Albany Ave & Bl…
      # … with 2 more variables: end_station_id <chr>, member_casual <chr>
```

```ruby
str(cyclistic)                     # See list of columns and data types (numeric, character, etc)

  >>> tibble [5,900,385 × 9] (S3: tbl_df/tbl/data.frame)
       $ ride_id           : chr [1:5900385] "0A1B623926EF4E16" "B2D5583A5A5E76EE" "6F264597DDBF427A" "379B58EAB20E8AA5" ...
       $ rideable_type     : chr [1:5900385] "docked_bike" "classic_bike" "classic_bike" "classic_bike" ...
       $ started_at        : POSIXct[1:5900385], format: "2021-07-02 14:44:36" "2021-07-07 16:57:42" "2021-07-25 11:30:55" "2021-07-08 22:08:30" ...
       $ ended_at          : POSIXct[1:5900385], format: "2021-07-02 15:19:58" "2021-07-07 17:16:09" "2021-07-25 11:48:45" "2021-07-08 22:23:32" ...
       $ start_station_name: chr [1:5900385] "Michigan Ave & Washington St" "California Ave & Cortez St" "Wabash Ave & 16th St" "California Ave & Cortez St" ...
       $ start_station_id  : chr [1:5900385] "13001" "17660" "SL-012" "17660" ...
       $ end_station_name  : chr [1:5900385] "Halsted St & North Branch St" "Wood St & Hubbard St" "Rush St & Hubbard St" "Carpenter St & Huron St" ...
       $ end_station_id    : chr [1:5900385] "KA1504000117" "13432" "KA1503000044" "13196" ...
       $ member_casual     : chr [1:5900385] "casual" "casual" "member" "member" ...
```

```ruby
skim_without_charts(cyclistic)     # Statistical summary of data 

  >>> ── Data Summary ────────────────────────
                                 Values   
      Name                       cyclistic
      Number of rows             5900385  
      Number of columns          9        
      _______________________             
      Column type frequency:              
        character                7        
        POSIXct                  2        
      ________________________            
      Group variables            None     

      ── Variable type: character ─────────────────────────────────────────────────────────────────────────────────────────────
        skim_variable      n_missing complete_rate min max empty n_unique whitespace
      1 ride_id                    0         1      16  16     0  5900385          0
      2 rideable_type              0         1      11  13     0        3          0
      3 start_station_name    836018         0.858   3  64     0     1293          0
      4 start_station_id      836015         0.858   3  44     0     1157          0
      5 end_station_name      892103         0.849   9  64     0     1315          0
      6 end_station_id        892103         0.849   3  44     0     1171          0
      7 member_casual              0         1       6   6     0        2          0

      ── Variable type: POSIXct ───────────────────────────────────────────────────────────────────────────────────────────────
        skim_variable n_missing complete_rate min                 max                 median              n_unique
      1 started_at            0             1 2021-07-01 00:00:22 2022-06-30 23:59:58 2021-10-27 17:35:55  4924385
      2 ended_at              0             1 2021-07-01 00:04:51 2022-07-13 04:21:06 2021-10-27 17:49:46  4924865
```
Analysis of he above results, we can see that there are:
  1- three unique rideable_type
  2- many missing station names and id's
  3- two unique values in member_casual
  4- the data can only be aggregated at the ride-level, by adding additional columns, such as day, month year, we will have more opportunity to aggregate the data
  5- adding ride_length as a calculated field will be beneficial for analysis

By adding columns that list the date, month, day, and year of each ride will allow us to aggregate ride data for each month, day, or year
before completing these operations we can only aggregate at the ride level

this link provides more information of data formats in R:
https://www.statmethods.net/input/dates.html


```ruby
cyclistic$date        <- as.Date(cyclistic$started_at)          # output will be in yyyy-mm-dd
cyclistic$day         <- format(as.Date(cyclistic$date), "%d")  # output will be in range of 01-31
cyclistic$month       <- format(as.Date(cyclistic$date), "%m")  # output will be in range of 01-12
cyclistic$year        <- format(as.Date(cyclistic$date), "%Y")  # output will be in 4-digit year
cyclistic$day_of_week <- format(as.Date(cyclistic$date), "%A")  # output will be unabbreviated weekday 

```
now, to add the ride length calculation, we use the difftime() function, this will result in a new column with values in unit of seconds,
for more details, check out the below link:
https://stat.ethz.ch/R-manual/R-devel/library/base/html/difftime.html

```ruby
cyclistic$ride_length <- 
  difftime(cyclistic$ended_at, cyclistic$started_at)   
```
To check the newly added column, let's use str() again
```ruby
>>> str(cyclistic)
      tibble [5,900,385 × 15] (S3: tbl_df/tbl/data.frame)
       $ ride_id           : chr [1:5900385] "0A1B623926EF4E16" "B2D5583A5A5E76EE" "6F264597DDBF427A" "379B58EAB20E8AA5" ...
       $ rideable_type     : chr [1:5900385] "docked_bike" "classic_bike" "classic_bike" "classic_bike" ...
       $ started_at        : POSIXct[1:5900385], format: "2021-07-02 14:44:36" "2021-07-07 16:57:42" "2021-07-25 11:30:55" "2021-07-08 22:08:30" ...
       $ ended_at          : POSIXct[1:5900385], format: "2021-07-02 15:19:58" "2021-07-07 17:16:09" "2021-07-25 11:48:45" "2021-07-08 22:23:32" ...
       $ start_station_name: chr [1:5900385] "Michigan Ave & Washington St" "California Ave & Cortez St" "Wabash Ave & 16th St" "California Ave & Cortez St" ...
       $ start_station_id  : chr [1:5900385] "13001" "17660" "SL-012" "17660" ...
       $ end_station_name  : chr [1:5900385] "Halsted St & North Branch St" "Wood St & Hubbard St" "Rush St & Hubbard St" "Carpenter St & Huron St" ...
       $ end_station_id    : chr [1:5900385] "KA1504000117" "13432" "KA1503000044" "13196" ...
       $ member_casual     : chr [1:5900385] "casual" "casual" "member" "member" ...
       $ date              : Date[1:5900385], format: "2021-07-02" "2021-07-07" "2021-07-25" "2021-07-08" ...
       $ day               : chr [1:5900385] "02" "07" "25" "08" ...
       $ month             : chr [1:5900385] "07" "07" "07" "07" ...
       $ year              : chr [1:5900385] "2021" "2021" "2021" "2021" ...
       $ day_of_week       : chr [1:5900385] "Friday" "Wednesday" "Sunday" "Thursday" ...
       $ ride_length       : 'difftime' num [1:5900385] 2122 1107 1070 902 ...
        ..- attr(*, "units")= chr "secs"
```
Convert "ride_length" from Factor to numeric so we can run calculations on the data
```ruby
is.factor(cyclistic$ride_length)  # returns FALSE
  >>> [1] FALSE
```
to change from value in seconds to string to numeric value

```ruby
cyclistic$ride_length <- 
  as.numeric(as.character(cyclistic$ride_length))

```
to check if the conversion was completed correctly
```ruby
is.numeric(cyclistic$ride_length) # returns TRUE
  >>> [1] TRUE
```
Let's check the statistical summary of the ride_length column
```ruby
skim_without_charts(cyclistic$ride_length)     # Statistical summary of data 
  >>> ── Data Summary ────────────────────────
                                 Values               
      Name                       cyclistic$ride_length
      Number of rows             5900385              
      Number of columns          1                    
      _______________________                         
      Column type frequency:                          
        numeric                  1                    
      ________________________                        
      Group variables            None                 

      ── Variable type: numeric ───────────────────────────────────────────────────────────────────────────────────────────────
        skim_variable n_missing complete_rate  mean    sd    p0 p25 p50  p75    p100
      1 data                  0             1 1217. 9313. -8245 377 670 1212 2946429
```
we can see that the minimum value in the ride length column is negative, some of the entries in the data frame were when bikes got taken
out of docks and checked for quality, we must remove the negative data but it is best to create a new dataframe 

```ruby
cyclistic_v2 <- cyclistic %>% 
  filter(ride_length > 0)
```

#####STEP 4: Conducting descriptive analysis

descriptive analysis on ride_length (all values in seconds)

```ruby
mean(cyclistic_v2$ride_length)      #straight average (total ride length / rides)
  >>> [1] 1217.109
```

```ruby
median(cyclistic_v2$ride_length)    #midpoint number in the ascending array of ride lengths
  >>> [1] 670
```

```ruby
max(cyclistic_v2$ride_length)       #longest ride
  >>> [1] 2946429
```
```ruby
min(cyclistic_v2$ride_length)       #shortest ride
  >>> [1] 1

```
using summary(), the four chunks above can be condensed
```ruby
summary(cyclistic_v2$ride_length)
   >>> Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
          1     377     670    1217    1212 2946429 

```

to compare members and casual users aggregation fuction was used
this link provides examples on how to aggregate:
https://www.statology.org/r-mean-by-group/ 
 
 
average ride length by customer type:
```ruby
aggregate(cyclistic_v2$ride_length ~ cyclistic_v2$member_casual, FUN = mean)
  >>> cyclistic_v2$member_casual cyclistic_v2$ride_length
    1                     casual                 1789.431
    2                     member                  779.049

```
median ride length by customer type:
```ruby
aggregate(cyclistic_v2$ride_length ~ cyclistic_v2$member_casual, FUN = median)
  >>> cyclistic_v2$member_casual cyclistic_v2$ride_length
    1                     casual                      891
    2                     member                      544

```

max ride length by customer type:
```ruby
aggregate(cyclistic_v2$ride_length ~ cyclistic_v2$member_casual, FUN = max)
  >>> cyclistic_v2$member_casual cyclistic_v2$ride_length
    1                     casual                  2946429
    2                     member                    93594

```

min ride length by customer type:
```ruby
aggregate(cyclistic_v2$ride_length ~ cyclistic_v2$member_casual, FUN = min)
  >>> cyclistic_v2$member_casual cyclistic_v2$ride_length
    1                     casual                        1
    2                     member                        1

```
to get the average ride time by day of week for members vs casual users, we use the same aggregation function, but add another 
```ruby


```

```ruby


```






---