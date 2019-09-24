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

## go untidy…

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)
analysis_result
```

    ## # A tibble: 4 x 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

``` r
pivot_wider(
  analysis_result,
  names_from = time, 
  values_from = mean
)
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

``` r
pivot_wider
```

    ## function (data, id_cols = NULL, names_from = name, names_prefix = "", 
    ##     names_sep = "_", names_repair = "check_unique", values_from = value, 
    ##     values_fill = NULL, values_fn = NULL) 
    ## {
    ##     names_from <- enquo(names_from)
    ##     values_from <- enquo(values_from)
    ##     spec <- build_wider_spec(data, names_from = !!names_from, 
    ##         values_from = !!values_from, names_prefix = names_prefix, 
    ##         names_sep = names_sep)
    ##     id_cols <- enquo(id_cols)
    ##     pivot_wider_spec(data, spec, !!id_cols, names_repair = names_repair, 
    ##         values_fill = values_fill, values_fn = values_fn)
    ## }
    ## <bytecode: 0x7f7f773a3400>
    ## <environment: namespace:tidyr>

### bind rows

``` r
#fellowship_ring = 
#  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>%
#  mutate(movie = "fellowship_ring")

#two_towers = 
#  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>%
#  mutate(movie = "two_towers")

#return_king = 
#  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>%
#  mutate(movie = "return_king")

#lotr_data =
#  bind_rows(two_towers, return_king)
#  janitor::clean_names() %>%
#    pivot_longer(
#      female:male,
#     names_to = "sex",
#      values_to = "words"
#    ) %>% 
#    select(movie, race, sex, words)
```

``` r
pup_data = 
  read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>%
  janitor::clean_names() %>%
  mutate(sex = recode(sex, `1` = "male", `2` = "female")) 

litter_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))
```

try to join these datasets\!

``` r
fas_data = 
  left_join(pup_data, litter_data, by = "litter_number")
fas_data
```

    ## # A tibble: 313 x 13
    ##    litter_number sex   pd_ears pd_eyes pd_pivot pd_walk group gd0_weight
    ##    <chr>         <chr>   <int>   <int>    <int>   <int> <chr>      <dbl>
    ##  1 #85           male        4      13        7      11 con7        19.7
    ##  2 #85           male        4      13        7      12 con7        19.7
    ##  3 #1/2/95/2     male        5      13        7       9 con7        27  
    ##  4 #1/2/95/2     male        5      13        8      10 con7        27  
    ##  5 #5/5/3/83/3-3 male        5      13        8      10 con7        26  
    ##  6 #5/5/3/83/3-3 male        5      14        6       9 con7        26  
    ##  7 #5/4/2/95/2   male       NA      14        5       9 con7        28.5
    ##  8 #4/2/95/3-3   male        4      13        6       8 con7        NA  
    ##  9 #4/2/95/3-3   male        4      13        7       9 con7        NA  
    ## 10 #2/2/95/3-2   male        4      NA        8      10 con7        NA  
    ## # … with 303 more rows, and 5 more variables: gd18_weight <dbl>,
    ## #   gd_of_birth <int>, pups_born_alive <int>, pups_dead_birth <int>,
    ## #   wt_gain <dbl>
