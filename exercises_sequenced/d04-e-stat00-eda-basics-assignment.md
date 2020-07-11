EDA Basics
================
2020-05-07

*Purpose*: *Exploratory Data Analysis* (EDA) is a **crucial** skill for
a practicing data scientist. Unfortunately, much like human-centered
design EDA is hard to teach. This is because EDA is **not** a strict
procedure, so much as it is a **mindset**. Also, much like
human-centered design, EDA is an *iterative, nonlinear process*. There
are two key principles to keep in mind when doing EDA:

  - 1.  Curiosity: Generate lots of ideas and hypotheses about your
        data.

  - 2.  Skepticism: Remain unconvinced of those ideas, unless you can
        find credible patterns to support them.

Since EDA is both *crucial* and *difficult*, we will practice doing EDA
*a lot* in this course\!

*Reading*: [Exploratory Data
Analysis](https://rstudio.cloud/learn/primers/3.1) *Topics*: (All
topics) *Reading Time*: \~45 minutes

*Note*: This exercise will consist of interpreting pre-made graphs. You
can run the whole notebook to generate all the figures at once. Just
make sure to do all the exercises and write your observations\!

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

**q0** Remember from `e02-data-basics` there were *simple checks* we’re
supposed to do? Do those simple checks on the diamonds dataset below.

I’m going to walk you through a train of thought I had when studying the
diamonds dataset.

There are four standard “C’s” of
[judging](https://en.wikipedia.org/wiki/Diamond_\(gemstone\)) a
diamond.\[1\] These are `carat, cut, color` and `clarity`, all of which
are in the `diamonds` dataset.

### Hypothesis 1

**Here’s a hypothesis**: `Ideal` is the “best” value of `cut` for a
diamond. Since an `Ideal` cut seems more labor-intensive, I hypothesize
that `Ideal` cut diamonds are less numerous than other cuts.

**q1** Run the chunk below, and study the plot. Was hypothesis 1
correct? Why or why not?

``` r
diamonds %>%
  ggplot(aes(cut)) +
  geom_bar()
```

![](d04-e-stat00-eda-basics-assignment_files/figure-gfm/q1-task-1.png)<!-- -->

**Observations**: - Write your observations here\!

### Hypothesis 2

**Another hypothesis**: The `Ideal` cut diamonds should be the most
pricey.

**q2.1** Study the following graph; does it support, contradict, or not
relate to hypothesis 2?

*Hint*: Is this an effective graph? Why or why not?

``` r
diamonds %>%
  ggplot(aes(cut, price)) +
  geom_point()
```

![](d04-e-stat00-eda-basics-assignment_files/figure-gfm/q2.1-task-1.png)<!-- -->

**Observations**: - Write your observations here\!

The following is a set of *boxplots*; the middle bar denotes the median,
the boxes denote the *quartiles* (upper and lower “quarters” of the
data), and the lines and dots denote large values and outliers.

**q2.2** Study the following graph; does it support or contradict
hypothesis 2?

``` r
diamonds %>%
  ggplot(aes(cut, price)) +
  geom_boxplot()
```

![](d04-e-stat00-eda-basics-assignment_files/figure-gfm/q2.2-task-1.png)<!-- -->

**Observations**: - Write your observations here\!

### Unraveling Hypothesis 2

Upon making the graph in **q2.2**, I was very surprised. So I did some
reading on diamond cuts. It turns out that some gemcutters [sacrifice
cut for carat](https://en.wikipedia.org/wiki/Diamond_\(gemstone\)#Cut).
Could this effect explain the surprising pattern above?

**q3** Study the following graph; does it support a “carat over cut”
hypothesis? How might this relate to price?

*Hint*: The article linked above will help you answer these questions\!

``` r
diamonds %>%
  ggplot(aes(cut, carat)) +
  geom_boxplot()
```

![](d04-e-stat00-eda-basics-assignment_files/figure-gfm/q2.3-task-1.png)<!-- -->

**Observations**: - Write your observations here\!

### Footnotes

\[1\] Don’t mistake my focus on `diamonds` as an endorsement of the
diamond industry. In my opinion aesthetic diamonds are a morally dubious
scam.

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-stat00-eda-basics-assignment.Rmd).
