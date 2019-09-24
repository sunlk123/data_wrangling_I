tidy\_data
================
Lorraine Kwok
2019-09-24

## Wide to long

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi"
    ) %>%
  mutate(
    visit = recode(visit, "bl" = "00m")
  )
pulse_data
```

    ## # A tibble: 4,348 x 5
    ##       id   age sex   visit   bdi
    ##    <dbl> <dbl> <chr> <chr> <dbl>
    ##  1 10003  48.0 male  00m       7
    ##  2 10003  48.0 male  01m       1
    ##  3 10003  48.0 male  06m       2
    ##  4 10003  48.0 male  12m       0
    ##  5 10015  72.5 male  00m       6
    ##  6 10015  72.5 male  01m      NA
    ##  7 10015  72.5 male  06m      NA
    ##  8 10015  72.5 male  12m      NA
    ##  9 10022  58.5 male  00m      14
    ## 10 10022  58.5 male  01m       3
    ## # … with 4,338 more rows

## separate in litters

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  separate(col = group, into = c("dose", "day_of_tx"), 3)
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

``` r
#Con7 is actually two variables togther so needed to separate the two
litters_data
```

    ## # A tibble: 49 x 9
    ##    dose  day_of_tx litter_number gd0_weight gd18_weight gd_of_birth
    ##    <chr> <chr>     <chr>              <dbl>       <dbl>       <dbl>
    ##  1 Con   7         #85                 19.7        34.7          20
    ##  2 Con   7         #1/2/95/2           27          42            19
    ##  3 Con   7         #5/5/3/83/3-3       26          41.4          19
    ##  4 Con   7         #5/4/2/95/2         28.5        44.1          19
    ##  5 Con   7         #4/2/95/3-3         NA          NA            20
    ##  6 Con   7         #2/2/95/3-2         NA          NA            20
    ##  7 Con   7         #1/5/3/83/3-…       NA          NA            20
    ##  8 Con   8         #3/83/3-3           NA          NA            20
    ##  9 Con   8         #2/95/3             NA          NA            20
    ## 10 Con   8         #3/5/2/2/95         28.5        NA            20
    ## # … with 39 more rows, and 3 more variables: pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>, pups_survive <dbl>
