---
title: "BIS015L Project"
authors: "Chloe, Emily, Ibrahim"
date: "2021-03-04"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---

## Load Libraries 


```r
library(tidyverse)
library(janitor)
library(svglite)
library(shiny)
library(shinydashboard)
options(scipen=999)
```

## Load the Data

```r
disease <- readr::read_csv("Data/infectious-diseases-by-county-year-and-sex 2.csv")
```

```
## 
## -- Column specification --------------------------------------------------------
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
disease
```

```
## # A tibble: 164,433 x 9
##    Disease  County   Year Sex   Cases Population Rate  Lower_95__CI Upper_95__CI
##    <chr>    <chr>   <dbl> <chr> <dbl>      <dbl> <chr>        <dbl>        <dbl>
##  1 Amebias~ Alameda  2001 Fema~     7     746596 0.93~        0.377        1.93 
##  2 Amebias~ Alameda  2001 Male      9     718968 1.25~        0.572        2.38 
##  3 Amebias~ Alameda  2001 Total    16    1465564 1.09~        0.624        1.77 
##  4 Amebias~ Alameda  2002 Fema~     4     747987 0.53~        0.146        1.37 
##  5 Amebias~ Alameda  2002 Male      5     720481 0.69~        0.225        1.62 
##  6 Amebias~ Alameda  2002 Total     9    1468468 0.61~        0.28         1.16 
##  7 Amebias~ Alameda  2003 Fema~     1     747441 0.13~        0.003        0.745
##  8 Amebias~ Alameda  2003 Male      5     719746 0.69~        0.226        1.62 
##  9 Amebias~ Alameda  2003 Total     7    1467187 0.47~        0.192        0.983
## 10 Amebias~ Alameda  2004 Fema~     3     746723 0.40~        0.083        1.17 
## # ... with 164,423 more rows
```

## Tidy Names of Data

```r
disease_tidy <- janitor::clean_names(disease)
names(disease_tidy)
```

```
## [1] "disease"     "county"      "year"        "sex"         "cases"      
## [6] "population"  "rate"        "lower_95_ci" "upper_95_ci"
```

## Remove data we will not use

```r
disease_data <- disease_tidy %>% 
  select(disease, county, year, sex, cases, population)
disease_data
```

```
## # A tibble: 164,433 x 6
##    disease   county   year sex    cases population
##    <chr>     <chr>   <dbl> <chr>  <dbl>      <dbl>
##  1 Amebiasis Alameda  2001 Female     7     746596
##  2 Amebiasis Alameda  2001 Male       9     718968
##  3 Amebiasis Alameda  2001 Total     16    1465564
##  4 Amebiasis Alameda  2002 Female     4     747987
##  5 Amebiasis Alameda  2002 Male       5     720481
##  6 Amebiasis Alameda  2002 Total      9    1468468
##  7 Amebiasis Alameda  2003 Female     1     747441
##  8 Amebiasis Alameda  2003 Male       5     719746
##  9 Amebiasis Alameda  2003 Total      7    1467187
## 10 Amebiasis Alameda  2004 Female     3     746723
## # ... with 164,423 more rows
```

## Summary of Data


```r
skimr::skim(disease_data)
```


Table: Data summary

|                         |             |
|:------------------------|:------------|
|Name                     |disease_data |
|Number of rows           |164433       |
|Number of columns        |6            |
|_______________________  |             |
|Column type frequency:   |             |
|character                |3            |
|numeric                  |3            |
|________________________ |             |
|Group variables          |None         |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|disease       |         0|             1|   7|  77|     0|       53|          0|
|county        |         0|             1|   4|  15|     0|       59|          0|
|sex           |         0|             1|   4|   6|     0|        3|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|      mean|         sd|   p0|   p25|    p50|    p75|     p100|hist                                     |
|:-------------|---------:|-------------:|---------:|----------:|----:|-----:|------:|------:|--------:|:----------------------------------------|
|year          |         0|          1.00|   2010.28|       5.49| 2001|  2006|   2010|   2015|     2019|▇▇▆▇▇ |
|cases         |      4120|          0.97|     10.65|     142.95|    0|     0|      0|      0|    10001|▇▁▁▁▁ |
|population    |         0|          1.00| 848040.32| 3527101.31|  563| 29245| 125234| 422487| 39959095|▇▁▁▁▁ |


```r
dim(disease_data)
```

```
## [1] 164433      6
```


```r
naniar::miss_var_summary(disease_data)
```

```
## # A tibble: 6 x 3
##   variable   n_miss pct_miss
##   <chr>       <int>    <dbl>
## 1 cases        4120     2.51
## 2 disease         0     0   
## 3 county          0     0   
## 4 year            0     0   
## 5 sex             0     0   
## 6 population      0     0
```

## Shiny App


```r
dat <- disease_data %>% filter(sex=="Total") %>% select(!sex)

ui <- dashboardPage(skin="purple",
  dashboardHeader(title = "Disease Abundance"),
  dashboardSidebar(disable = T),
  dashboardBody(
  fluidRow(
  box(title = "Plot Options", width = 3,
  selectInput("fill", "Select Disease", choices=unique(disease_data$disease)),
                hr(),
      helpText("The data represent cases with an estimated illness onset date from 2001 through the last year indicated from California Confidential Morbidity Reports and/or Laboratory Reports")
  ),
  
  box(title = "Disease Incidence Per California County", width = 9,
  plotOutput("plot", width = "1200px", height = "500px", click="plot_click"),
  verbatimTextOutput("info")
  ) 
  ) 
  )
  ) 

server <- function(input, output, session) { 
  
  output$plot <- renderPlot({
  disease_data %>% 
  filter(county!="California", disease==input$fill) %>% 
  ggplot(aes_string(x = "county", y="cases", fill="county")) + 
  geom_col()+
  theme_light(base_size = 18)+
  labs (x="County", y="Cases", fill="Disease")+
  theme(axis.text.x = element_text(angle = 60, hjust = 1), legend.position="none")
  })

  
   output$info <- renderText({
    paste0("x=", input$plot_click$x, "\ny=", input$plot_click$y)
})
  session$onSessionEnded(stopApp)
  
} 
shinyApp(ui, server)
```

`<div style="width: 100% ; height: 400px ; text-align: center; box-sizing: border-box; -moz-box-sizing: border-box; -webkit-box-sizing: border-box;" class="muted well">Shiny applications not supported in static R Markdown documents</div>`{=html}