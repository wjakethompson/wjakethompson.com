---
title: "Rethinking Congressional Apportionment"
subtitle: ""
excerpt: "Make yourself a ggplot2-themed clock, using ggplot2!"
date: 2022-01-04
author: "Jake Thompson"
draft: false
categories:
  - R
tags:
  - ggplot2
  - politics
layout: single
---

Somehow we are already in another election year, with midterm elections coming up in November.
These the midterms are special this year because they are the first elections after the 2020 census, with the new house apportionment.
Inspired by

{{% tweet "1443684142592536579" %}}

``` r
all_seats %>% 
  pivot_longer(ends_with("seats"), names_to = "method", values_to = "seats",
               names_pattern = "(.*)_seats") %>% 
  group_by(year, method) %>% 
  mutate(prop_seat = seats / sum(seats),
         people_per_seat = population / seats)
#> # A tibble: 450 × 9
#> # Groups:   year, method [9]
#>     year geoid state state_name population method seats prop_seat
#>    <dbl> <chr> <chr> <chr>           <dbl> <chr>  <int>     <dbl>
#>  1  2000 01    AL    Alabama       4447100 cubic      9   0.0162 
#>  2  2000 01    AL    Alabama       4447100 wy         9   0.0165 
#>  3  2000 01    AL    Alabama       4447100 real       7   0.0161 
#>  4  2000 02    AK    Alaska         626932 cubic      1   0.00180
#>  5  2000 02    AK    Alaska         626932 wy         1   0.00184
#>  6  2000 02    AK    Alaska         626932 real       1   0.00230
#>  7  2000 04    AZ    Arizona       5130632 cubic     10   0.0180 
#>  8  2000 04    AZ    Arizona       5130632 wy        10   0.0184 
#>  9  2000 04    AZ    Arizona       5130632 real       8   0.0184 
#> 10  2000 05    AR    Arkansas      2673400 cubic      5   0.00901
#> # … with 440 more rows, and 1 more variable: people_per_seat <dbl>
```
