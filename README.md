
<!-- README.md is generated from README.Rmd. Please edit that file -->

# STM <img src="inst/figures/logo.png" align="right" width="200" />

<!-- badges: start -->

[![CRAN
status](https://www.r-pkg.org/badges/version/STM)](https://CRAN.R-project.org/package=STM)
<!-- badges: end -->

The goal of `STM` is to provide the readers of the [Strength Training
Manual](https://amzn.to/3owbBr6) a list of functions to help them
re-create set and rep schemes as well as to create their own in
reproducible and open-source environment.

## Installation

You can install the released version (once released) of `STM` from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("STM")
```

And the development version from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("mladenjovanovic/STM")
```

## Examples

This is a quick example, more are coming.

``` r
require(tidyverse)
require(ggstance)
require(STM)

# Wave set and rep scheme
scheme <- scheme_wave(
  reps = c(10, 8, 6, 10, 8, 6),
  # Adjusting using lower %1RM (perc_drop method used)
  adjustment = c(4, 2, 0, 6, 4, 2),
  vertical_planning = vertical_linear,
  vertical_planning_control = list(reps_change = c(0, -2, -4)),
  progression_table = RIR_increment,
  progression_table_control = list(volume = "extensive"))

# Reorganize the data
scheme <- scheme %>%
  group_by(index) %>%
  mutate(
    set = row_number(),
    adjustment = round(adjustment, 1),
    perc_1RM = round(perc_1RM * 100),
    index = paste0("Week ", index)) %>%
  rename(
    RIR = adjustment,
    `%1RM` = perc_1RM) %>%
  pivot_longer(cols = c("reps", "RIR", "%1RM"), names_to = "param") %>%
  mutate(param = factor(param, levels = c("reps", "RIR", "%1RM")))

# Plot
ggplot(scheme, aes(x = value, y = set)) +
  theme_bw() +
  geom_barh(stat =  "identity") +
  geom_label(aes(label = value), hjust = 1.1, size = 3) +
  facet_grid(index~param, scales = "free_x") +
  scale_y_reverse() +
  xlab(NULL) +
  ylab(NULL)
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" />
