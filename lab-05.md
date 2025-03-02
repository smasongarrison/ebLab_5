Lab 05 - La Quinta is Spanish for next to Denny’s, Pt. 2
================
Benjamin Egan
02-12-25

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
    y = "Distance to a La Quinta in km"
  )
```

![](lab-05_files/figure-gfm/comparison%20for%20Alaska-1.png)<!-- -->

``` r
mean(dn_lq_ak_mindist$closest)
```

    ## [1] 4.41

This graph shows us that the closest La Quinta to a Denny’s is only
about 2 km away. The farthest La Quinta is only about 6 km away. I also
calculated the average distance of a Denny’s to a La Quinta to be 4.41
km.

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
    y = "Distance to a La Quinta in km"
  )+
  ggtitle(title_nc)+
  theme(plot.title = element_text(size=12.5))
```

![](lab-05_files/figure-gfm/comparison%20for%20North%20Carolina-1.png)<!-- -->

``` r
mean(dn_lq_nc_mindist$closest)
```

    ## [1] 65.44286

This graph shows us that the same trend does not hold in North Carolina.
The average distance from a Denny’s to a La Quinta is 65.44 km, with
only four locations under 10 km.

### Texas

``` r
title_tx <- "Minimum distance from a Denny's to a La Quinta in Texas"
dn_lq_tx_mindist %>%
  ggplot(mapping = aes(
   x = closest
  )) +
  theme_bw()+
    geom_histogram(binwidth = 1,fill = "#6a4e93") +
  labs(
    x = "Distance to a La Quinta in km",
    y = NULL
  )+
  ggtitle(title_tx)+
  theme(plot.title = element_text(size=14))
```

![](lab-05_files/figure-gfm/comparison%20for%20Texas-1.png)<!-- -->

``` r
mean(dn_lq_tx_mindist$closest)
```

    ## [1] 11.34865

``` r
sd(dn_lq_tx_mindist$closest)
```

    ## [1] 20.52603

``` r
dn_lq_tx_mindist%>%
count(close)
```

    ## # A tibble: 2 × 2
    ##   close           n
    ##   <chr>       <int>
    ## 1 Less than 5   242
    ## 2 More than 5   194

The average distance from a Denny’s to a La Quinta is around 5.7 km,
with the standard deviation being 8.83 km. By creating a variable that
distinguishes distance, we can see that 126 La Quinta’s are less than 5
km away from a Denny’s, while 74 of them are over 5 km away.

### Georgia

Since I am from Atlanta, I decided to pick Georgia.

``` r
title_ga <- "Minimum distance from a Denny's to a La Quinta in Georgia"

dn_lq_ga_mindist %>%
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
    y = "Distance to a La Quinta in km"
  )+
  ggtitle(title_ga)+
  theme(plot.title = element_text(size=12.5))
```

![](lab-05_files/figure-gfm/comparison%20for%20Georgia-1.png)<!-- -->

``` r
mean(dn_lq_ga_mindist$closest)
```

    ## [1] 14.045

``` r
sd(dn_lq_ga_mindist$closest)
```

    ## [1] 20.62042

In Georgia, mean is 14.05 km and the standard deviation is 20.62 km. You
can see that the majority of Denny’s have a La Quinta less than 20 km
away.

### Final Question

I’m guessing this holds up the best in Texas. I didn’t look at other
states with high numbers of Denny’s/La Quintas (i.e. California), but
Texas has a ton of low distances based on the datasets.

## Visual by geographic location

I also decided to graph these relationships geographically, using
longitude and latitude.

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
together, indicating Denny’s are always close to La Quintas.

### North Carolina

``` r
NC_dataset %>%
  ggplot( mapping = aes(
    x = longitude,
    y = latitude,
    color = establishment
  )) +
  theme_bw()+
    geom_point(alpha = .6) +
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
increase in locations make it harder to understand which La Quinta each
Denny’s is going to, but it does have advantages. One primary advantage
is understanding why there are a bunch of triple digit distances. You
can see that the bottom right corner of the state has several Denny’s
without a La Quinta.

### Texas

``` r
anti_join(all_together, dn_lq_tx_mindist, by = "address") %>%
  select(address)
```

    ## # A tibble: 0 × 1
    ## # ℹ 1 variable: address <chr>

``` r
TX_dataset %>%
  ggplot( mapping = aes(
    x = longitude,
    y = latitude,
    color = establishment
  )) +
  theme_bw()+
    geom_point(alpha = .6) +
 scale_color_manual(values=c('Blue','Red'))+
geom_text_repel(aes(x = longitude, 
              y = latitude, 
              label = closest),
          size = 3, color = "black") +
  labs(
    title = "Minimum distance from a Denny's to a La Quinta in Texas",
    x = "Longitude",
    y = "Latitude"
  )
```

![](lab-05_files/figure-gfm/geographic%20for%20Texas-1.png)<!-- -->

This is a very messy plot and something I would absolutely not use. I am
concerned about the datapoints on the far left of the graph not showing
distances.

### Georgia

``` r
GA_dataset %>%
  ggplot( mapping = aes(
    x = longitude,
    y = latitude,
    color = establishment
  )) +
  theme_bw()+
    geom_point(alpha = .6) +
 scale_color_manual(values=c('Blue','Red'))+
geom_text_repel(aes(x = longitude, 
              y = latitude, 
              label = closest),
          size = 3, color = "black") +
  labs(
    title = "Minimum distance from a Denny's to a La Quinta in Georgia",
    x = "Longitude",
    y = "Latitude"
  )
```

![](lab-05_files/figure-gfm/geographic%20for%20Georgia-1.png)<!-- -->

I’ll admit this looks nothing like Georgia, and some of the data points
written don’t appear to be as far as they seem. But it’s a cool visual.
