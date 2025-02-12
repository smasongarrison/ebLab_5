Lab 05 - La Quinta is Spanish for next to Denny’s, Pt. 2
================
Benjamin Egan
02-12-25

### Load packages and data

``` r
library(tidyverse) 
library(dsbox) 
```

``` r
states <- read_csv("data/states.csv")
```

### Exercise 1

``` r
dn_ak <- dennys %>%
  filter(state == "AK")
nrow(dn_ak)
```

    ## [1] 3

``` r
lq_ak <- laquinta %>%
  filter(state == "AK")
nrow(lq_ak)
```

    ## [1] 2

There are 3 Denny’s and 2 La Quintas in Alaska.

### Exercise 2

``` r
nrow(lq_ak) *nrow(dn_ak)
```

    ## [1] 6

Since there are 3 Denny’s and 2 La Quintas in Alaska, we need 6 total
distances. We can combine the two data frames to aid in the calcuations.

``` r
dn_lq_ak <- full_join(dn_ak, lq_ak, 
                      by = "state")

dn_lq_ak
```

    ## # A tibble: 6 × 11
    ##   address.x     city.x state zip.x longitude.x latitude.x address.y city.y zip.y
    ##   <chr>         <chr>  <chr> <chr>       <dbl>      <dbl> <chr>     <chr>  <chr>
    ## 1 2900 Denali   Ancho… AK    99503       -150.       61.2 3501 Min… "\nAn… 99503
    ## 2 2900 Denali   Ancho… AK    99503       -150.       61.2 4920 Dal… "\nFa… 99709
    ## 3 3850 Debarr … Ancho… AK    99508       -150.       61.2 3501 Min… "\nAn… 99503
    ## 4 3850 Debarr … Ancho… AK    99508       -150.       61.2 4920 Dal… "\nFa… 99709
    ## 5 1929 Airport… Fairb… AK    99701       -148.       64.8 3501 Min… "\nAn… 99503
    ## 6 1929 Airport… Fairb… AK    99701       -148.       64.8 4920 Dal… "\nFa… 99709
    ## # ℹ 2 more variables: longitude.y <dbl>, latitude.y <dbl>

``` r
view(dn_lq_ak)
```

Once we combine the data frames, the dn_lq_ak data frame contains 6
rows, one row per combination. Each variable has an .x or .y designation
to differentiate between if it is for a Denny’s or a La Quinta. We can
use mutate() to add a variable for distance.

### Exercise 3

\`\`\` \### Exercise 4

…

### Exercise 5

…

### Exercise 6

…

Add exercise headings as needed.
