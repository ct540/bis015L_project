---
title: "BIS015L Project"
author: 
date: "2021-02-16"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---

## Load Libraries 


```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
library(paletteer)
library(readr)
```

## Load the Data

```r
diease_data <- readr::read_csv("Data/infectious-diseases-by-county-year-and-sex 2.csv")
```

```
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   Disease = col_character(),
##   County = col_character(),
##   Year = col_double(),
##   Sex = col_character(),
##   Cases = col_double(),
##   Population = col_double(),
##   Rate = col_character(),
##   Lower_95__CI = col_double(),
##   Upper_95__CI = col_double()
## )
```

```r
diease_data
```

```
## # A tibble: 164,433 x 9
##    Disease  County   Year Sex   Cases Population Rate  Lower_95__CI Upper_95__CI
##    <chr>    <chr>   <dbl> <chr> <dbl>      <dbl> <chr>        <dbl>        <dbl>
##  1 Amebias… Alameda  2001 Fema…     7     746596 0.93…        0.377        1.93 
##  2 Amebias… Alameda  2001 Male      9     718968 1.25…        0.572        2.38 
##  3 Amebias… Alameda  2001 Total    16    1465564 1.09…        0.624        1.77 
##  4 Amebias… Alameda  2002 Fema…     4     747987 0.53…        0.146        1.37 
##  5 Amebias… Alameda  2002 Male      5     720481 0.69…        0.225        1.62 
##  6 Amebias… Alameda  2002 Total     9    1468468 0.61…        0.28         1.16 
##  7 Amebias… Alameda  2003 Fema…     1     747441 0.13…        0.003        0.745
##  8 Amebias… Alameda  2003 Male      5     719746 0.69…        0.226        1.62 
##  9 Amebias… Alameda  2003 Total     7    1467187 0.47…        0.192        0.983
## 10 Amebias… Alameda  2004 Fema…     3     746723 0.40…        0.083        1.17 
## # … with 164,423 more rows
```

## Summary of Data


```r
diease_data <- janitor::clean_names(diease_data)
names(diease_data)
```

```
## [1] "disease"     "county"      "year"        "sex"         "cases"      
## [6] "population"  "rate"        "lower_95_ci" "upper_95_ci"
```


```r
skimr::skim(diease_data)
```


Table: Data summary

|                         |            |
|:------------------------|:-----------|
|Name                     |diease_data |
|Number of rows           |164433      |
|Number of columns        |9           |
|_______________________  |            |
|Column type frequency:   |            |
|character                |4           |
|numeric                  |5           |
|________________________ |            |
|Group variables          |None        |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|disease       |         0|             1|   7|  77|     0|       53|          0|
|county        |         0|             1|   4|  15|     0|       59|          0|
|sex           |         0|             1|   4|   6|     0|        3|          0|
|rate          |         0|             1|   1|   6|     0|    12105|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|      mean|         sd|      p0|      p25|       p50|       p75|        p100|hist  |
|:-------------|---------:|-------------:|---------:|----------:|-------:|--------:|---------:|---------:|-----------:|:-----|
|year          |         0|          1.00|   2010.28|       5.49| 2001.00|  2006.00|   2010.00|   2015.00|     2019.00|▇▇▆▇▇ |
|cases         |      4120|          0.97|     10.65|     142.95|    0.00|     0.00|      0.00|      0.00|    10001.00|▇▁▁▁▁ |
|population    |         0|          1.00| 848040.32| 3527101.31|  563.00| 29245.00| 125234.00| 422487.00| 39959095.00|▇▁▁▁▁ |
|lower_95_ci   |      4981|          0.97|      0.69|       5.48|    0.00|     0.00|      0.00|      0.00|      387.73|▇▁▁▁▁ |
|upper_95_ci   |      4981|          0.97|     22.08|      74.13|    0.01|     1.25|      4.08|     14.33|      653.08|▇▁▁▁▁ |


```r
naniar::miss_var_summary(diease_data)
```

```
## # A tibble: 9 x 3
##   variable    n_miss pct_miss
##   <chr>        <int>    <dbl>
## 1 lower_95_ci   4981     3.03
## 2 upper_95_ci   4981     3.03
## 3 cases         4120     2.51
## 4 disease          0     0   
## 5 county           0     0   
## 6 year             0     0   
## 7 sex              0     0   
## 8 population       0     0   
## 9 rate             0     0
```

## Data already has "total" value for each county, we may want to get rid of this as its just a repeated data?


