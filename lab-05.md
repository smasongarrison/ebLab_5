Lab 05 - La Quinta is Spanish for next to Denny’s, Pt. 2
================
Benjamin Egan
02-12-25

### Load packages and data

``` r
library(ggrepel)
```

    ## Warning: package 'ggrepel' was built under R version 4.3.3

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
view(dn_lq_ak)
```

Once we combine the data frames, the dn_lq_ak data frame contains 6
rows, one row per combination. Each variable has an .x or .y designation
to differentiate between if it is for a Denny’s or a La Quinta. We can
use mutate() to add a variable for distance.

### Exercise 3

``` r
dn_lq_ak <- 
  dn_lq_ak %>%
  mutate(
    distance = haversine(longitude.x, latitude.x, longitude.y, latitude.y, 2)
  )

view(dn_lq_ak)
```

### Exercise 4

``` r
dn_lq_ak_mindist <- dn_lq_ak %>%
  group_by(address.x) %>%
  summarize(closest = min(distance))

dn_lq_ak_mindist <- dn_lq_ak_mindist %>%
  rename(address = address.x)

view(dn_lq_ak_mindist)
```

    ## Warning in full_join(dn_nc, lq_nc, by = "state"): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 1 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

## Graphing The Distance

### Alaska

``` r
dn_lq_ak_mindist %>%
  ggplot(mapping = aes(
    x = fct_reorder(address, closest),
   y = closest
  )) +
  theme_bw()+
  
    geom_col(fill = "#6a4e93") +
  labs(
    title = "Minimum distance from a Denny's to a La Quinta in Alaska",
    x = "Address of a Denny's",
    y = "Distance to a La Qinta"
  )
```

![](lab-05_files/figure-gfm/comparison%20for%20Alaska-1.png)<!-- -->

``` r
mean(dn_lq_ak_mindist$closest)
```

    ## [1] 4.41

This graph shows us that the closest La Qinta to a Denny’s is only about
2 somethings away (I don’t really know what units we’re supposed to use
here). The farthest La Qinta is only about 6 somethings away.

### North Carolina

``` r
title_nc <- "Minimum distance from a Denny's to a La Quinta in North Carolina"

dn_lq_nc_mindist %>%
  ggplot(mapping = aes(
    x = fct_rev(fct_reorder(address, closest)),
   y = closest
  )) +
  theme_bw()+
  coord_flip()+
  geom_text(aes(x = address, 
              y = closest, 
              label = closest),
          size = 3.7, color = "black") +
    geom_col(alpha = .4, fill = "#6a4e93") +
  labs(
    x = "Address of a Denny's",
    y = "Distance to a La Qinta"
  )+
  ggtitle(title_nc)+
  theme(plot.title = element_text(size=12.5))
```

![](lab-05_files/figure-gfm/comparison%20for%20North%20Carolina-1.png)<!-- -->

This graph shows us that the same trend does not hold

…

## Visual by geographic location

### Alaska

``` r
AK_dataset %>%
  ggplot( mapping = aes(
    x = longitude,
    y = latitude,
    color = establishment
  )) +
  theme_bw()+
    geom_point() +
 scale_color_manual(values=c('Blue','Red'))+
geom_text_repel(aes(x = longitude, 
              y = latitude, 
              label = closest),
          size = 3, color = "black") +
  labs(
    title = "Minimum distance from a Denny's to a La Quinta in Alaska",
    x = "longitude",
    y = "latitude"
  )
```

    ## Warning: Removed 2 rows containing missing values or values outside the scale range
    ## (`geom_text_repel()`).

![](lab-05_files/figure-gfm/geographic%20for%20Alaska-1.png)<!-- -->

This map is a geographical representation of the distance between
Denny’s and La Quinta. You can tell that they are tightly clustered
together, indicating Denny’s are always close to La Qintas.

### North Carolina

``` r
NC_dataset %>%
  ggplot( mapping = aes(
    x = longitude,
    y = latitude,
    color = establishment
  )) +
  theme_bw()+
    geom_point() +
 scale_color_manual(values=c('Blue','Red'))+
geom_text_repel(aes(x = longitude, 
              y = latitude, 
              label = closest),
          size = 3, color = "black") +
  labs(
    title = "Minimum distance from a Denny's to a La Quinta in North Carolina",
    x = "Longitude",
    y = "Latitude"
  )
```

![](lab-05_files/figure-gfm/geographic%20for%20North%20Carolina-1.png)<!-- -->

This graph is more messy and less helpful than the Alaska map. The
increase in locations make it harder to understand which La Qinta each
Denny’s is going to.

### Exercise 5

…

### Exercise 6

…

Add exercise headings as needed.
