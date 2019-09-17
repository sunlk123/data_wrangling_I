Data Import
================
2019-09-17

## Load in a dataset

``` r
##specifying column types 

litters_data = read_csv(file = "./data/FAS_litters.csv",
  col_types = cols(
    Group = col_character(),
    `Litter Number` = col_character(),
    `GD0 weight` = col_double(),
    `GD18 weight` = col_double(),
    `GD of Birth` = col_integer(),
    `Pups born alive` = col_integer(),
    `Pups dead @ birth` = col_integer(),
    `Pups survive` = col_integer()
  )
)

litters_data = janitor::clean_names(litters_data)
```

## Load in pups data

``` r
pups_data = read_csv(file = "./data/FAS_pups.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

``` r
pups_data = janitor::clean_names(pups_data)

pups_data_2 = read_csv(file = "/Users/lorrainekwok/Desktop/Fall 2019/Data Science/Session 5/data_wrangling_I/data/FAS_pups.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

## Read in an excel file

``` r
mlb11_data = 
  read_excel(
    path = "./data/mlb11.xlsx",
    range = "A1:D7")
```

## Read in SAS file

``` r
pulse_data = haven::read_sas("./data/public_pulse_data.sas7bdat")
```
