Michelson Speed-of-light Measurements
================
Charlie Farison
2020-07-18

  - [Grading Rubric](#grading-rubric)
      - [Individual](#individual)
      - [Team](#team)
      - [Due Date](#due-date)
      - [Bibliography](#bibliography)

*Purpose*: When studying physical problems, there is an important
distinction between *error* and *uncertainty*. The primary purpose of
this challenge is to dip our toes into these factors by analyzing a real
dataset.

*Reading*: [Experimental Determination of the Velocity of
Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
(Optional)

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category    | Unsatisfactory                                                                   | Satisfactory                                                               |
| ----------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Effort      | Some task **q**’s left unattempted                                               | All task **q**’s attempted                                                 |
| Observed    | Did not document observations                                                    | Documented observations based on analysis                                  |
| Supported   | Some observations not supported by analysis                                      | All observations supported by analysis (table, graph, etc.)                |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Team

<!-- ------------------------- -->

| Category   | Unsatisfactory                                                                                   | Satisfactory                                       |
| ---------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| Documented | No team contributions to Wiki                                                                    | Team contributed to Wiki                           |
| Referenced | No team references in Wiki                                                                       | At least one reference in Wiki to member report(s) |
| Relevant   | References unrelated to assertion, or difficult to find related analysis based on reference text | Reference text clearly points to relevant analysis |

## Due Date

<!-- ------------------------- -->

All the deliverables stated in the rubrics above are due on the day of
the class discussion of that exercise. See the
[Syllabus](https://docs.google.com/document/d/1jJTh2DH8nVJd2eyMMoyNGroReo0BKcJrz1eONi3rPSc/edit?usp=sharing)
for more information.

``` r
# Libraries
library(tidyverse)
library(googlesheets4)

url <- "https://docs.google.com/spreadsheets/d/1av_SXn4j0-4Rk0mQFik3LLr-uf0YdA06i3ugE6n-Zdo/edit?usp=sharing"

# Parameters
LIGHTSPEED_VACUUM    <- 299792.458 # Exact speed of light in a vacuum (km / s)
LIGHTSPEED_MICHELSON <- 299944.00  # Michelson's speed estimate (km / s)
LIGHTSPEED_PM        <- 51         # Michelson error estimate (km / s)
```

*Background*: In 1879 Albert Michelson led an experimental campaign to
measure the speed of light. His approach was a development upon the
method of Foucault, and resulted in a new estimate of
\(v_0 = 299944 \pm 51\) kilometers per second (in a vacuum). This is
very close to the modern *exact* value of `r LIGHTSPEED_VACUUM`. In this
challenge, you will analyze Michelson’s original data, and explore some
of the factors associated with his experiment.

I’ve already copied Michelson’s data from his 1880 publication; the code
chunk below will load these data from a public googlesheet.

*Aside*: The speed of light is *exact* (there is **zero error** in the
value `LIGHTSPEED_VACUUM`) because the meter is actually
[*defined*](https://en.wikipedia.org/wiki/Metre#Speed_of_light_definition)
in terms of the speed of light\!

``` r
## Note: No need to edit this chunk!
gs4_deauth()
ss <- gs4_get(url)
df_michelson <-
  read_sheet(ss) %>%
  select(Date, Distinctness, Temp, Velocity) %>%
  mutate(Distinctness = as_factor(Distinctness))
```

    ## Reading from "michelson1879"

    ## Range "Sheet1"

``` r
df_michelson %>% glimpse
```

    ## Rows: 100
    ## Columns: 4
    ## $ Date         <dttm> 1879-06-05, 1879-06-07, 1879-06-07, 1879-06-07, 1879-06…
    ## $ Distinctness <fct> 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 2, 2, 2, 2, 2, 1, 3, 3, 2,…
    ## $ Temp         <dbl> 76, 72, 72, 72, 72, 72, 83, 83, 83, 83, 83, 90, 90, 71, …
    ## $ Velocity     <dbl> 299850, 299740, 299900, 300070, 299930, 299850, 299950, …

*Data dictionary*:

  - `Date`: Date of measurement
  - `Distinctness`: Distinctness of measured images: 3 = good, 2 = fair,
    1 = poor
  - `Temp`: Ambient temperature (Fahrenheit)
  - `Velocity`: Measured speed of light (km / s)

**q1** Re-create the following table (from Michelson (1880), pg. 139)
using `df_michelson` and `dplyr`. Note that your values *will not* match
those of Michelson *exactly*; why might this be?

| Distinctness | n  | MeanVelocity |
| ------------ | -- | ------------ |
| 3            | 46 | 299860       |
| 2            | 39 | 299860       |
| 1            | 15 | 299810       |

``` r
df_q1 <- df_michelson %>%
  group_by(Distinctness) %>%
  summarise(n = n(), MeanVelocity = mean(Velocity))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
df_q1 %>%
  arrange(desc(Distinctness)) %>%
  knitr::kable()
```

| Distinctness |  n | MeanVelocity |
| :----------- | -: | -----------: |
| 3            | 46 |     299861.7 |
| 2            | 39 |     299858.5 |
| 1            | 15 |     299808.0 |

**Observations**:

  - Most observations had distinctness level 2 or 3.
  - The mean was slightly lower for observations with poorer
    distinctness.
  - My table might differ from Michelson’s because his values seem to be
    rounded to the nearest 10.

The `Velocity` values in the dataset are the speed of light *in air*;
Michelson introduced a couple of adjustments to estimate the speed of
light in a vacuum. In total, he added \(+92\) km/s to his mean estimate
for `VelocityVacuum` (from Michelson (1880), pg. 141). While this isn’t
fully rigorous (\(+92\) km/s is based on the mean temperature), we’ll
simply apply this correction to all the observations in the dataset.

**q2** Create a new variable `VelocityVacuum` with the \(+92\) km/s
adjustment to `Velocity`. Assign this new dataframe to `df_q2`.

``` r
## TODO: Adjust the data, assign to df_q2
df_q2 <- 
  df_michelson %>%
  mutate(VelocityVacuum = Velocity + 92)
df_q2
```

    ## # A tibble: 100 x 5
    ##    Date                Distinctness  Temp Velocity VelocityVacuum
    ##    <dttm>              <fct>        <dbl>    <dbl>          <dbl>
    ##  1 1879-06-05 00:00:00 3               76   299850         299942
    ##  2 1879-06-07 00:00:00 2               72   299740         299832
    ##  3 1879-06-07 00:00:00 2               72   299900         299992
    ##  4 1879-06-07 00:00:00 2               72   300070         300162
    ##  5 1879-06-07 00:00:00 2               72   299930         300022
    ##  6 1879-06-07 00:00:00 2               72   299850         299942
    ##  7 1879-06-09 00:00:00 3               83   299950         300042
    ##  8 1879-06-09 00:00:00 3               83   299980         300072
    ##  9 1879-06-09 00:00:00 3               83   299980         300072
    ## 10 1879-06-09 00:00:00 3               83   299880         299972
    ## # … with 90 more rows

``` r
df_q2 %>%
  group_by(Distinctness) %>%
  summarise(n = n(), MeanVacVelocity = mean(VelocityVacuum))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 3 x 3
    ##   Distinctness     n MeanVacVelocity
    ##   <fct>        <int>           <dbl>
    ## 1 1               15         299900 
    ## 2 2               39         299950.
    ## 3 3               46         299954.

As part of his study, Michelson assessed the various potential sources
of error, and provided his best-guess for the error in his
speed-of-light estimate. These values are provided in
`LIGHTSPEED_MICHELSON`—his nominal estimate—and
`LIGHTSPEED_PM`—plus/minus bounds on his estimate. Put differently,
Michelson believed the true value of the speed-of-light probably lay
between `LIGHTSPEED_MICHELSON - LIGHTSPEED_PM` and `LIGHTSPEED_MICHELSON
+ LIGHTSPEED_PM`.

Let’s introduce some terminology:\[2\]

  - **Error** is the difference between a true value and an estimate of
    that value; for instance `LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON`.
  - **Uncertainty** is an analyst’s *assessment* of the error.

Since a “true” value is often not known in practice, one generally does
not know the error. The best they can do is quantify their degree of
uncertainty. We will learn some means of quantifying uncertainty in this
class, but for many real problems uncertainty includes some amount of
human judgment.\[2\]

**q3** Compare Michelson’s speed of light estimate against the modern
speed of light value. Is Michelson’s estimate of the error (his
uncertainty) greater or less than the true error?

``` r
## TODO: Compare Michelson's estimate and error against the true value
HighEstimate = LIGHTSPEED_MICHELSON + LIGHTSPEED_PM
LowEstimate = LIGHTSPEED_MICHELSON - LIGHTSPEED_PM
TrueValue = LIGHTSPEED_VACUUM
Difference = TrueValue - LIGHTSPEED_MICHELSON

HighEstimate
```

    ## [1] 299995

``` r
LowEstimate
```

    ## [1] 299893

``` r
TrueValue
```

    ## [1] 299792.5

``` r
Difference
```

    ## [1] -151.542

``` r
LIGHTSPEED_PM
```

    ## [1] 51

**Observations**:

  - Michelson estimated his uncertainty as 51 km/s, but the real error
    was about 151 km/s below the real value, so he underestimated the
    uncertainty.

**q4** You have access to a few other variables. Construct a few
visualizations of `VelocityVacuum` against these other factors. Are
there other patterns in the data that might help explain the difference
between Michelson’s estimate and `LIGHTSPEED_VACUUM`?

``` r
library(viridis)
df_q2 %>%
  ggplot() +
  geom_point(mapping = aes(x = Date, y = VelocityVacuum, color = Distinctness)) +
  geom_line(mapping = aes(x = Date, y = 299792.5)) +
  ggtitle("No Clear Trend for Distinctness in (Corrected) Readings")
```

![](c02-michelson-assignment_files/figure-gfm/visualization-distinct-1.png)<!-- -->

**Observations**:

  - There doesn’t seem to be a clear trend between distinctness and
    proximity to the real value, though as we noted before, readings
    with lower distinctness do seem to have a lower mean.
  - Most of the readings with low distinctness were taken on the same
    day.

<!-- end list -->

``` r
library(viridis)
df_q2 %>%
  ggplot() +
  geom_point(mapping = aes(x = Date, y = VelocityVacuum, color = Temp)) +
  geom_hline(yintercept = LIGHTSPEED_MICHELSON, linetype = "dashed") +
  geom_hline(yintercept = LIGHTSPEED_VACUUM) +
  scale_color_viridis() + 
  ggtitle("Most (Corrected) Readings Are Above the True Value")
```

![](c02-michelson-assignment_files/figure-gfm/visualization-temp-1.png)<!-- -->

**Observations**:

  - The vast majority of readings seem to be above the true value.

<!-- end list -->

``` r
df_q2 %>%
  filter(Temp > 71) %>%
  ggplot() +
  geom_point(mapping = aes(x = Date, y = VelocityVacuum, color = Temp)) +
  geom_hline(yintercept = LIGHTSPEED_MICHELSON, linetype = "dashed") +
  geom_hline(yintercept = LIGHTSPEED_VACUUM) +
  scale_color_viridis() +
  ggtitle("Above Temperature 71, All (Corrected) Readings Are Above the Real Value")
```

![](c02-michelson-assignment_files/figure-gfm/visualization-temp-high-1.png)<!-- -->

**Observations**:

  - Above a temperature of 71, all readings are above the real value.

<!-- end list -->

``` r
df_q2 %>%
  summarise(Michelson_Estimate = mean(VelocityVacuum))
```

    ## # A tibble: 1 x 1
    ##   Michelson_Estimate
    ##                <dbl>
    ## 1            299944.

``` r
df_q2 %>%
  filter(Temp < 71) %>%
  summarise(Michelson_Estimate_Excluding_High_Temps = mean(VelocityVacuum))
```

    ## # A tibble: 1 x 1
    ##   Michelson_Estimate_Excluding_High_Temps
    ##                                     <dbl>
    ## 1                                 299910.

**Observations**:

  - Excluding observations above a temperature of 71, the average gets
    closer to the real value, but only decreases by about 34 km/s,
    likely because there are only 2 data points that were below the real
    value.

<!-- end list -->

``` r
df_q2 %>%
  summarise(Count = n())
```

    ## # A tibble: 1 x 1
    ##   Count
    ##   <int>
    ## 1   100

``` r
df_q2 %>%
  filter(Temp < 71) %>%
  summarise(Count_Below_71 = n())
```

    ## # A tibble: 1 x 1
    ##   Count_Below_71
    ##            <int>
    ## 1             20

**Observations**:

  - Of the observations taken, only 20 of the 100 observations were
    taken below a temperature of 71. Doing more observations at a lower
    temperature may have helped get more accurate data. Doing a more
    rigorous temperature correction may have helped as well.

<!-- end list -->

``` r
df_q2 %>%
  ggplot() +
  geom_point(mapping = aes(x = Date, y = Velocity, color = Temp)) +
  geom_hline(yintercept = LIGHTSPEED_VACUUM) +
  scale_color_viridis() +
  ggtitle("Even Before Correction, Most Readings Are Above the Real Value")
```

![](c02-michelson-assignment_files/figure-gfm/visualization-pre-correction-1.png)<!-- -->

**Observations**:

  - Even before the correction for measurement in air rather than
    vacuum, most readings were above the real value.

<!-- end list -->

``` r
df_q2 %>%
  summarise(Uncorrected_Mean = mean(Velocity))
```

    ## # A tibble: 1 x 1
    ##   Uncorrected_Mean
    ##              <dbl>
    ## 1          299852.

**Observations**:

  - Before correction for measurement in air, the mean is 299852.4 km/s.
    This is only 59.9 km/s below the real value, and almost within
    Michelson’s predicted uncertainty. But values were corrected for
    measurement in air by adding 94 km/s, which skewed the estimate
    high.

**Summary of Observations**:

  - High temperature does seem to skew the mean up. Perhaps using a
    correction of temperature by point, rather than correcting all
    values by adding +94 km/s based on the mean temperature, would have
    helped. Taking more readings at lower temperatures, below 71, may
    also have helped.

## Bibliography

  - \[1\] Michelson, [Experimental Determination of the Velocity of
    Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
    (1880)
  - \[2\] Henrion and Fischhoff, [Assessing Uncertainty in Physical
    Constants](https://www.cmu.edu/epp/people/faculty/research/Fischoff-Henrion-Assessing%20uncertainty%20in%20physical%20constants.pdf)
    (1986)
