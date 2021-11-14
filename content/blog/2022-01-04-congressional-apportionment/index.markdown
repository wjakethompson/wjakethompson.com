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

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

Somehow we are already in another election year, with midterm elections coming up in November.
These the midterms are special this year because they are the first elections after the 2020 census, with the new house apportionment.
Congressional apportionment is incredibly important.
It determines not only how many representatives each state gets in the House of Representatives, but also the number of electoral votes each state gets for presidential elections.

Inspired by [Dr. Andrew Heiss](https://twitter.com/andrewheiss), I thought I would look at some alternative methods to apportioning seats in congress and how that might have impacted past elections.

{{% tweet "1443684142592536579" %}}

## Apportionment methods

In Dr. Heiss’s original post, he examined Joseph Smith’s 1844 proposal that each states get one representative for each 1 million citizens.
In the modern day, there are two major proposals for reform that we’ll examine: the *Cube root rule* and the *Wyoming rule*.
Both of these are discussed in detail below, but we’ll start with get the data.

First, we will get data on the actual number of seats appropriated to each state following the 2000, 2010, and 2020 census.
This code comes directly from [Dr. Heiss](https://gist.github.com/andrewheiss/5f89847f617eb825a08de6b02a053188).

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["Alaska","Alabama","Arkansas","Arizona","California","Colorado","Connecticut","Delaware","Florida","Georgia","Hawaii","Iowa","Idaho","Illinois","Indiana","Kansas","Kentucky","Louisiana","Massachusetts","Maryland","Maine","Michigan","Minnesota","Missouri","Mississippi","Montana","North Carolina","North Dakota","Nebraska","New Hampshire","New Jersey","New Mexico","Nevada","New York","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Virginia","Vermont","Washington","Wisconsin","West Virginia","Wyoming"],[1,7,4,8,53,7,5,1,25,13,2,5,2,19,9,4,6,7,10,8,2,15,8,9,4,1,13,1,3,2,13,3,3,29,18,5,5,19,2,6,1,9,32,3,11,1,9,8,3,1],[1,7,4,9,53,7,5,1,27,14,2,4,2,18,9,4,6,6,9,8,2,14,8,8,4,1,13,1,3,2,12,3,4,27,16,5,5,18,2,7,1,9,36,4,11,1,10,8,3,1],[1,7,4,9,52,8,5,1,28,14,2,4,2,17,9,4,6,6,9,8,2,13,8,8,4,2,14,1,3,2,12,3,4,26,15,5,6,17,2,7,1,9,38,4,11,1,10,8,2,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>State<\/th>\n      <th>2000<\/th>\n      <th>2010<\/th>\n      <th>2020<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"autoWidth":true,"columnDefs":[{"className":"dt-left","targets":0},{"width":"40%","targets":0},{"width":"20%","targets":[1,2,3]},{"className":"dt-right","targets":[1,2,3]}],"pageLength":10,"lengthMenu":[5,10,25,50],"order":[],"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

### Current method

### Cube root rule

### Wyoming rule

## Effects of different apportionments

### 2000 census

### 2010 census

### 2020 census

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
