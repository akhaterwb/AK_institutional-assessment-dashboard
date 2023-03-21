# Process manual additions

- Input: `data/raw/20211118_new_additions_notGov360.dta`
- Output: `data/clean/additional_data.rds`

## Load packages


```r
packages <- 
  c(
    "tidyverse",
    "here",
    "haven",
    "labelled"
  )

pacman::p_load(packages, character.only = TRUE)
```

## Import data

```r
additions <- 
  read_dta(
    here(
      "..",
      "data",
      "raw",
      "20211118_new_additions_notGov360.dta"
    )
  )
```

## Keep only relevant data


```r
additions_selected <-
  additions %>%
  filter(year >= 2015) %>%
  mutate(
    country_code = iso3code,
    year = as.character(year)
  ) %>%
  select(
    country_code,
    year,
    all_of(vars_additions)
  )
```


```r
additions_clean <-
  additions_selected %>%
  remove_labels() %>%
  group_by(country_code, year) %>%
  summarise(
    across(
      where(is.numeric),
      ~ mean(., na.rm = TRUE) %>%
        replace(is.nan(.), NA)
    )
  ) %>%
  mutate(
    across(
      where(is.numeric),
      ~ ifelse(is.nan(.), NA, .)
    )
  )
```

```
## `summarise()` has grouped output by 'country_code'. You can override using the `.groups`
## argument.
```

## Save data


```r
write_rds(
  additions_clean,
  here(
    "..",
    "data",
    "clean",
    "additional_data.rds"
  )
)
```
