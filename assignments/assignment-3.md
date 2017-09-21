
Assignment 3: Interactive graphics with Gapminder
=================================================

[Gapminder](http://www.gapminder.org/data/) compiles detailed indicators of global development. For this assignment, you should build an interactive visualization to tell a story about the data. **This requires you to develop both the visualization as well as a written narrative.**

-   You do not need to analyze all 500+ variables. You can focus on a small set of variables (whichever ones you wish, though check the indicator's coverage for all the countries and the time period available)
-   You can build either a highly linear or non-linear structure to your visualization. For instance, you can write up an analysis of the data as an R Markdown document/website with interactive visualizations incorporated throughout the analysis, or build a Shiny application with which users can explore the data. Choose whichever format you find more appealing. Note that if you design a non-linear interactive visualization, you still need to situate it using an introduction or text description (a la [America's Public Bible](http://americaspublicbible.org/)[1]. See FA ch 9 for more details.)
-   [`gapminder`](https://cran.r-project.org/web/packages/gapminder/index.html) only contains data on three variables. You will need to obtain additional indicators from Gapminder's [data page](http://www.gapminder.org/data/). Unfortunately they don't have a friendly API to access their database - each variable has to be downloaded manually as an Excel spreadsheet and imported into R. The following basic function should allow you to import a downloaded spreadsheet into R,[2] and tidy:

``` r
library(tidyverse)
library(readxl)

import_gapminder <- function(filename, inc_name = NA){
  # import file
  indicator <- read_excel(filename)
  
  # rename first column to country, store indicator name for later
  inc_fullname <- names(indicator)[[1]]
  names(indicator)[[1]] <- "country"
  
  # tidy data frame and add indicator name as variable
  indicator <- indicator %>%
    gather(year, value, -1, convert = TRUE) %>%
    mutate(value = as.numeric(value),
           variable = ifelse(!is.na(inc_name), inc_name, inc_fullname)) %>%
    select(country, year, variable, value)
}

# use default name
(import_gapminder("data/indicator hiv estimated prevalence% 15-49 (1).xlsx"))
```

    ## # A tibble: 9,075 x 4
    ##                  country  year                                 variable
    ##                    <chr> <int>                                    <chr>
    ##  1              Abkhazia  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  2           Afghanistan  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  3 Akrotiri and Dhekelia  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  4               Albania  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  5               Algeria  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  6        American Samoa  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  7               Andorra  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  8                Angola  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ##  9              Anguilla  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ## 10   Antigua and Barbuda  1979 Estimated HIV Prevalence% - (Ages 15-49)
    ## # ... with 9,065 more rows, and 1 more variables: value <dbl>

``` r
# use custom label
(import_gapminder("data/indicator hiv estimated prevalence% 15-49 (1).xlsx",
                  inc_name = "hiv_15_49"))
```

    ## # A tibble: 9,075 x 4
    ##                  country  year  variable      value
    ##                    <chr> <int>     <chr>      <dbl>
    ##  1              Abkhazia  1979 hiv_15_49         NA
    ##  2           Afghanistan  1979 hiv_15_49         NA
    ##  3 Akrotiri and Dhekelia  1979 hiv_15_49         NA
    ##  4               Albania  1979 hiv_15_49         NA
    ##  5               Algeria  1979 hiv_15_49         NA
    ##  6        American Samoa  1979 hiv_15_49         NA
    ##  7               Andorra  1979 hiv_15_49         NA
    ##  8                Angola  1979 hiv_15_49 0.02652787
    ##  9              Anguilla  1979 hiv_15_49         NA
    ## 10   Antigua and Barbuda  1979 hiv_15_49         NA
    ## # ... with 9,065 more rows

Can I use some different data?
------------------------------

YES! If you have another dataset around which you want to build an interactive visualization, go ahead! Just let me know before you proceed.

Grading
-------

Your visualization will be evaluated using the following criteria:

-   Is it truthful?
-   Is it functional?
-   Is it beautiful?
-   Is it insightful?
-   Is it enlightening?

In addition to the visualization and accompanying narrative, write an approximately 500 word explanation defending your design choices. This includes (but is not limited to):

-   What is the story?
-   Why did you select this graphical form?
-   What is the role of interactivity? How does it enhance your ability to tell the story and communicate the data?

Submission details
------------------

Submit your visualization and analysis as a single R Markdown document rendered using the `html_document` output format. If you build a Shiny app, deploy it on [shinyapps.io](http://www.shinyapps.io) and include a link to it in your submission. Include your design explanation in a separate document.

Your submission is due by **1:30pm on Wednesday, May 10th**.

[1] Though with far less detail

[2] You could also rewrite it to directly import from Google Sheets using the `googlesheets` package, making it fully reproducible.