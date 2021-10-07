Resampling Methods
================

## P1

Summary: Download the file 5.R.RData and load it into R using
load(“5.R.RData”). Consider the linear regression model of y on X1 and
X2. What is the standard error for beta1?

Task 1: Load data into R.

``` r
load("5.R.RData")
summary(Xy)
```

    ##        X1                X2                y           
    ##  Min.   :-0.8068   Min.   :-1.1753   Min.   :-0.75293  
    ##  1st Qu.:-0.1674   1st Qu.:-0.2339   1st Qu.:-0.06136  
    ##  Median : 0.2798   Median : 0.1824   Median : 0.30452  
    ##  Mean   : 0.3337   Mean   : 0.1288   Mean   : 0.35471  
    ##  3rd Qu.: 0.7017   3rd Qu.: 0.4646   3rd Qu.: 0.73283  
    ##  Max.   : 1.8531   Max.   : 1.7658   Max.   : 2.03922

Task 2: Solve the linear regression model of y on x1 and x2.

``` r
fit=lm(y~., data = Xy)
fit
```

    ## 
    ## Call:
    ## lm(formula = y ~ ., data = Xy)
    ## 
    ## Coefficients:
    ## (Intercept)           X1           X2  
    ##      0.2658       0.1453       0.3134

``` r
out <- summary(fit)
```

Task 3: Return the standard error for beta1.

``` r
out$coefficients[2 ,2]
```

    ## [1] 0.02593295

Task 4: Next, plot the data using matplot(Xy,type=“l”).

``` r
matplot(Xy,type="l")
```

![](ResamplingMethods_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## P2

Summary: Now, use the (standard) bootstrap to estimate the standard
error for beta1. To within 10%, what do you get?

Task 1: Load libraries.

``` r
require(ISLR)
```

    ## Loading required package: ISLR

``` r
require(boot)
```

    ## Loading required package: boot

Task 2: Define bootstrap function.

``` r
boot.fn=function(data, index) + return(coef(lm(y~., data=data, subset=index)))
```

Task 3: Perform bootstrap.

``` r
set.seed(1)
boot.fn(Xy, sample(1:1000, 1000, replace = TRUE))
```

    ## (Intercept)          X1          X2 
    ##   0.2714016   0.1668250   0.3821881

``` r
out2 <- boot(Xy, boot.fn, 1000)
```

Task 4: Return the standard error for beta1.

``` r
out2
```

    ## 
    ## ORDINARY NONPARAMETRIC BOOTSTRAP
    ## 
    ## 
    ## Call:
    ## boot(data = Xy, statistic = boot.fn, R = 1000)
    ## 
    ## 
    ## Bootstrap Statistics :
    ##      original        bias    std. error
    ## t1* 0.2658349 -8.140169e-06  0.01441209
    ## t2* 0.1453263  1.181540e-03  0.02847033
    ## t3* 0.3133670  1.670687e-03  0.03526574

``` r
sd(out2$t[ , 2])
```

    ## [1] 0.02847033

## P3

Summary: Finally, use the block bootstrap to estimate . Use blocks of
100 contiguous observations, and resample ten whole blocks with
replacement then paste them together to construct each bootstrap time
series. For example, one of your bootstrap resamples could be:

new.rows = c(101:200, 401:500, 101:200, 901:1000, 301:400, 1:100, 1:100,
801:900, 201:300, 701:800)

new.Xy = Xy\[new.rows, \]

To within 10%, what do you get?

Task 1: Define time series bootstrap function.

``` r
newboot.fn=function(data) + return(coef(lm(y~., data=data)))
```

Task 2: Perform bootstrap.

``` r
set.seed(1)
out3 <- tsboot(Xy, statistic = newboot.fn, R = 1000, l = 100, sim = "fixed")
```

Task 3: Return the standard error for beta1.

``` r
out3
```

    ## 
    ## BLOCK BOOTSTRAP FOR TIME SERIES
    ## 
    ## Fixed Block Length of 100 
    ## 
    ## Call:
    ## tsboot(tseries = Xy, statistic = newboot.fn, R = 1000, l = 100, 
    ##     sim = "fixed")
    ## 
    ## 
    ## Bootstrap Statistics :
    ##      original        bias    std. error
    ## t1* 0.2658349 -0.0011871649  0.09355643
    ## t2* 0.1453263 -0.0008227603  0.19505853
    ## t3* 0.3133670  0.0617663497  0.30066695

``` r
sd(out3$t[ , 2])
```

    ## [1] 0.1950585
