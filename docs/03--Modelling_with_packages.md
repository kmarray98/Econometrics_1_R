---
output:
  pdf_document: default
  html_document: default
---
# Modelling 1 - estimators from packages

Now we know how to deal with data, we can think about fitting some models!
There are two main ways to do this in R: using routines in packages, or
programming the mathematical operations yourself using matrices. Both have their
upsides and downsides. R is very good at the first, and still very decent - though
not the best language out there - for the second.

The first is typically a lot easier. The main upside of
R is its amazing array of different packages containing almost every model you
would like to use, with lots of options and community support.
The downside is that you have less control. Someone else has decided what summary
statistics to report, which optimiser to use, how to deal with NAs and so on. These
are not always what you would like. We should note, however, that even the package
functions in R are typically much more flexible than alternatives like Stata. It
is much easier to work out, and change, what is going on under the hood. This makes
R a great choice for applied statistics, econometrics, and data science, and is 
part of why R is so popular in these communities. 

The second is typically a bit harder to do. You have to know how to program the
models, all your desired summary statistics etc yourself. The upside is that 
you can choose exactly what you want to do and how to do it. Also, R is less
optimised for matrix operations than dedicated high-performance computing languages like
Julia, or Python 3 with Numba.
Thus, if you only want to program your own estimators and never use packages, maybe
consider these instead (see https://julialang.org/ or https://numba.pydata.org/)

We are going to show you how to do the first in this section. We deal with the second in the next section. 
We will introduce
modelling with inbuilt functions by introducing the `lm()` function. Through it, we are going to encounter some useful tips and tricks
for using inbuilt functions. 

The `lm` function is how
we perform OLS in R. Most of the models you will encounter in R packages are built
off of this base function. Through it, we are going to encounter some useful tips and tricks
for using inbuilt functions. Thus, there are high returns to knowing how it works and reports
summary statistics. Thus, we will take some time to go through how it works,
how to access all of its outputs from the summary objects, and some tricks for using it.
Next, we will cover how to compute different covariance matrices using the  `sandwich`
package. After this, we will see how to automatically  make nice latex regression tables from lm objects
and `sandwich` standard errors with `stargazer()`.



```r
# lets read in a new dataframe


# read.csv allows us to read a csv file into R. The `as.data.frame' wrapper
# tells R that we want the object to be represented as a dataframe

# For illustration, we are going to use some of the data from Dell and Querubin (2017)
# `Nation building through foreign interventions'. This estimates the causal effect of 
# military firepower on insurgent support on the intensive margin 
# discontinuities in US strategies across regions in South Vietnam during their
# occupation. We have observations by hamlet 


# To download the original data, go to
# https://scholar.harvard.edu/files/dell/files/nationbuilding.pdf
# The file we will use is there as `firstclose_post.dta'
df <- as.data.frame(read.csv("vietnam_war.csv", stringsAsFactors = F))
```


## Linear regression with `lm()`

`lm()` is the command, included in base R, that allows us to perform linear regression.
To carry out a regression, we take a dataframe object including all of our dependent
and independent variables. 

The first argument to `lm()` is a 'formula'. This is
the formula for our regression model. We specify it with the following syntax:
'dependent_variable ~ independent variable 1 + independent  variable 2 + ... +
independent variable n'. The variable names have to be the same as the names of the
variables in the dataframe. Otherwise, `lm()` will not recognise the name of the
variable. We do not have to pass the formula or variable names as
strings - we just write them out in text, and `lm()` finds the variable with
that name in our dataframe. `lm()` automatically includes an intercept. If we
want to exclude this, write 0 as the first variable in our formula.

The second argument to `lm()` is 'data'. We need to pass the name of the dataframe
containing our data. It is important to know what type of vector each of your
variables is within the dataframe. If your variable is one of the numeric vector types, 
`lm()` will treat it as a continuous variable. If it is a factor, it will treat it
as a categorical variable, with an ordering given by the ordering of the underlying
factor. Thus, it will include a series of dummy variables or 'fixed effects' for each
level of the factor, omitting the one corresponding to the lowest level to avoid 
pure multicolinearity.

This is basically it! There are other additional arguments that allow you to
specify vectors of weights for weighted least squares, different fitting methods,
and so on. 


```r
# Lets run a few different linear regressions with lm

# regressing mean airstrikes on whether the hamlet was above or below the algorithm
# scoring threshold US planners used to assign planned airstrikes

lm(fr_strikes_mean ~ below, data=df)
```

```
## 
## Call:
## lm(formula = fr_strikes_mean ~ below, data = df)
## 
## Coefficients:
## (Intercept)        below  
##    0.257591     0.009856
```

```r
# lets drop the intercept

lm(fr_strikes_mean ~ 0 + below, data=df)
```

```
## 
## Call:
## lm(formula = fr_strikes_mean ~ 0 + below, data = df)
## 
## Coefficients:
##  below  
## 0.2674
```

```r
# regressing mean airstrikes on distance from the threshold with a second-order
# polynomial trend 

# we will save this as an object m2 so we can take a look at it later

df[["md_ab_square"]] <- df[["md_ab"]]^2

m2 <- lm(fr_strikes_mean ~ md_ab + md_ab_square, data=df)

# now lets use factors to add year fixed effects

m_time <-lm(fr_strikes_mean ~ as.factor(yr) +md_ab + md_ab_square, data=df)
```
As we can see, the output from the `lm()` command by itself it quite ugly. It is
better to save it as a variable, and then we can look at it more nicely and do
things with it. The first thing is to make it look a (little) nicer. We can
do this using the base R `summary` function. Running summary(model_name) 
will output us a regression table containing OLS standard errors, p-values, R-squared,
and the F statistic for joint significance of all coefficients.


```r
summary(m2)
```

```
## 
## Call:
## lm(formula = fr_strikes_mean ~ md_ab + md_ab_square, data = df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -0.3079 -0.2412 -0.1059  0.1609  1.0993 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    0.305918   0.002982  102.59  < 2e-16 ***
## md_ab         -0.303735   0.043391   -7.00 2.69e-12 ***
## md_ab_square -11.375115   0.345114  -32.96  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2946 on 12204 degrees of freedom
## Multiple R-squared:  0.08179,	Adjusted R-squared:  0.08164 
## F-statistic: 543.5 on 2 and 12204 DF,  p-value: < 2.2e-16
```

```r
# lets save it as an object so we can inspect it later too

s2 <- summary(m2)
```

Now, imagine that we do not know what the arguments for `summary()` are. We want
a way of looking up what they are, how the function works, and so on. Luckily,
RStudio has a nice inbuilt help function we can use to do this. We put a `?` before
the name of the function and run this. This produces the documentation for the
function in the viewer on the right hand side of the screen. The documentation
tells us the arguments of the function, additional details, and some examples of
how we might use it.


```r
# Lets have a look at the help function here for lm

?summary
```

```
## starting httpd help server ... done
```

Imagine here that we had multiple objects called `summary` from different packages
that we might use. We can specify which we want to use by preceeding the function
with the package name and a double colon `::`. Our version of `summary` is from base R - the set of functions
that come with the R distribution itself. To call that `summary` specifically, we
can run `base::summary`. 


```r
# Some examples of finding the documentation for a specific function

?base::summary

# now imagine we want the documentation for the group_by function from the 
# package dplyr

?dplyr::group_by

# notice we can get the documentation without actually loading the package!
# But if we wanted to actually run the function we would need to load
# the package of course
```


`lm()` returns a special object of class `lm()`. This object contains all of the
relevant model coefficients, residuals, and fitted values. The `summary` object
also contains all of the ouputs we see above.

Both of these objects are structured as lists of lists. The whole object itself
is a list, and each of the relevant outputs - e.g the coefficients - are a list
within that list. The objects also store things like the variable names of each 
coefficient. The list structure and attached metadata means that we can slice the object using
similar slicing techniques to the ones we learned earlier to extract what we
want. This can be useful if, for example, we want to do some plotting, get
a single test statistic, or use the coefficient values to predict out of sample.



```r
# lets get some results from our regressions
# imagine for example that we want to plot the residuals to look for some
# heteroskedasticity

resids <- s2[["residuals"]]
plot(df[["md_ab"]], resids,  col="red", xlab="Distance from threshold", ylab="Residual",
     main = "Regression residuals against distance from score threshold")
```

<img src="03--Modelling_with_packages_files/figure-html/unnamed-chunk-6-1.png" width="672" />

```r
# we want to look at the 35th residual from this model for some reason
s2[["residuals"]][35]
```

```
##         35 
## 0.04256734
```

```r
# or we just want to look at the coefficient on md_ab

m2[["coefficients"]][["md_ab"]]
```

```
## [1] -0.303735
```
Most other regression routines in R either use a version of this function and output  structure, 
or something deliberately very similar (e.g `plm()` for linear
panel data models, `pgmm()` for panel data GMM models, `dynlm()` for time series).


```r
# An example of other lm-type objects in R - IV models

# IV regression in R

library(AER)
```

```
## Loading required package: car
```

```
## Loading required package: carData
```

```
## Loading required package: lmtest
```

```
## Loading required package: zoo
```

```
## 
## Attaching package: 'zoo'
```

```
## The following objects are masked from 'package:base':
## 
##     as.Date, as.Date.numeric
```

```
## Loading required package: sandwich
```

```
## Loading required package: survival
```

```
## Warning: package 'survival' was built under R version 4.0.5
```

```r
# lets do a fuzzy-rdd type IV regression to demonstrate
# We look at the causal effect of airstrikes on local insurgent infiltration
# using distance from the threshold as an instrument for airstrikes (and including
# a quadratic trend)

# syntax is second stage regression|dependent variables in first stage

m_iv <- ivreg(guer_squad ~fr_strikes_mean + md_ab_square|md_ab + md_ab_square, data=df)
s_iv <- summary(m_iv)

# see how this package uses the base lm object to build up the more complex 
# econometric model, so that we can slice the resulting object in the same
# way as before
```


The `lm` object can also evaluate the model as strings. Thus, a neat trick we
can use to fit large models or many similar models is to construct the model formula 
as a string using `paste()`, and then pass that string to the formula argument
of `lm()`.


```r
# Imagine we want to include the variables in columns 4:50 of our dataframe into 
# our model as regressors

# this syntax may look intimidating, so lets look at it piece by piece

# As we learned when we looked at slicing earlier, df[,c(4:50)] will select
# all rows (as the left hand side of the comma is empty) of columns 4:50 (as we
# pass the vector of numbers 4:50 on the right hand side of the column) from our
# dataframe df.

# names() returns the column name of a given dataframe object. Thus, names(df[,c(4:50)])
# returns a vector of names of all of the columns in our slice as strings

# paste() takes in vectors of strings, or multiple strings manually, and returns
# a single string. If we specify the 'collapse' argument, it takes any vector
# of strings and returns a single string made up of each of the elements of the 
# vector pasted together, and separated by the thing we pass to 'collapse'. In 
# this case, it takes all of our names(df[,c(4:50)]) and puts them in a single
# string, each separated by a '+' sign

dep_vars <- paste(names(df[,c(4:50)]), collapse="+")

# next we need to add our independent variable. We can do this with 'paste' again.
# If we specify 'sep' and pass strings as individual arguments (instead of a vector),
# paste takes each string we pass and puts them together in
# one string separated by the symbol we pass to 'sep'.

formula_string <- paste("fr_strikes_mean", dep_vars, sep="~")

# now we have our formula, we can run the regression model

m_string <- lm(formula_string, data=df)
summary(m_string)
```

```
## 
## Call:
## lm(formula = formula_string, data = df)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.92720 -0.11849 -0.02094  0.08878  0.92147 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)          4.460e-02  2.955e-02   1.509 0.131249    
## fr_forces_mean       1.651e-01  7.819e-03  21.110  < 2e-16 ***
## pop_g               -1.426e-01  1.472e-02  -9.687  < 2e-16 ***
## naval_attack         6.203e-02  8.221e-02   0.755 0.450564    
## sh_pf_presence      -1.430e-02  6.744e-03  -2.120 0.033994 *  
## sh_rf_presence      -3.333e-02  1.163e-02  -2.866 0.004162 ** 
## fr_init             -8.907e-02  5.815e-02  -1.532 0.125634    
## fw_init             -1.415e+00  4.552e-01  -3.108 0.001890 ** 
## en_d                 3.473e-05  1.881e-04   0.185 0.853526    
## fw_d                -3.587e-02  1.168e-02  -3.073 0.002128 ** 
## fr_d                 7.137e-04  1.154e-03   0.618 0.536382    
## fr_opday_dummy       1.011e-01  5.724e-02   1.765 0.077516 .  
## fw_opday_dummy       1.306e+00  4.589e-01   2.846 0.004434 ** 
## vc_infr_vilg        -7.916e-03  9.467e-03  -0.836 0.403053    
## part_vc_cont         8.758e-03  3.903e-02   0.224 0.822450    
## guer_squad           8.530e-02  7.902e-03  10.795  < 2e-16 ***
## mainforce_squad      1.286e-01  8.226e-03  15.629  < 2e-16 ***
## en_base              3.031e-03  8.903e-03   0.340 0.733517    
## entax_vilg           5.570e-02  8.042e-03   6.926 4.59e-12 ***
## phh_psdf            -1.480e-02  1.160e-02  -1.276 0.201842    
## psdf_dummy          -4.234e-02  1.197e-02  -3.538 0.000405 ***
## chief_visit          5.704e-02  1.352e-02   4.219 2.48e-05 ***
## village_comm        -4.277e-02  9.169e-03  -4.665 3.13e-06 ***
## gvn_taxes           -4.322e-02  7.493e-03  -5.767 8.30e-09 ***
## rdc_active          -5.534e-02  6.824e-03  -8.110 5.68e-16 ***
## civic_org_part      -6.545e-02  1.134e-02  -5.772 8.08e-09 ***
## vilg_council_meet    3.150e-02  6.653e-03   4.736 2.22e-06 ***
## youth_act            3.903e-03  8.058e-03   0.484 0.628098    
## p_own_vehic         -5.310e-02  1.635e-02  -3.248 0.001166 ** 
## nonrice_food         1.841e-02  9.894e-03   1.861 0.062810 .  
## manuf_avail         -1.264e-02  9.302e-03  -1.359 0.174230    
## surplus_goods        3.832e-02  6.667e-03   5.748 9.28e-09 ***
## econ_train           5.062e-02  1.235e-02   4.100 4.17e-05 ***
## self_dev_part       -2.065e-02  1.055e-02  -1.957 0.050347 .  
## selfdev_vilg        -6.659e-02  1.504e-02  -4.428 9.60e-06 ***
## pworks_under_constr  2.016e-02  7.014e-03   2.875 0.004049 ** 
## prim_access          3.258e-02  1.087e-02   2.996 0.002741 ** 
## sec_school_vilg      3.092e-02  6.321e-03   4.891 1.02e-06 ***
## p_require_assist     4.766e-02  2.051e-02   2.323 0.020192 *  
## nofarm_sec           4.624e-02  7.037e-03   6.571 5.24e-11 ***
## urban               -4.235e-02  1.035e-02  -4.091 4.33e-05 ***
## buddhist             1.146e-02  5.236e-03   2.189 0.028649 *  
## farming             -1.872e-02  7.110e-03  -2.633 0.008482 ** 
## all_atk              2.945e-01  1.794e-02  16.418  < 2e-16 ***
## en_pres              1.711e-01  1.821e-02   9.399  < 2e-16 ***
## en_prop             -1.060e-01  2.338e-02  -4.533 5.89e-06 ***
## admin_p1             1.017e-01  2.800e-02   3.634 0.000281 ***
## health_p1           -4.880e-02  8.736e-03  -5.586 2.39e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2027 on 9761 degrees of freedom
##   (2398 observations deleted due to missingness)
## Multiple R-squared:  0.5178,	Adjusted R-squared:  0.5155 
## F-statistic:   223 on 47 and 9761 DF,  p-value: < 2.2e-16
```



## Computing covariance matrices with `sandwich`

`lm` only computes standard errors based on the standard OLS variance-covariance matrix.
We often want to do something different, for example computing Newey-West standard
errors. The package sandwich allows us to easily compute these kind of 'sandwich' 
covariance matrices. 

There are multiple different `sandwich` commands, corresponding to different types
of covariance matrix. Generally, we need to pass these object a fitted model object
like our `lm()` model. Next, we can then specify whether we need to do a finite
sample adjustment (if we want to make our output consistent with Stata, which
often does this automatically), if we want to pass a weights matrix, and so on.
If we want to specify the outsides and insides of the covariance matrix structure
manually, we can do so using `bread()` and `meat()`.



```r
# Lets compute some different types of covariance
# matrices with sandwich

library(sandwich)

# heteroskedasticity and autocorrelation robust covariance matrix

m2_HAC_cov_mat <- vcovHAC(m2)

# Panel-corrected covariance matrix

m2_pan_cov_mat <- vcovPC(m2, cluster=df[["usid"]])

# now if we want to get the standard errors back, we need to take the square root
# of the diagonal of the matrix

m2_HAC_ses <- sqrt(diag(m2_HAC_cov_mat))
```

Once we have these, we might want to look quickly at which variables are significant.
We can do this by using the `coeftest()` function from the package `lmtest`. The
package `lmtest` contains functions for lots of specification tests for different
linear models (to name three examples: likelihood ratio tests, reset tests, and tests for Granger causality). 
`coeftest()` is a counterpart
to the `lm()` object. We pass it a `lm()` object and a method for computing a 
sandwich covariance matrix. The function then prints out the object summary with the appropriate significance
levels and p-values from our covariance matrix.


```r
library(lmtest)

coeftest(m2, vcov=vcovHAC)
```

```
## 
## t test of coefficients:
## 
##                 Estimate  Std. Error  t value  Pr(>|t|)    
## (Intercept)    0.3059175   0.0098534  31.0469 < 2.2e-16 ***
## md_ab         -0.3037350   0.0645894  -4.7026 2.597e-06 ***
## md_ab_square -11.3751147   0.6366604 -17.8668 < 2.2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

