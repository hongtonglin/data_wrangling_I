Data Import
================

This file is for doing data import.

## Import 2 data sets

``` r
library(tidyverse)
library(readxl) # read excels 
library(haven) # import sas
```

Import the first dataset.

``` r
litters_df <- 
  read_csv(
    file = "data/FAS_litters.csv",  # relative path
    na = c("", "NA", ".") #what does NA mean in this data set. fix this during the data import before analysis
    )  
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
#always use read_csv not read.csv
#always use a relative path name that everyone can use 
#in the output, the GD0 weight is a character not a number. 
#fix na thing and clean names before any analysis.  

litters_df <- janitor::clean_names(litters_df) #clean names to have the same rule
```

Import the FAS_pups dataset.

``` r
pups_df = 
  read_csv(
    "data/FAS_pups.csv", 
    skip = 3,
    na = c("", "NA", ".")
    )
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# "skip=3" can skip the first 3 rows.  

pups_df = janitor::clean_names(pups_df)
```

## Look at the data

``` r
litters_df
```

    ## # A tibble: 49 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
pups_df
```

    ## # A tibble: 313 × 6
    ##    litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85               1       4      13        7      11
    ##  2 #85               1       4      13        7      12
    ##  3 #1/2/95/2         1       5      13        7       9
    ##  4 #1/2/95/2         1       5      13        8      10
    ##  5 #5/5/3/83/3-3     1       5      13        8      10
    ##  6 #5/5/3/83/3-3     1       5      14        6       9
    ##  7 #5/4/2/95/2       1      NA      14        5       9
    ##  8 #4/2/95/3-3       1       4      13        6       8
    ##  9 #4/2/95/3-3       1       4      13        7       9
    ## 10 #2/2/95/3-2       1       4      NA        8      10
    ## # ℹ 303 more rows

Head or Tail

``` r
head(litters_df, 20)
```

    ## # A tibble: 20 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## 11 Con8  #5/4/3/83/3           28          NA            19               9
    ## 12 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ## 13 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## 14 Con8  #2/2/95/2             NA          NA            19               5
    ## 15 Con8  #3/6/2/2/95-3         NA          NA            20               7
    ## 16 Mod7  #59                   17          33.4          19               8
    ## 17 Mod7  #103                  21.4        42.1          19               9
    ## 18 Mod7  #1/82/3-2             NA          NA            19               6
    ## 19 Mod7  #3/83/3-2             NA          NA            19               8
    ## 20 Mod7  #2/95/2-2             NA          NA            20               7
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
tail(pups_df, 5)
```

    ## # A tibble: 5 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ## 1 #2/95/2           2       3      13        6       8
    ## 2 #2/95/2           2       3      13        7       9
    ## 3 #82/4             2       4      13        7       9
    ## 4 #82/4             2       3      13        7       9
    ## 5 #82/4             2       3      13        7       9

skimming: **do it in the console**

``` r
skimr::skim(pups_df)
```

|                                                  |         |
|:-------------------------------------------------|:--------|
| Name                                             | pups_df |
| Number of rows                                   | 313     |
| Number of columns                                | 6       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |         |
| Column type frequency:                           |         |
| character                                        | 1       |
| numeric                                          | 5       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |         |
| Group variables                                  | None    |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| litter_number |         0 |             1 |   3 |  15 |     0 |       49 |          0 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |  mean |   sd |  p0 | p25 | p50 | p75 | p100 | hist  |
|:--------------|----------:|--------------:|------:|-----:|----:|----:|----:|----:|-----:|:------|
| sex           |         0 |          1.00 |  1.50 | 0.50 |   1 |   1 |   2 |   2 |    2 | ▇▁▁▁▇ |
| pd_ears       |        18 |          0.94 |  3.68 | 0.59 |   2 |   3 |   4 |   4 |    5 | ▁▅▁▇▁ |
| pd_eyes       |        13 |          0.96 | 12.99 | 0.62 |  12 |  13 |  13 |  13 |   15 | ▂▇▁▂▁ |
| pd_pivot      |        13 |          0.96 |  7.09 | 1.51 |   4 |   6 |   7 |   8 |   12 | ▂▇▂▂▁ |
| pd_walk       |         0 |          1.00 |  9.50 | 1.34 |   7 |   9 |   9 |  10 |   14 | ▆▇▇▂▁ |

Viewing: **do it in the console**

``` r
view(pups_df)
```

## Excel?

``` r
library(tidyverse)
library(readxl)
```

``` r
mlb_df =
  read_xlsx("data/mlb11.xlsx") # read_xlsx can specify the cell range and sheet 
```

Look at the data

``` r
mlb_df
```

    ## # A tibble: 30 × 12
    ##    team        runs at_bats  hits homeruns bat_avg strikeouts stolen_bases  wins
    ##    <chr>      <dbl>   <dbl> <dbl>    <dbl>   <dbl>      <dbl>        <dbl> <dbl>
    ##  1 Texas Ran…   855    5659  1599      210   0.283        930          143    96
    ##  2 Boston Re…   875    5710  1600      203   0.28        1108          102    90
    ##  3 Detroit T…   787    5563  1540      169   0.277       1143           49    95
    ##  4 Kansas Ci…   730    5672  1560      129   0.275       1006          153    71
    ##  5 St. Louis…   762    5532  1513      162   0.273        978           57    90
    ##  6 New York …   718    5600  1477      108   0.264       1085          130    77
    ##  7 New York …   867    5518  1452      222   0.263       1138          147    97
    ##  8 Milwaukee…   721    5447  1422      185   0.261       1083           94    96
    ##  9 Colorado …   735    5544  1429      163   0.258       1201          118    73
    ## 10 Houston A…   615    5598  1442       95   0.258       1164          118    56
    ## # ℹ 20 more rows
    ## # ℹ 3 more variables: new_onbase <dbl>, new_slug <dbl>, new_obs <dbl>

Load some LotR data

Import FOTR words

``` r
fotr_df = 
  read_xlsx(
    "data/LotR_Words.xlsx",
    range = "B3:D6" # specify the range of interest to be imported
    )

fotr_df = janitor::clean_names(fotr_df)
```

Two Towers data from LotR

``` r
two_towers_df =
  read_xlsx(
  "data/LotR_Words.xlsx",
  range = "F3:H6"  
  )

two_towers_df = janitor::clean_names(two_towers_df)
```

## Import SAS

Read in the PULSE dataset

``` r
pulse_df = 
  read_sas(
    "data/public_pulse_data.sas7bdat"
  )

pulse_df = janitor::clean_names(pulse_df)
```
