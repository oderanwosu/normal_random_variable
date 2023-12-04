---
title: "R Notebook"
output: html_notebook
---

This week, I'll be continuing my use of Open AQ API <https://api.openaq.org/v2/measurements> to retrieve numerical values. The data set I used focused on the particulate matter measurements (ug/m\^3) in Los Angeles between October 2st 2022 and October 1st 2023.

\
Measuring my values I found that my data is actually skewed to the right.

Once I take the sample averages for a sample size of 5 with 1000 averages, my distribution is mostly normal.

I increased my simple size 500 (my data set has 1000 data points) to make my values more accurate.

Last week, I calculated my EX to be about 8.071 and a variance of 65.789. However, the actual EX calculated is 5.391684. The actual variance is 0.0289399.

```{r}
library(lubridate)
library(dplyr)
library(ggplot2)
library(httr)
```

## Data set

```{r}
url <- "https://api.openaq.org/v2/measurements"

queryString <- list(
  date_from = "2022-10-01 19:00:00+00",
  date_to = "2023-10-01 19:00:00+00",
  limit = "1000",
  page = "1",
  offset = "0",
  sort = "desc",
  radius = "1000",
  city = "LOS ANGELES",
  order_by = "datetime",
  parameter = "pm25"
)

response <- VERB("GET", url, query = queryString, accept("application/json"))


api_data <- content(response, "text") 
parsed_data <- jsonlite::fromJSON(api_data)
```

## Configurations

```{r}
pm25_measure <- parsed_data$results

pm25_measure <- pm25_measure[pm25_measure$value > -1,]
```

```{r}
hist(pm25_measure$value, main = "Histogram of Particulate Matter Measure (ug/m^3) in the last 4 years")
?hist
```

```{r}

n=5 
num_averages <- 1000

sample_avg <- numeric(num_averages)

for(i in 1:num_averages){
  sample_avg[i] <-mean(sample(pm25_measure$value, size= n, replace = TRUE))
}

hist(sample_avg,  xlab="Averages" , main="Histogram of PM25 Sample Averages (ug/m^3)")
```

```{r}

n=500 
num_averages <- 1000

sample_avg <- numeric(num_averages)

for(i in 1:num_averages){
  sample_avg[i] <-mean(sample(pm25_measure$value, size= n, replace = TRUE))
}

hist(sample_avg)
```

```{r}
mean(sample_avg)
var(sample_avg)
```

```{r}
qnorm(.4, lower.tail = FALSE)
```
