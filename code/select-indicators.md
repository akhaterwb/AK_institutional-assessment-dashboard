# Process selected indicators

- Input: `data/db_variables.xlsx`
- Outputs: 
  - `data/final/db_variables.xlsx`
  - `data/final/definitions.rds`

## Load packages


```r
packages <- 
  c(
    "tidyverse",
    "here",
    "readxl"
  )

pacman::p_load(
  packages, 
  character.only = TRUE
)
```


## Load list of selected indicators

This list is filled by hand in Excel.


```r
db_variables <-
  read_excel(
    here(
      "..",
      "data",
      "db_variables.xlsx"
    )
  )
```

## Save list of selected indicators in R format


```r
write_rds(
  db_variables,
  here(
    "..",
    "data",
    "final",
    "db_variables.rds"
  )
)
```

## Save variable definitions by family


```r
description <- 
  function(x) {
    assign(
      x,
      db_variables %>%
        filter(family_name == x) %>%
        select(
          Indicator = var_name,
          Description = description,
          Source = source
        )
    )
  }

description <-
  lapply(
    unique(db_variables$family_name),
    description
  )

names(description) <- 
  unique(db_variables$family_name)

write_rds(
  description,
  here(
    "..",
    "data",
    "final",
    "definitions.rds"
  )
)
```

## Define list of variables


```r
source(here("vars-control.R"))
```
