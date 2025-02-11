# Multiple Regression

This R markdown document provides an example of performing a multiple
regression using the lm() function in R and compares the output with the
linReg() function in the jmv (Jamovi) package.

## Package management in R

``` r
# keep a list of the packages used in this script
packages <- c("tidyverse","rio","jmv")
```

This next code block has eval=FALSE because you don’t want to run it
when knitting the file. Installing packages when knitting an R notebook
can be problematic.

``` r
# check each of the packages in the list and install them if they're not installed already
for (i in packages){
  if(! i %in% installed.packages()){
    install.packages(i,dependencies = TRUE)
  }
  # show each package that is checked
  print(i)
}
```

``` r
# load each package into memory so it can be used in the script
for (i in packages){
  library(i,character.only=TRUE)
  # show each package that is loaded
  print(i)
}
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.5     v dplyr   1.0.3
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.0

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## [1] "tidyverse"
    ## [1] "rio"
    ## [1] "jmv"

## Multiple Regression

Multiple regression is predicting a continuous outcome variable
(dependent variable) with several predictor variables (independent
variables). You can perform regressions using categorical predictor
variables, but we’ll talk more about that later.

## Open data file

The rio package works for importing several different types of data
files. We’re going to use it in this class. There are other packages
which can be used to open datasets in R. You can see several options by
clicking on the Import Dataset menu under the Environment tab in
RStudio. (For a csv file like we have this week we’d use either From
Text(base) or From Text (readr). Try it out to see the menu dialog.)

``` r
# Using the file.choose() command allows you to select a file to import from another folder.
dataset <- rio::import(file.choose())
# dataset <- rio::import("Album Sales.sav")
```

## Get R code from Jamovi output

You can get the R code for most of the analyses you do in Jamovi.

1.  Click on the three vertical dots at the top right of the Jamovi
    window.
2.  Click on the Syndax mode check box at the bottom of the Results
    section.
3.  Close the Settings window by clicking on the Hide Settings arrow at
    the top right of the settings menu.
4.  you should now see the R code for each of the analyses you just ran.

## lm() function in R

Many linear models are calculated in R using the lm() function. We’ll
look at how to perform a multiple regression using the lm() function
since it’s so common.

#### Visualization

``` r
# This code creates a scatter matrix
library(GGally)
GGally::ggpairs(dataset, columns=c('Sales','Adverts','Airplay','Image'), lower = list(continuous = "smooth"))
```

``` r
# This code creates a scatterplot between a single pair of variables
ggplot(dataset, aes(x = Adverts, y = Sales)) +
  geom_point() +
  stat_smooth(method = lm)
```

#### Computation

``` r
model <- lm(formula = Sales ~ Adverts + Airplay + Image, data = dataset)
model
```

#### Model assessment

``` r
summary(model)
```

#### Standardized residuals from lm()

You might notice lm() does not provide the standardized residuals. Those
must me calculated separately.

``` r
standardized = lm(scale(Sales) ~ scale(Adverts) + scale(Airplay) + scale(Image), data=dataset)
summary(standardized)
```

## function in Jamovi

Compare the output from the lm() function with the output from the
function in the jmv package.

``` r
jmv::linReg(
  data = dataset,
  dep = Sales,
  covs = vars(Adverts, Airplay, Image),
  blocks = list(list("Adverts"),
                list("Airplay","Image")),
  refLevels = list(),
  modelTest = TRUE,
  anova = TRUE,
  ci = TRUE,
  stdEst = TRUE,
  ciStdEst = TRUE)
```
