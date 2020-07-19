Statistics: Probability
================
Zach del Rosario
2020-06-08

*Purpose*: *Probability* is a quantitative measure of uncertainty. It is
intimately tied to how we use distributions to model data and to how we
express uncertainty. In order to do all these useful things, we’ll need
to learn some basics about probability.

*Reading*: (None; this exercise *is* the reading.)

*Topics*: Frequency, probability, probability density function (PDF),
cumulative distribution function (CDF)

“Probability is the most important concept in modern science, especially
as nobody has the slightest notion what it means.” — Bertrand Russell

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

# Intuitive Definition

<!-- -------------------------------------------------- -->

In the previous stats exercise, we learned about *distributions*. In
this exercise, we’re going to learn a more formal definition of
distributions using probability.

To introduce the notion of probability, let’s first think about
*frequency*, defined as

\[F_X(A) \equiv \sum \frac{\text{Cases in set }A}{\text{Total Cases in }X}.\]

Note that this frequency considers both a *set* \(A\) and a sample
\(X\). We need to define both \(A, X\) in order to compute a frequency.

As an example, let’s consider the set \(A\) to be the set of
\(-1.96 <= z <= +1.96\): We denote this set as
\(A = {z | -1.96 <= z <= +1.96}\). Let’s also let \(X\) be a sample from
a standard (`mean = 0, sd = 1`) normal distribution. The following
figure illustrates the set \(A\) against a standard normal distribution.

``` r
## NOTE: No need to change this!
tibble(z = seq(-3, +3, length.out = 500)) %>%
  mutate(d = dnorm(z)) %>%
  ggplot(aes(z, d)) +
  geom_ribbon(
    data = . %>% filter(-1.96 <= z, z <= +1.96),
    aes(ymin = 0, ymax = d, fill = "Set A"),
    alpha = 1 / 3
  ) +
  geom_line() +
  scale_fill_discrete(name = "")
```

![](d12-e-stat02-probability-assignment_files/figure-gfm/set-vis-1.png)<!-- -->

Note that a frequency is defined *not* in terms of a distribution, but
rather in terms of a sample. The following example code draws a sample
from a standard normal, and computes the frequency with which values in
the sample \(X\) lie in the set \(A\).

``` r
## NOTE: No need to change this!
df_z <- tibble(z = rnorm(100))

df_z %>%
  mutate(in_A = (-1.96 <= z) & (z <= +1.96)) %>%
  summarize(count_total = n(), count_A = sum(in_A), fr = mean(in_A))
```

    ## # A tibble: 1 x 3
    ##   count_total count_A    fr
    ##         <int>   <int> <dbl>
    ## 1         100      97  0.97

Now it’s your turn\!

**q1** Let \(A = {z | z <= 0}\). Complete the following code to compute
`count_total`, `count_A`, and `fr`. Before executing the code, **make a
prediction** about the value of `fr`. Did the computed `fr` value match
your prediction?

``` r
## NOTE: No need to change this!
df_z <- tibble(z = rnorm(100))

## TODO: Compute `count_total`, `count_A`, and `fr`
```

**Observations**:

  - What value of `fr` did you predict?
  - Did your prediction match your calculation?

The following graph visualizes the set \(A = {z | z <= 0}\) against a
standard normal distribution.

``` r
## NOTE: No need to change this!
tibble(z = seq(-3, +3, length.out = 500)) %>%
  mutate(d = dnorm(z)) %>%
  ggplot(aes(z, d)) +
  geom_ribbon(
    data = . %>% filter(z <= 0),
    aes(ymin = 0, ymax = d, fill = "Set A"),
    alpha = 1 / 3
  ) +
  geom_line() +
  scale_fill_discrete(name = "")
```

![](d12-e-stat02-probability-assignment_files/figure-gfm/q1-vis-1.png)<!-- -->

Based on this visual, we might expect `fr = 0.5`. This was (likely) not
the value that our *frequency* took, but it is the precise value of the
*probability* that \(Z <= 0.5\).

Remember in the previous stats exercise that we studied how larger
values of `n` led to a histogram closer approaching a distribution?
Something very similar happens with frequency and probability:

``` r
## NOTE: No need to change this!
map_dfr(
  c(10, 100, 1000, 1e4),
  function(n_samples) {
    tibble(
      z = rnorm(n = n_samples),
      n = n_samples
    ) %>%
      mutate(in_A = (z <= 0)) %>%
      summarize(count_total = n(), count_A = sum(in_A), fr = mean(in_A))
  }
)
```

    ## # A tibble: 4 x 3
    ##   count_total count_A    fr
    ##         <int>   <int> <dbl>
    ## 1          10       8 0.8  
    ## 2         100      50 0.5  
    ## 3        1000     509 0.509
    ## 4       10000    4980 0.498

This is because *probability* is actually defined\[1\] in terms of the
limit

\[\mathbb{P}_{\rho}[X \in A] = \lim_{n \to \infty} F_{X_n}(A),\]

where \(X_n\) is a sample of size \(n\) drawn from the distribution
\(\rho\).\[2\]

**q2**: Modify the code below to consider the set
\(A = {z | -1.96 <= z <= +1.96}\). What value does `fr` appear to be
limiting towards?

``` r
## TASK: Modify the code below
      #summarize(count_total = n(), count_A = sum(in_A), fr = mean(in_A))
  #}
#)
```

**Observations**:

  - What value does `fr` appear to be limiting towards?

Now that we know what probability is; let’s connect the idea to
distributions.

# Relation to Distributions

<!-- -------------------------------------------------- -->

A continuous distribution *models* probability in terms of an integral

\[\mathbb{P}_{\rho}[X \in A] = \int_{-\infty}^{+\infty} 1_A(x)\rho(x)\,dx\]

where \(1_A(x)\) is the *indicator function*; a function that takes the
value \(1\) when \(x \in A\), and the value \(0\) when \(x \not\in A\).

This definition gives us a geometric way to think about probability; the
distribution definition means probability is the area under the curve
within the set \(A\).

Before concluding this reading, let’s talk about two sticking points
about distributions:

## Sets vs Points

<!-- ------------------------- -->

Note that, for continuous distributions, the probability of a single
point is *zero*. First, let’s gather some empirical evidence.

**q3** Modify the code below to consider the set \(A = {z | z = 2}\).

*Hint*: Remember the difference between `=` and `==`\!

``` r
## TASK: Modify the code below
      #summarize(count_total = n(), count_A = sum(in_A), fr = mean(in_A))
  #}
#)
```

**Observations**:

  - What value is `fr` approaching?

We can also understand this phenomenon in terms of areas; the following
graph visualizes the set \(A = {z | z = 2}\) against a standard normal.

``` r
## NOTE: No need to change this!
tibble(z = seq(-3, +3, length.out = 500)) %>%
  mutate(d = dnorm(z)) %>%
  ggplot(aes(z, d)) +
  geom_segment(
    data = tibble(z = 2, d = dnorm(2)),
    mapping = aes(z, 0, xend = 2, yend = d, color = "Set A")
  ) +
  geom_line() +
  scale_color_discrete(name = "")
```

![](d12-e-stat02-probability-assignment_files/figure-gfm/q3-vis-1.png)<!-- -->

Note that this set \(A\) has nonzero height but zero width. Zero width
corresponds to zero area, and thus zero probability.

*This is weird*. If we’re using a distribution to model something
physical, say a material property, this means that any specific material
property has zero probability of occuring. But in practice, some
specific value will be realized\! If you’d like to learn more, take a
look at Note \[3\].

## Two expressions of the same information

<!-- ------------------------- -->

There is a bit more terminology associated with distributions. The
\(\rho(x)\) we considered above is called a [probability density
function](https://en.wikipedia.org/wiki/Probability_density_function)
(PDF); it is the function we integrate in order to obtain a probability.
In R, the PDF has the `d` prefix, for instance `dnorm`. For example, the
standard normal has the following PDF.

``` r
tibble(z = seq(-3, +3, length.out = 1000)) %>%
  mutate(d = dnorm(z)) %>%

  ggplot(aes(z, d)) +
  geom_line() +
  labs(
    x = "z",
    y = "Likelihood",
    title = "Probability Density Function"
  )
```

![](d12-e-stat02-probability-assignment_files/figure-gfm/vis-pdf-1.png)<!-- -->

There is also a [cumulative distribution
function](https://en.wikipedia.org/wiki/Cumulative_distribution_function),
which is the indefinite integral of the PDF:

\[R(x) = \int_{-\infty}^x \rho(s) ds.\]

In R, the CDF has the prefix `p`, such as `pnorm`. For example, the
standard normal has the following CDF.

``` r
tibble(z = seq(-3, +3, length.out = 1000)) %>%
  mutate(p = pnorm(z)) %>%

  ggplot(aes(z, p)) +
  geom_line() +
  labs(
    x = "z",
    y = "Probability",
    title = "Cumulative Density Function"
  )
```

![](d12-e-stat02-probability-assignment_files/figure-gfm/vis-cdf-1.png)<!-- -->

Note that, by definition, the CDF gives the probability over the set
\(A = {x | x <= 0}\). Thus the CDF returns a probability (which explains
the `p` prefix for R functions).

**q4** Use `pnorm` to compute the probability that `Z ~ norm(mean = 0,
sd = 1)` is less than or equal to zero. Compare this against your
frequency prediction from **q1**.

``` r
## TASK: Compute the probability that Z <= 0, assign to p0
p0 <- NA_real_
p0
```

    ## [1] NA

Use the following code to check your answer.

``` r
## NOTE: No need to change this
#assertthat::assert_that(p0 == 0.5)
#print("Nice!")
```

**Observations**:

  - What was your `fr` prediction in **q1**? How does `p0` compare?

Note that when our set \(A\) is an *interval*, we can use the CDF to
express the associated probability. Note that

\[\mathbb{P}_{\rho}[a <= X <= b] = \int_a^b \rho(x) dx = \int_{-\infty} \rho(x) dx - \int_{-\infty}^a \rho(x) dx.\]

**q5** Using the identity above, use `pnorm` to compute the probability
that \(-1.96 <= Z <= +1.96\) with `Z ~ norm(mean = 0, sd = 1)`.

``` r
## TASK: Compute the probability that -1.96 <= Z <= +1.96, assign to pI
pI <- NA_real_
pI
```

    ## [1] NA

Use the following code to check your answer.

``` r
## NOTE: No need to change this
#assertthat::assert_that(abs(pI - 0.95) < 1e-3)
#print("Nice!")
```

<!-- include-exit-ticket -->

# Exit Ticket

<!-- -------------------------------------------------- -->

Once you have completed this exercise, make sure to fill out the **exit
ticket survey**, [linked
here](https://docs.google.com/forms/d/e/1FAIpQLSeuq2LFIwWcm05e8-JU84A3irdEL7JkXhMq5Xtoalib36LFHw/viewform?usp=pp_url&entry.693978880=e-stat02-probability-assignment.Rmd).

# Notes

<!-- -------------------------------------------------- -->

\[1\] This is where things get confusing and potentially controversial.
This “limit of frequencies” definition of probability is called
“Frequentist probability”, to distinguish it from a “Bayesian
probability”. The distinction is meaningful but slippery. We won’t cover
this distinction in this course. If you’re curious to learn more, my
favorite video on Bayes vs Frequentist is by [Kristin
Lennox](https://www.youtube.com/watch?v=eDMGDhyDxuY&list=WL&index=131&t=0s).

Note that even Bayesians use this Frequentist definition of probability,
for instance in [Markov Chain Monte
Carlo](https://en.wikipedia.org/wiki/Markov_chain_Monte_Carlo), the
workhorse of Bayesian computation.

\[2\] Technically these samples must be drawn *independently and
identically distributed*, shortened to iid.

\[3\] [3Blue1Brown](https://www.youtube.com/watch?v=8idr1WZ1A7Q) has a
very nice video about continuous probabilities.