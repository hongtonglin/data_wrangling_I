03_tidy_data
================
Hongtong Lin
2026-09-29

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.1     ✔ readr     2.2.0
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.3     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.5     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## data tidying

``` r
pulse_df <- 
  haven::read_sas("data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names()
```

let’s tidy

``` r
pulse_tidy_df <- 
  pulse_df |> 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", # send the colnames of the previous list as "visit"
    names_prefix = "bdi_score_", # tell R that there's a prefix for every single value of the visit
    values_to = "bdi_score" # send the values of the previous list "bdi_score"
  ) |> 
  mutate(
    visit = replace(visit, visit == "bl", "00m")
  )
```

Practice

Import the littes data; keep columns litter number and GD weights; tidy

``` r
litters_df <- read_csv(
  "data/FAS_litters.csv", na= c("", "NA", ".")
) |> 
  janitor::clean_names()
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
litters_tidy_df <-
  litters_df |> 
  select(litter_number, gd0_weight:gd18_weight) |> 
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd",
    names_prefix = "gd",
    values_to = "weight"
  ) |> 
  mutate(
    gd =  str_remove(gd, "_weight")
  )

#|> 
#  mutate(
 #   gd = recode_values(
  #    gd,
   #   "gd0_weight" ~ 0, # match "gd0_weight" and replace it with 0
    #  "gd18_weight" ~ 18 # match "gd18_weight" and replace it with 18
    #)
 # )
```

## Deliberately untidy data

``` r
analysis_df <- 
  tibble(
    groups = c("treatment", "treatment", "placebo", "placebo"),
    time  = c("pre", "post", "pre", "post"),
    mean_outcome = c(4, 8, 3.5, 4.6)
  )
```

untidy a data set: making a wide data set

``` r
analysis_df |> 
  pivot_wider(
    names_from = time,
    values_from = mean_outcome
  ) |> 
  knitr::kable() # format the table as a markdown table
```

| groups    | pre | post |
|:----------|----:|-----:|
| treatment | 4.0 |  8.0 |
| placebo   | 3.5 |  4.6 |

## Bind tables from Lord of ring tables

import each lotr movie table

``` r
fellowship_df <- 
  readxl::read_xlsx("data/LotR_Words.xlsx", range = "B3:D6") |> 
  mutate(movie = "fellowship")

two_towers_df <- 
  readxl::read_xlsx("data/LotR_Words.xlsx", range = "F3:H6") |> 
  mutate(movie = "two_towers")

return_df <- 
  readxl::read_xlsx("data/LotR_Words.xlsx", range = "J3:L6") |> 
  mutate(movie = "return of the king")
```

Next, join all of these together and binding

``` r
lotr_df <- 
  bind_rows(fellowship_df, two_towers_df, return_df) |> 
  janitor::clean_names() |> 
  relocate(movie) |>  # put the movie column to the front
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )
```

## join FAS datasets

import both

``` r
pups_df <- 
  read_csv("data/FAS_pups.csv",
           skip = 3,
           na = c("", "NA", ".")) |> 
  janitor::clean_names() |> 
  mutate(
    sex = case_match(
      sex,
      1 ~ "male",
      2 ~ "female"
    )
  )
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `sex = case_match(sex, 1 ~ "male", 2 ~ "female")`.
    ## Caused by warning:
    ## ! `case_match()` was deprecated in dplyr 1.2.0.
    ## ℹ Please use `recode_values()` instead.

``` r
litters_df <- 
    read_csv("data/FAS_litters.csv",
           na = c("", "NA", ".")) |> 
  janitor::clean_names() |> 
  relocate(litter_number) |> 
  separate(group, into = c("dose", "day_of_tx"), 3) |> # 3 indicates the split happens after the 3rd character
  mutate(
    dose = str_to_lower(dose),
    day_of_tx = as.numeric(day_of_tx),
    gd_weight_gain = gd18_weight - gd0_weight
  ) |> 
  relocate(gd_weight_gain, .after = day_of_tx)
```

Join both

``` r
fas_df <-
  left_join(pups_df, litters_df, by = "litter_number") # Joining with `by = join_by(litter_number)`
```
