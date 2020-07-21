Traumatic Brain Injury Trends
================
Charlie Farison
2020-08-08

  - [Background](#background)
  - [Initial Data Exploration](#initial-data-exploration)

*Purpose*: We’d like to understand trends in causes of traumatic brain
injury.

# Background

<!-- -------------------------------------------------- -->

Blah Blah

# Initial Data Exploration

<!-- -------------------------------------------------- -->

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ───────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

Getting the data, from Tidy Tuesday’s TBI data set from the CDC

``` r
tbi_age <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-03-24/tbi_age.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   age_group = col_character(),
    ##   type = col_character(),
    ##   injury_mechanism = col_character(),
    ##   number_est = col_double(),
    ##   rate_est = col_double()
    ## )

``` r
tbi_year <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-03-24/tbi_year.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   injury_mechanism = col_character(),
    ##   type = col_character(),
    ##   year = col_double(),
    ##   rate_est = col_double(),
    ##   number_est = col_double()
    ## )

``` r
tbi_military <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-03-24/tbi_military.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   service = col_character(),
    ##   component = col_character(),
    ##   severity = col_character(),
    ##   diagnosed = col_double(),
    ##   year = col_double()
    ## )

Initial checks

``` r
glimpse(tbi_age)
```

    ## Rows: 231
    ## Columns: 5
    ## $ age_group        <chr> "0-17", "0-17", "0-17", "0-17", "0-17", "0-17", "0-1…
    ## $ type             <chr> "Emergency Department Visit", "Emergency Department …
    ## $ injury_mechanism <chr> "Motor Vehicle Crashes", "Unintentional Falls", "Uni…
    ## $ number_est       <dbl> 47138, 397190, 229236, 55785, NA, 24360, 57983, 5464…
    ## $ rate_est         <dbl> 64.1, 539.8, 311.6, 75.8, NA, 33.1, 78.8, 27.5, 1161…

``` r
summary(tbi_age)
```

    ##   age_group             type           injury_mechanism     number_est       
    ##  Length:231         Length:231         Length:231         Min.   :     16.0  
    ##  Class :character   Class :character   Class :character   1st Qu.:    603.8  
    ##  Mode  :character   Mode  :character   Mode  :character   Median :   2581.0  
    ##                                                           Mean   :  29960.6  
    ##                                                           3rd Qu.:  17431.5  
    ##                                                           Max.   :1213412.0  
    ##                                                           NA's   :11         
    ##     rate_est       
    ##  Min.   :   0.000  
    ##  1st Qu.:   1.375  
    ##  Median :   5.750  
    ##  Mean   :  50.973  
    ##  3rd Qu.:  43.850  
    ##  Max.   :1442.500  
    ##  NA's   :11

``` r
glimpse(tbi_year)
```

    ## Rows: 216
    ## Columns: 5
    ## $ injury_mechanism <chr> "Motor vehicle crashes", "Motor vehicle crashes", "M…
    ## $ type             <chr> "Emergency Department Visit", "Emergency Department …
    ## $ year             <dbl> 2006, 2007, 2008, 2009, 2010, 2011, 2012, 2013, 2014…
    ## $ rate_est         <dbl> 85.3, 83.8, 83.9, 88.7, 95.3, 98.7, 99.9, 99.6, 106.…
    ## $ number_est       <dbl> 254793, 252459, 254391, 270240, 292942, 305694, 3112…

``` r
summary(tbi_year)
```

    ##  injury_mechanism       type                year         rate_est     
    ##  Length:216         Length:216         Min.   :2006   Min.   :  0.10  
    ##  Class :character   Class :character   1st Qu.:2008   1st Qu.:  2.15  
    ##  Mode  :character   Mode  :character   Median :2010   Median :  7.95  
    ##                                        Mean   :2010   Mean   : 65.26  
    ##                                        3rd Qu.:2012   3rd Qu.: 57.00  
    ##                                        Max.   :2014   Max.   :801.90  
    ##                                                                       
    ##    number_est     
    ##  Min.   :    339  
    ##  1st Qu.:   5182  
    ##  Median :  19754  
    ##  Mean   : 115309  
    ##  3rd Qu.: 141855  
    ##  Max.   :1213476  
    ##  NA's   :27

``` r
glimpse(tbi_military)
```

    ## Rows: 450
    ## Columns: 5
    ## $ service   <chr> "Army", "Army", "Army", "Army", "Army", "Army", "Army", "Ar…
    ## $ component <chr> "Active", "Active", "Active", "Active", "Active", "Guard", …
    ## $ severity  <chr> "Penetrating", "Severe", "Moderate", "Mild", "Not Classifia…
    ## $ diagnosed <dbl> 189, 102, 709, 5896, 122, 33, 26, 177, 1332, 29, 12, 11, 63…
    ## $ year      <dbl> 2006, 2006, 2006, 2006, 2006, 2006, 2006, 2006, 2006, 2006,…

``` r
summary(tbi_military)
```

    ##    service           component           severity           diagnosed      
    ##  Length:450         Length:450         Length:450         Min.   :    1.0  
    ##  Class :character   Class :character   Class :character   1st Qu.:   15.0  
    ##  Mode  :character   Mode  :character   Mode  :character   Median :   41.5  
    ##                                                           Mean   :  554.8  
    ##                                                           3rd Qu.:  233.8  
    ##                                                           Max.   :13074.0  
    ##                                                           NA's   :12       
    ##       year     
    ##  Min.   :2006  
    ##  1st Qu.:2008  
    ##  Median :2010  
    ##  Mean   :2010  
    ##  3rd Qu.:2012  
    ##  Max.   :2014  
    ##
