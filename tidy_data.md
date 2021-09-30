tidy data 9.30
================

I’m an R Markdown document!

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

``` r
pulse_tidy = 
  pulse_df %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>% 
  mutate(
    visit = replace(visit, visit == "bl", "00m") # remember to use double "=" when replace
  )
```

``` r
analysis_df = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

analysis_df %>% 
  pivot_wider(
    names_from = "time",
    values_from = "mean"
  ) %>% 
  knitr::kable() # output a nice table when report using "knit".
```

| group     | pre | post |
|:----------|----:|-----:|
| treatment | 4.0 |    8 |
| placebo   | 3.5 |    4 |

## bind\_rows

``` r
fellowship_df =
  read_excel("data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship_rings")

two_towers_df =
  read_excel("data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "two_towers")

return_df =
  read_excel("data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")

lotr_df = 
  bind_rows(fellowship_df, two_towers_df, return_df) %>% # never use 'rbind()'
  janitor::clean_names() %>% 
  pivot_longer(
    female:male,
    names_to = "sex",
    values_to = "words"
  ) %>% 
  relocate(movie)
```

## jions

Look at FAS data.

``` r
litters_df =
  read_csv("data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  separate(group, into = c("dose", "day_of_tx"), 3) %>% # but always use 'pivot_wider()'
  relocate(litter_number) %>% 
  mutate(dose = str_to_lower(dose))
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
pups_df =
    read_csv("data/FAS_pups.csv") %>% 
    janitor::clean_names() %>% 
    mutate(sex = recode(sex, "1" = "male", "2" = "female"))
```

    ## Rows: 313 Columns: 6

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Let’s join them up.

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number")
```
