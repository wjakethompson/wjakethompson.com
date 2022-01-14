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
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>
<link href="{{< blogdown/postref >}}index_files/datatables-css/datatables-crosstalk.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/datatables-binding/datatables.js"></script>
<script src="{{< blogdown/postref >}}index_files/jquery/jquery-3.6.0.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.min.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/dt-core/css/jquery.dataTables.extra.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/dt-core/js/jquery.dataTables.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>
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

``` r
library(tidyverse)
library(rvest)

raw_apportionment <- read_html(
  paste0("https://web.archive.org/web/20210930182255/", 
         "https://en.wikipedia.org/wiki/United_States_congressional_apportionment")
) %>%
  html_nodes(xpath = '/html/body/div[3]/div[3]/div[5]/div[1]/table[4]') %>%  
  html_table() %>% 
  bind_rows()

# Clean up wide messy table
congress_actual <- raw_apportionment %>% 
  slice(5:n()) %>% 
  select(-Statehoodorder, state = Census, everything()) %>% 
  mutate(across(!state, ~as.integer(.))) %>% 
  pivot_longer(cols = !state, names_to = "census", values_to = "seats") %>% 
  filter(!is.na(seats)) %>% 
  mutate(state_name = state.name[match(state, state.abb)]) %>% 
  filter(census %in% c("22nd", "23rd", "24th")) %>% 
  mutate(year = recode(census, `22nd` = 2000, `23rd` = 2010, `24th` = 2020)) %>% 
  select(census, year, state, state_name, seats) %>% 
  arrange(year, state)
```

<details>
<summary>
Code to reproduce
</summary>

``` r
library(DT)

real_dt <- congress_actual %>% 
  select(state_name, year, seats) %>% 
  pivot_wider(names_from = year, values_from = seats) %>% 
  datatable(rownames = FALSE,
            colnames = c("State", "2000", "2010", "2020"),
            options = list(
              autoWidth = TRUE,
              columnDefs = list(list(className = "dt-left", targets = 0),
                                list(width = "40%", targets = 0),
                                list(width = "20%", targets = 1:3)),
              pageLength = 10,
              lengthMenu = c(5, 10, 25, 50)
            ))
```

</details>

<br>

<div id="htmlwidget-1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"filter":"none","vertical":false,"data":[["Alaska","Alabama","Arkansas","Arizona","California","Colorado","Connecticut","Delaware","Florida","Georgia","Hawaii","Iowa","Idaho","Illinois","Indiana","Kansas","Kentucky","Louisiana","Massachusetts","Maryland","Maine","Michigan","Minnesota","Missouri","Mississippi","Montana","North Carolina","North Dakota","Nebraska","New Hampshire","New Jersey","New Mexico","Nevada","New York","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Virginia","Vermont","Washington","Wisconsin","West Virginia","Wyoming"],[1,7,4,8,53,7,5,1,25,13,2,5,2,19,9,4,6,7,10,8,2,15,8,9,4,1,13,1,3,2,13,3,3,29,18,5,5,19,2,6,1,9,32,3,11,1,9,8,3,1],[1,7,4,9,53,7,5,1,27,14,2,4,2,18,9,4,6,6,9,8,2,14,8,8,4,1,13,1,3,2,12,3,4,27,16,5,5,18,2,7,1,9,36,4,11,1,10,8,3,1],[1,7,4,9,52,8,5,1,28,14,2,4,2,17,9,4,6,6,9,8,2,13,8,8,4,2,14,1,3,2,12,3,4,26,15,5,6,17,2,7,1,9,38,4,11,1,10,8,2,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>State<\/th>\n      <th>2000<\/th>\n      <th>2010<\/th>\n      <th>2020<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"autoWidth":true,"columnDefs":[{"className":"dt-left","targets":0},{"width":"40%","targets":0},{"width":"20%","targets":[1,2,3]},{"className":"dt-right","targets":[1,2,3]}],"pageLength":10,"lengthMenu":[5,10,25,50],"order":[],"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

Next, in order to examine apportionment with alternate methods, we need state-level population data.
This is straightforward to get thanks to the great [tidycensus](https://github.com/walkerke/tidycensus) package from [Kyle Walker](https://twitter.com/kyle_e_walker).

``` r
library(tidycensus)

get_state_pop <- function(year) {
  var <- ifelse(year == 2020, "P1_001N", "P001001")
  
  get_decennial(geography = "state", variables = var, year = year) %>% 
    select(geoid = GEOID, state_name = NAME, population = value) %>% 
    mutate(year = year, .before = 1)
}

state_pop <- map_dfr(c(2000, 2010, 2020), get_state_pop) %>% 
  mutate(state = state.abb[match(state_name, state.name)], .before = state_name) %>% 
  mutate(state = case_when(state_name == "District of Columbia" ~ "DC",
                           state_name == "Puerto Rico" ~ "PR",
                           TRUE ~ state))
```

<details>
<summary>
Code to reproduce
</summary>

``` r
library(DT)

pop_dt <- state_pop %>% 
  select(state_name, year, population) %>% 
  mutate(population = prettyNum(population, big.mark = ",")) %>% 
  pivot_wider(names_from = year, values_from = population) %>% 
  datatable(rownames = FALSE,
            colnames = c("State", "2000", "2010", "2020"),
            options = list(
              autoWidth = TRUE,
              columnDefs = list(list(className = "dt-left", targets = 0),
                                list(className = "dt-right", targets = 1:3),
                                list(width = "40%", targets = 0),
                                list(width = "20%", targets = 1:3)),
              pageLength = 10,
              lengthMenu = c(5, 10, 25, 50)
            ))
```

</details>

<br>

<div id="htmlwidget-2" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"filter":"none","vertical":false,"data":[["Alabama","Alaska","Arizona","Arkansas","California","Colorado","Connecticut","Delaware","District of Columbia","Florida","Georgia","Hawaii","Idaho","Illinois","Indiana","Iowa","Kansas","Kentucky","Louisiana","Maine","Maryland","Massachusetts","Michigan","Minnesota","Mississippi","Missouri","Montana","Nebraska","Nevada","New Hampshire","New Jersey","New Mexico","New York","North Carolina","North Dakota","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Vermont","Virginia","Washington","West Virginia","Wisconsin","Wyoming","Puerto Rico"],["4,447,100","626,932","5,130,632","2,673,400","33,871,648","4,301,261","3,405,565","783,600","572,059","15,982,378","8,186,453","1,211,537","1,293,953","12,419,293","6,080,485","2,926,324","2,688,418","4,041,769","4,468,976","1,274,923","5,296,486","6,349,097","9,938,444","4,919,479","2,844,658","5,595,211","902,195","1,711,263","1,998,257","1,235,786","8,414,350","1,819,046","18,976,457","8,049,313","642,200","11,353,140","3,450,654","3,421,399","12,281,054","1,048,319","4,012,012","754,844","5,689,283","20,851,820","2,233,169","608,827","7,078,515","5,894,121","1,808,344","5,363,675","493,782","3,808,610"],["4,779,736","710,231","6,392,017","2,915,918","37,253,956","5,029,196","3,574,097","897,934","601,723","18,801,310","9,687,653","1,360,301","1,567,582","12,830,632","6,483,802","3,046,355","2,853,118","4,339,367","4,533,372","1,328,361","5,773,552","6,547,629","9,883,640","5,303,925","2,967,297","5,988,927","989,415","1,826,341","2,700,551","1,316,470","8,791,894","2,059,179","19,378,102","9,535,483","672,591","11,536,504","3,751,351","3,831,074","12,702,379","1,052,567","4,625,364","814,180","6,346,105","25,145,561","2,763,885","625,741","8,001,024","6,724,540","1,852,994","5,686,986","563,626","3,725,789"],["5,024,279","733,391","7,151,502","3,011,524","39,538,223","5,773,714","3,605,944","989,948","689,545","21,538,187","10,711,908","1,455,271","1,839,106","12,812,508","6,785,528","3,190,369","2,937,880","4,505,836","4,657,757","1,362,359","6,177,224","7,029,917","10,077,331","5,706,494","2,961,279","6,154,913","1,084,225","1,961,504","3,104,614","1,377,529","9,288,994","2,117,522","20,201,249","10,439,388","779,094","11,799,448","3,959,353","4,237,256","13,002,700","1,097,379","5,118,425","886,667","6,910,840","29,145,505","3,271,616","643,077","8,631,393","7,705,281","1,793,716","5,893,718","576,851","3,285,874"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>State<\/th>\n      <th>2000<\/th>\n      <th>2010<\/th>\n      <th>2020<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"autoWidth":true,"columnDefs":[{"className":"dt-left","targets":0},{"className":"dt-right","targets":[1,2,3]},{"width":"40%","targets":0},{"width":"20%","targets":[1,2,3]}],"pageLength":10,"lengthMenu":[5,10,25,50],"order":[],"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

That that we have all the data, let’s define our apportionment methods.

### Current method

Until 1921, the number of seats in the House of Representative increased every 10 years when census was completed.
The total number was raised to 435 in 1912, following the 1910 census.
However, congress failed to reapportion the House in 1921.
A compromise was struck in 1929 to cap the House membership at 435, which is where it has stayed ever since.
Because the number of representatives has stayed the same but population of the United States has increased the average number of constituents in each district has increased from approximately 210,000 in 1913 to over 760,000 in 2023 (following the 2022 midterms).

The 435 members are split across the 50 states using the [Huntington-Hill method](https://en.wikipedia.org/wiki/Huntington%E2%80%93Hill_method).
This method minimizes the percentage differences in the number of constituents per representative.
It works by assigning a “priority” number for each seat, based on each state’s population and the current number of seats allocated to each state.

To illustrate, let’s look at the 2020 census data.
We start by assigning each state a single seat to ensure that every state has at least one representative.

``` r
seats2020 <- state_pop %>% 
  filter(!state_name %in% c("District of Columbia", "Puerto Rico"),
         year == 2020) %>% 
  mutate(seats = 1)

seats2020
#> # A tibble: 50 × 6
#>     year geoid state state_name  population seats
#>    <dbl> <chr> <chr> <chr>            <dbl> <dbl>
#>  1  2020 01    AL    Alabama        5024279     1
#>  2  2020 02    AK    Alaska          733391     1
#>  3  2020 04    AZ    Arizona        7151502     1
#>  4  2020 05    AR    Arkansas       3011524     1
#>  5  2020 06    CA    California    39538223     1
#>  6  2020 08    CO    Colorado       5773714     1
#>  7  2020 09    CT    Connecticut    3605944     1
#>  8  2020 10    DE    Delaware        989948     1
#>  9  2020 16    ID    Idaho          1839106     1
#> 10  2020 12    FL    Florida       21538187     1
#> # … with 40 more rows
```

We can then calculate the priority number for each state, `\(A_s\)`, as

$$
A_{{s}}={\frac  {P_s}{{\sqrt  {n_s(n_s+1)}}}}
$$
where `\(P_s\)` is the state’s population and `\(n_s\)` is the number of seats currently allocated the state.

``` r
seats2020 %>% 
  mutate(priority = population / sqrt(seats * seats + 1)) %>% 
  arrange(desc(priority))
#> # A tibble: 50 × 7
#>     year geoid state state_name     population seats  priority
#>    <dbl> <chr> <chr> <chr>               <dbl> <dbl>     <dbl>
#>  1  2020 06    CA    California       39538223     1 27957746.
#>  2  2020 48    TX    Texas            29145505     1 20608984.
#>  3  2020 12    FL    Florida          21538187     1 15229798.
#>  4  2020 36    NY    New York         20201249     1 14284440.
#>  5  2020 42    PA    Pennsylvania     13002700     1  9194297.
#>  6  2020 17    IL    Illinois         12812508     1  9059811.
#>  7  2020 39    OH    Ohio             11799448     1  8343470.
#>  8  2020 13    GA    Georgia          10711908     1  7574463.
#>  9  2020 37    NC    North Carolina   10439388     1  7381762.
#> 10  2020 26    MI    Michigan         10077331     1  7125749.
#> # … with 40 more rows
```

California has the highest priority number, so they receive the next seat.

``` r
seats2020 <- seats2020 %>%
  mutate(priority = population / sqrt(seats * seats + 1),
         seats = case_when(priority == max(priority) ~ seats + 1,
                           TRUE ~ seats))

seats2020
#> # A tibble: 50 × 7
#>     year geoid state state_name  population seats  priority
#>    <dbl> <chr> <chr> <chr>            <dbl> <dbl>     <dbl>
#>  1  2020 01    AL    Alabama        5024279     1  3552702.
#>  2  2020 02    AK    Alaska          733391     1   518586.
#>  3  2020 04    AZ    Arizona        7151502     1  5056876.
#>  4  2020 05    AR    Arkansas       3011524     1  2129469.
#>  5  2020 06    CA    California    39538223     2 27957746.
#>  6  2020 08    CO    Colorado       5773714     1  4082632.
#>  7  2020 09    CT    Connecticut    3605944     1  2549787.
#>  8  2020 10    DE    Delaware        989948     1   699999.
#>  9  2020 16    ID    Idaho          1839106     1  1300444.
#> 10  2020 12    FL    Florida       21538187     1 15229798.
#> # … with 40 more rows
```

We then repeat the process by calculating a new set of priority numbers.

``` r
seats2020 %>% 
  mutate(priority = population / sqrt(seats * seats + 1)) %>% 
  arrange(desc(priority))
#> # A tibble: 50 × 7
#>     year geoid state state_name     population seats  priority
#>    <dbl> <chr> <chr> <chr>               <dbl> <dbl>     <dbl>
#>  1  2020 48    TX    Texas            29145505     1 20608984.
#>  2  2020 06    CA    California       39538223     2 17682031.
#>  3  2020 12    FL    Florida          21538187     1 15229798.
#>  4  2020 36    NY    New York         20201249     1 14284440.
#>  5  2020 42    PA    Pennsylvania     13002700     1  9194297.
#>  6  2020 17    IL    Illinois         12812508     1  9059811.
#>  7  2020 39    OH    Ohio             11799448     1  8343470.
#>  8  2020 13    GA    Georgia          10711908     1  7574463.
#>  9  2020 37    NC    North Carolina   10439388     1  7381762.
#> 10  2020 26    MI    Michigan         10077331     1  7125749.
#> # … with 40 more rows
```

Now Texas has the highest priority number, so they receive the next seat.
This process repeats over and over again until the total number of seats reaches 435.
To automate this process, we can write a function, `hunt_hill()` that will data a data frame of population data and the total number of seats and return the same data frame with the number of allocated seats.

``` r
hunt_hill <- function(year_dat, total = 435) {
  year_dat <- mutate(year_dat, seats = 1L)
  
  while (sum(year_dat$seats) < total) {
    year_dat <- year_dat %>% 
      mutate(priority = population / sqrt(seats * (seats + 1)),
             seats = case_when(priority == max(priority) ~ seats + 1L,
                               TRUE ~ seats)) %>% 
      select(-priority)
  }
  
  return(year_dat)
}
```

To confirm, let’s run the 2020 census through this function, and then compared the results the real allocation.

``` r
seats2020 <- state_pop %>% 
  filter(!state_name %in% c("District of Columbia", "Puerto Rico"),
         year == 2020) %>% 
  hunt_hill(total = 435)

seats2020
#> # A tibble: 50 × 6
#>     year geoid state state_name  population seats
#>    <dbl> <chr> <chr> <chr>            <dbl> <int>
#>  1  2020 01    AL    Alabama        5024279     7
#>  2  2020 02    AK    Alaska          733391     1
#>  3  2020 04    AZ    Arizona        7151502     9
#>  4  2020 05    AR    Arkansas       3011524     4
#>  5  2020 06    CA    California    39538223    52
#>  6  2020 08    CO    Colorado       5773714     8
#>  7  2020 09    CT    Connecticut    3605944     5
#>  8  2020 10    DE    Delaware        989948     1
#>  9  2020 16    ID    Idaho          1839106     2
#> 10  2020 12    FL    Florida       21538187    28
#> # … with 40 more rows
```

``` r
real_seats <- congress_actual %>% 
  filter(year == 2020) %>% 
  select(state_name, real_seats = seats)

compare_seats <- left_join(seats2020, real_seats, by = "state_name")

all(compare_seats$seats == compare_seats$real_seats)
#> [1] TRUE
```

### Cube root rule

The first alternative we’ll consider is the [cube root rule](https://en.wikipedia.org/wiki/Cube_root_rule).
This method is similar to the current method of apportionment, in that the Huntington-Hill algorithm is used to assign seats to each state.
However, rather than the total number of representatives be fixed at 435, the total is determined by taking the cube root of the total population.
This rule is derived from the [findings of Rein Taagepera](https://www.sciencedirect.com/science/article/abs/pii/0049089X72900841?via%3Dihub), who found that around the world the size of national assemblies tends to be around the cube root of the total population.

In the United States, we have two legislative bodies, the Senate and the House of Representatives.
The Senate is made up of 100 representatives (2 from each of the 50 states).
Therefore, when using the cube root rule, the number of seats in the House of Representatives is calculated as the cube root of the total population minus 100.
When using the cube root rule, rather than 435 seats in the House of Representatives, there would have been 554 following the 2000 census, 575 after the 2010 census, and 591 starting in 2022, following the 2020 census.

``` r
state_pop %>% 
  filter(!state_name %in% c("District of Columbia", "Puerto Rico")) %>%
  add_count(year, wt = population, name = "total_pop") %>% 
  nest(state_pop = -c(year, total_pop)) %>% 
  mutate(total_reps = as.integer(total_pop ^ (1 / 3)),
         house_seats = total_reps - 100L)
#> # A tibble: 3 × 5
#>    year total_pop state_pop         total_reps house_seats
#>   <dbl>     <dbl> <list>                 <int>       <int>
#> 1  2000 280849847 <tibble [50 × 4]>        654         554
#> 2  2010 308143815 <tibble [50 × 4]>        675         575
#> 3  2020 330759736 <tibble [50 × 4]>        691         591
```

We can then apply the Huntington-Hill algorithm to determine how many seats each state would receive when setting the total number of seats based on the cube root rule.

<details>
<summary>
Code to reproduce
</summary>

``` r
cube_root_seats <- state_pop %>% 
  filter(!state_name %in% c("District of Columbia", "Puerto Rico")) %>%
  add_count(year, wt = population, name = "total_pop") %>% 
  nest(state_pop = -c(year, total_pop)) %>% 
  mutate(total_reps = as.integer(total_pop ^ (1 / 3)),
         house_seats = total_reps - 100L,
         cube_seats = map2(state_pop, house_seats, hunt_hill)) %>% 
  select(year, cube_seats) %>% 
  unnest(cube_seats) %>% 
  rename(cube_root_seats = seats)
  

cube_dt <- cube_root_seats %>% 
  select(state_name, year, cube_root_seats) %>% 
  pivot_wider(names_from = year, values_from = cube_root_seats) %>% 
  datatable(rownames = FALSE,
            colnames = c("State", "2000", "2010", "2020"),
            options = list(
              autoWidth = TRUE,
              columnDefs = list(list(className = "dt-left", targets = 0),
                                list(width = "40%", targets = 0),
                                list(width = "20%", targets = 1:3)),
              pageLength = 10,
              lengthMenu = c(5, 10, 25, 50)
            ))
```

</details>

<br>

<div id="htmlwidget-3" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-3">{"x":{"filter":"none","vertical":false,"data":[["Alabama","Alaska","Arizona","Arkansas","California","Colorado","Connecticut","Delaware","Florida","Georgia","Hawaii","Idaho","Illinois","Indiana","Iowa","Kansas","Kentucky","Louisiana","Maine","Maryland","Massachusetts","Michigan","Minnesota","Mississippi","Missouri","Montana","Nebraska","Nevada","New Hampshire","New Jersey","New Mexico","New York","North Carolina","North Dakota","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Vermont","Virginia","Washington","West Virginia","Wisconsin","Wyoming"],[9,1,10,5,67,8,7,2,31,16,2,3,24,12,6,5,8,9,3,10,12,20,10,6,11,2,3,4,2,17,4,37,16,1,22,7,7,24,2,8,2,11,41,4,1,14,12,4,11,1],[9,1,12,5,69,9,7,2,35,18,3,3,24,12,6,5,8,8,3,11,12,18,10,6,11,2,3,5,3,16,4,36,18,1,21,7,7,24,2,9,2,12,47,5,1,15,13,3,11,1],[9,1,13,5,71,10,6,2,38,19,3,3,23,12,6,5,8,8,2,11,13,18,10,5,11,2,4,6,3,17,4,36,19,1,21,7,8,23,2,9,2,12,52,6,1,15,14,3,11,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>State<\/th>\n      <th>2000<\/th>\n      <th>2010<\/th>\n      <th>2020<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"autoWidth":true,"columnDefs":[{"className":"dt-left","targets":0},{"width":"40%","targets":0},{"width":"20%","targets":[1,2,3]},{"className":"dt-right","targets":[1,2,3]}],"pageLength":10,"lengthMenu":[5,10,25,50],"order":[],"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

### Wyoming rule

The second alternative apportionment method is the [Wyoming rule](https://en.wikipedia.org/wiki/Wyoming_Rule).
Unlike our current apportionment method and the cube root rule, the Wyoming rule does not use the Huntington-Hill algorithm to apportion seats.
Instead, the size (in terms of population) of each congressional district is based on the population of the smallest state, which has been Wyoming since 1990.

Using this method, the state with the smallest population (Wyoming) gets one seat in the House of Representatives.
The number of seats for other states depends how many times larger their population is than Wyoming’s.
If a state has 2x the population, they get 2 seats (really 1.5x larger due to rounding).
Similarly, if a state has 3x the population, that state would get 3 seats (again, 2.5x with rounding).
For example, in the 2020 census, Wyoming had a total population of 576,851, and Nevada had a population of 3,104,614.
Therefore, when using the Wyoming rule, Nevada would get 3,104,614 ÷ 576,851 = 5.382 = 5 seats.

The table below shows the number of seats that would be appropriated to each state with the Wyoming rule.

<details>
<summary>
Code to reproduce
</summary>

``` r
wyoming_seats <- state_pop %>% 
  filter(!state_name %in% c("District of Columbia", "Puerto Rico")) %>% 
  group_by(year) %>% 
  mutate(wyoming_seats = round(population / min(population), digits = 0),
         wyoming_seats = as.integer(wyoming_seats)) %>% 
  ungroup()

wyoming_dt <- wyoming_seats %>% 
  select(state_name, year, wyoming_seats) %>% 
  pivot_wider(names_from = year, values_from = wyoming_seats) %>% 
  datatable(rownames = FALSE,
            colnames = c("State", "2000", "2010", "2020"),
            options = list(
              autoWidth = TRUE,
              columnDefs = list(list(className = "dt-left", targets = 0),
                                list(width = "40%", targets = 0),
                                list(width = "20%", targets = 1:3)),
              pageLength = 10,
              lengthMenu = c(5, 10, 25, 50)
            ))
```

</details>

<br>

<div id="htmlwidget-4" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-4">{"x":{"filter":"none","vertical":false,"data":[["Alabama","Alaska","Arizona","Arkansas","California","Colorado","Connecticut","Delaware","Florida","Georgia","Hawaii","Idaho","Illinois","Indiana","Iowa","Kansas","Kentucky","Louisiana","Maine","Maryland","Massachusetts","Michigan","Minnesota","Mississippi","Missouri","Montana","Nebraska","Nevada","New Hampshire","New Jersey","New Mexico","New York","North Carolina","North Dakota","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Vermont","Virginia","Washington","West Virginia","Wisconsin","Wyoming"],[9,1,10,5,69,9,7,2,32,17,2,3,25,12,6,5,8,9,3,11,13,20,10,6,11,2,3,4,3,17,4,38,16,1,23,7,7,25,2,8,2,12,42,5,1,14,12,4,11,1],[8,1,11,5,66,9,6,2,33,17,2,3,23,12,5,5,8,8,2,10,12,18,9,5,11,2,3,5,2,16,4,34,17,1,20,7,7,23,2,8,1,11,45,5,1,14,12,3,10,1],[9,1,12,5,69,10,6,2,37,19,3,3,22,12,6,5,8,8,2,11,12,17,10,5,11,2,3,5,2,16,4,35,18,1,20,7,7,23,2,9,2,12,51,6,1,15,13,3,10,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th>State<\/th>\n      <th>2000<\/th>\n      <th>2010<\/th>\n      <th>2020<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"autoWidth":true,"columnDefs":[{"className":"dt-left","targets":0},{"width":"40%","targets":0},{"width":"20%","targets":[1,2,3]},{"className":"dt-right","targets":[1,2,3]}],"pageLength":10,"lengthMenu":[5,10,25,50],"order":[],"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

## Effects of different apportionments

Congressional apportionment has two major impacts:

1.  The make up of congress, and
2.  The Electoral College.

It’s hard to know exactly how the make up of congress would be affected by different apportionment rules, as we have no way of knowing where the hypothetical congressional lines would be drawn, and how much [gerrymandering](https://en.wikipedia.org/wiki/Gerrymandering) may or may not take place.
However, we can examine which states gain or lose power, as measured by the difference in the percentage of the total representatives that come from each state.
Additionally, we can look at measures of fairness in appropriate, such as the average size of districts and the ratio of sizes of the largest and smallest districts.

The Electoral College is a little more straightforward.
The number of Electoral College votes each state has is equal to the number of seats in the House of Representatives plus 2 (the number of senators).
In most cases, all of a state’s electoral votes go to the state-wide winner.
It’s reasonable to assume that changing the number of representatives would not change the state-wide presidential winner (although it’s possible if down-ballot representative races would have motivated turnout in a particular party).
Using the assumption that state-wide presidential results were the same, we can explore how the electoral votes allocated under each rule may have affected the outcome of the presidential elections.

Before we get started, we combine the number of seats from each rule that we’ve calculated.

``` r
state_map <- us_states("2000-12-31") %>% 
  shift_geometry()

all_seats <- list(state_pop %>% 
                    filter(!state_name %in% c("District of Columbia",
                                              "Puerto Rico")) %>% 
                    select(year, state, state_name, population),
                  congress_actual %>% 
                    select(year, state, state_name, real_seats = seats),
                  cube_root_seats %>% 
                    select(year, state, state_name, cube_root_seats),
                  wyoming_seats %>% 
                    select(year, state, state_name, wyoming_seats)) %>% 
  reduce(full_join, by = c("year", "state", "state_name")) %>% 
  mutate(year = as.integer(year))

all_seats
#> # A tibble: 150 × 7
#>     year state state_name  population real_seats cube_root_seats wyoming_seats
#>    <int> <chr> <chr>            <dbl>      <int>           <int>         <int>
#>  1  2000 AL    Alabama        4447100          7               9             9
#>  2  2000 AK    Alaska          626932          1               1             1
#>  3  2000 AZ    Arizona        5130632          8              10            10
#>  4  2000 AR    Arkansas       2673400          4               5             5
#>  5  2000 CA    California    33871648         53              67            69
#>  6  2000 CO    Colorado       4301261          7               8             9
#>  7  2000 CT    Connecticut    3405565          5               7             7
#>  8  2000 DE    Delaware        783600          1               2             2
#>  9  2000 FL    Florida       15982378         25              31            32
#> 10  2000 GA    Georgia        8186453         13              16            17
#> # … with 140 more rows
```

### 2000 census

<details>
<summary>
Code to reproduce
</summary>

``` r
# -0.308, 0.363
state_map %>% 
  left_join(filter(all_seats, year == 2000),
            by = c("state_abbr" = "state", "state_name")) %>% 
  filter(terr_type == "State") %>% 
  mutate(state_power = real_seats / sum(real_seats, na.rm = TRUE),
         cr_state_power = cube_root_seats / sum(cube_root_seats, na.rm = TRUE),
         wy_state_power = wyoming_seats / sum(wyoming_seats, na.rm = TRUE)) %>% 
  pivot_longer(contains("_state_power"), names_to = "method",
               values_to = "power") %>% 
  mutate(pct_change_power = (power - state_power) / power,
         method = case_when(method == "cr_state_power" ~ "Cube Root Rule",
                            method == "wy_state_power" ~ "Wyoming Rule")) %>% 
  ggplot() +
  facet_grid(cols = vars(method)) +
  geom_sf(aes(fill = pct_change_power), size = 0.5, color = "#F0F0F0") +
  scale_fill_viridis_c(option = "F", direction = -1, na.value = "grey95",
                       guide = guide_colourbar(barwidth = 12, title.position = "top"),
                       labels = scales::percent_format(),
                       limits = c(-0.4, 0.4)) +
  coord_sf(crs = st_crs("ESRI:102003")) +  # Albers
  labs(title = "Change in relative state House power for 2000 census",
       subtitle = "House power calculated by dividing each state's total number of representatives\nby the overall number of representatives",
       fill = "Percent change in relative power",
       caption = "Plot: @wjakethompson") +
  theme(panel.grid.major = element_blank(),
        axis.text.x = element_blank(),
        axis.text.y = element_blank(),
        legend.title = element_text(hjust = 0.5),
        plot.caption = element_text(face = "plain"),
        strip.text = element_text(colour = "black")) -> p


ggsave(fig_path(".png"), plot = p, width = 9, height = 5, units = "in",
       dpi = "retina")
```

</details>

<br>

<img src="{{< blogdown/postref >}}index_files/figure-html/power2000-1.png" width="100%" style="display: block; margin: auto;" />

### 2010 census

### 2020 census

``` r
all_seats %>% 
  pivot_longer(ends_with("seats"), names_to = "method", values_to = "seats",
               names_pattern = "(.*)_seats") %>% 
  group_by(year, method) %>% 
  mutate(prop_seat = seats / sum(seats),
         people_per_seat = population / seats)
#> # A tibble: 450 × 8
#> # Groups:   year, method [9]
#>     year state state_name population method    seats prop_seat people_per_seat
#>    <int> <chr> <chr>           <dbl> <chr>     <int>     <dbl>           <dbl>
#>  1  2000 AL    Alabama       4447100 real          7   0.0161          635300 
#>  2  2000 AL    Alabama       4447100 cube_root     9   0.0162          494122.
#>  3  2000 AL    Alabama       4447100 wyoming       9   0.0158          494122.
#>  4  2000 AK    Alaska         626932 real          1   0.00230         626932 
#>  5  2000 AK    Alaska         626932 cube_root     1   0.00181         626932 
#>  6  2000 AK    Alaska         626932 wyoming       1   0.00176         626932 
#>  7  2000 AZ    Arizona       5130632 real          8   0.0184          641329 
#>  8  2000 AZ    Arizona       5130632 cube_root    10   0.0181          513063.
#>  9  2000 AZ    Arizona       5130632 wyoming      10   0.0176          513063.
#> 10  2000 AR    Arkansas      2673400 real          4   0.00920         668350 
#> # … with 440 more rows
```
