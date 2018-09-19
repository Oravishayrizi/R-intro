---
title: "R intro"
author:
  name: Grant R. McDermott & Ed Rubin
  affiliation: University of Oregon
  # email: grantmcd@uoregon.edu
date: "19 September 2018"
output:
  html_document:
    theme: flatly
    highlight: haddock
    # code_folding: show
    toc: yes
    toc_depth: 3
    toc_float: yes
    keep_md: true
---

# Installing `R`

__`R`__ To use `R`, navigate your browser to [cran.r-project.org](https://cran.r-project.org). Download. You're ready to use `R`.^[CRAN is the central `R` repository.]

__RStudio__ Most `R` users interact with `R` through an IDE called `RStudio`. Navigate to [https://www.rstudio.com/products/rstudio/](https://www.rstudio.com/products/rstudio/) and download the desktop IDE. Now you're really ready.

# Differences between `R` and `Stata`

Relative to `Stata`, `R` introduces a few new dimensions:

1. `R` is an object-oriented language, in which objects have types.
2. `R` uses packages (a.k.a. _libraries_).
3. Working with matrices in `R` is easy (and common).
4. `R` tries to guess what you meant.
5. `R` easily (and infinitely) parallelizes.
6. `R` plays nicely with with `Markdown`.
7. `R` is free.^[In a monetary sense.]

## `R` is an object-oriented language, in which objects have types

- You hold multiple (many) objects in memory at the same time.
- No more `preserve`, `snapshot`, `restore` if you have multiple datasets.
- Defining objects is a thing:
  - `a <- 3`
  - `b <- matrix(1:4, nrow = 2)`
  - `<-` is read aloud as _gets_
- Object types matter: _e.g._, a `matrix` is a bit different from `data.frame` or a `vector`. [More](http://edrub.in/ARE212/section02.html#data_structures_in_r).

## `R` uses packages

Just as `LaTeX` uses packages (_i.e._, `\usepackage{foo}`), `R` also draws upon non-default packages (`library(foo)`)

- `R` loads with a set of default packages called the `base` installation—the most commonly used packages across all of the use cases.
- __Important:__ `R` intends for you to go get more packages—don't constrain yourself.
- __Install a package:__
  - `install.packages("package.name")`
  - Notice that the package's name is in quotes.^[R uses single (`'word'`) and double quotes (`"word"`) to reference characters (strings).]
- __Load a package:__
  - `library(package.name)`
  - Notice that you don't need quotation marks now.^[Why? Once you've installed the package, `R` treats it as an object rather than a character.]
- If you want to get really _meta_: the `pacman` (package management) package helps you... manage packages.

## Working with matrices

Because R began its life as a statistical language/environment, it plays very nicely with matrices.

__Create a matrix:__ `R matrix(data = c(3, 2, 4, 3), ncol = 3)`

__Assign (store) a matrix:__ `R A <- matrix(data = c(3, 2, 4, 3), ncol = 3)`

__Invert a matrix:__ `R solve(A)`

## `R` tries to guess what you meant

R is friendly and likes to try to help.^[This desire to help can sometimes hide programming mistakes.]

`TRUE + TRUE`

The answer? `2`.

`lm(wage ~ education + gender, data = mincer)`

Our regression can use variable like `gender` that takes values `"female"` and `"male"`. `R` knows what you mean: you want indicator variables for the levels of the variable.

## `R` easily (and infinitely) parallelizes

Parallelization is pretty easy with packages like `pbapply`, `parallel`, and `future`.

Example of a simulation:


```r
# Set our seed
set.seed(12345)
# Set sample size
n <- 1e6
# Generate 'x' and 'e'
our_data <- data.frame(x = rnorm(n), e = rnorm(n))
# Calculate 'y'
our_data$y <- 3 + 2 * our_data$x + our_data$e
# Function that draws a sample of 10,000 observations and runs a regression
our_reg <- function(i) {
  # Sample the data
  sample_data <- our_data[sample.int(n = n, size = 1e4, replace = T),]
  # Run the regression
  lm(y ~ x, data = sample_data)$coef[2]
}
```

Simulation without parallelization:


```r
# Set seed
set.seed(1234)
# Start timer
t1a <- proc.time()
# 1,000-iteration simulation
sim1 <- lapply(X = 1:1e4, FUN = our_reg)
# Stop timer
t1b <- proc.time()
# How long did it take?
t1b - t1a
```

```
##    user  system elapsed 
##  97.847   4.280 102.296
```

Simulation with parallelization (15 cores):


```r
# Load the 'pbapply' package (must be installed first)
library(pbapply)
# Set seed
set.seed(1234)
# Start timer
t2a <- proc.time()
# 1,000-iteration simulation
sim2 <- pblapply(X = 1:1e4, FUN = our_reg, cl = 15)
# Stop timer
t2b <- proc.time()
# How long did it take?
t2b - t2a
```

```
##    user  system elapsed 
## 124.748   5.567  18.632
```

Further, many packages in R default (or have options) to work in parallel. _E.g._, the regression package `lfe` uses the available processing power to estimate fixed-effect models.

__Note:__ This parallelization often means that you move away from `for` loops and toward parallelized replacements (_e.g._, `lapply` has many parallelized implementations).^[Though there are parallelized `for` loop versions.]^[[More](http://edrub.in/ARE212/section05.html).]

## `R` plays nicely with with `Markdown`

Notes, websites, presentations can all easily include

code chunks

```r
# Some amazing code
2 + 2
2 - 2
2 * 2
2 / 2
```

evaluated code

```r
"Ernie" > "Burt"
```

```
## [1] TRUE
```

and normal/mathematical text $\left(\text{e.g., }\dfrac{x^2}{3}\right)$.

## `R` is free

So we can use money for other things.