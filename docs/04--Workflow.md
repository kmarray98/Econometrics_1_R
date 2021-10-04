---
output:
  pdf_document: default
  html_document: default
---
# Workflow

We have computed our models. Now we need to write up our results and put them
in nice tables. If you want to do this from scratch, it can be really annoying.
If you do not believe me, try writing the regression tables out by hand in TeX
without looking at the output. Furthermore, you have to do it every time you
change your model, change variables, or fix a bug. 

Luckily, we can automate this all in R! Below, we go through how to automatically
output journal-quality LaTeX or HTML tables in R every time you run your code. 
This gives us two ways of nicely automating our workflow. Firstly, if you want
to write your document in a LaTeX, you can install a local LaTeX editor on 
your machine (I personally prefer TeXMaker https://www.xm1math.net/texmaker/) and
output TeX tables to the same location you store your TeX file. Then, whenever you
run your code, it will also update the tables in your LaTeX document. Otherwise,
we can actually write the entire document in R as something called an RMarkdown
file (like these notes). Then, as we have here, we can write our report and code
in the same document and thus see our tables and so on directly in the document. 

We first cover how to automate creating tables using Stargazer. We then demonstrate
how to do it for a general dataframe using XTable. We do not have the space to
do RMarkdown justice here, so take a look at the introductory files on their
website https://rmarkdown.rstudio.com/lesson-1.html. 

## Regression tables with Stargazer

When we have our lm-type output object, and our standard errors, we want to
put them in a nice regression table with the appropriate specification tests
that we can put in our report or paper. Typically, this will be a LaTeX table.
The most common way to automat this is the `stargazer` package we saw
briefly earlier. It is very flexible, giving us lots of choice
what to include or exclude, and supports both Tex and HTML tables.

The basic command in stargazer is `stargazer()`. This is the command to create
the table. As the first argument, we pass a single regression model or a list
of regression models. If we pass a list of regression models, it will automatically
put each model in its own column in the table.


```r
library(stargazer)
```

```
## 
## Please cite as:
```

```
##  Hlavac, Marek (2018). stargazer: Well-Formatted Regression and Summary Statistics Tables.
```

```
##  R package version 5.2.2. https://CRAN.R-project.org/package=stargazer
```

```r
# To download the original data, go to
# https://scholar.harvard.edu/files/dell/files/nationbuilding.pdf
# The file we will use is there as `firstclose_post.dta'

library(sandwich)
library(stargazer)

#setwd("C://Users//kiera//OneDrive//Documents//Tinbergen MPhil//Econometrics_1_TA//intro_R_TI")
df <- as.data.frame(read.csv("vietnam_war.csv", stringsAsFactors = F))

df[["md_ab_square"]] <- df[["md_ab"]]^2

m2 <- lm(fr_strikes_mean ~ md_ab + md_ab_square, data=df)

stargazer(m2)
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:12:55
## \begin{table}[!htbp] \centering 
##   \caption{} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{\textit{Dependent variable:}} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
## \hline \\[-1.8ex] 
##  md\_ab & $-$0.304$^{***}$ \\ 
##   & (0.043) \\ 
##   & \\ 
##  md\_ab\_square & $-$11.375$^{***}$ \\ 
##   & (0.345) \\ 
##   & \\ 
##  Constant & 0.306$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.082 \\ 
## Adjusted R$^{2}$ & 0.082 \\ 
## Residual Std. Error & 0.295 (df = 12204) \\ 
## F Statistic & 543.513$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.1; $^{**}$p$<$0.05; $^{***}$p$<$0.01} \\ 
## \end{tabular} 
## \end{table}
```
By default, stargazer returns the code for a LaTeX table object with the basic 
standard errors.

There are many, many different arguments we can specify to change the style of
the table, titles and variable names, what we include or omit, and different 
standard errors. Go and look at the package vignette at https://cran.r-project.org/web/packages/stargazer/vignettes/stargazer.pdf
for a list of all of the arguments and commands. In the code section below, we will
demonstrate some of the most common and useful ones.


```r
# Lets use the m2 and m_time models from before to demonstrate how Stargazer
# works

# now lets use factors to add year fixed effects

m_time <-lm(fr_strikes_mean ~ as.factor(yr) +md_ab + md_ab_square, data=df)

m2_HAC_ses <- sqrt(diag(vcovHAC(m2)))


# lets add some labels to start with and report coefficients with two digits
# these need to be written in the appropriate TeX 
# write all backslashes '\' as '\\' instead


stargazer(m2, title = "Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets",
          dep.var.caption = "Dependent variable: Mean foreign airstrikes",
          intercept.bottom=T, digits = 2,
          covariate.labels = c("$\\text{Distance}^{2}$", "$\\text{Distance}$^{2}"))
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:12:59
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{Dependent variable: Mean foreign airstrikes} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
## \hline \\[-1.8ex] 
##  $\text{Distance}^{2}$ & $-$0.30$^{***}$ \\ 
##   & (0.04) \\ 
##   & \\ 
##  $\text{Distance}$^{2} & $-$11.38$^{***}$ \\ 
##   & (0.35) \\ 
##   & \\ 
##  Constant & 0.31$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.08 \\ 
## Adjusted R$^{2}$ & 0.08 \\ 
## Residual Std. Error & 0.29 (df = 12204) \\ 
## F Statistic & 543.51$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.1; $^{**}$p$<$0.05; $^{***}$p$<$0.01} \\ 
## \end{tabular} 
## \end{table}
```

```r
# now lets manually specify the cutoffs for significance stars

stargazer(m2, title = "Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets",
          dep.var.caption = "Dependent variable: Mean foreign airstrikes",
          intercept.bottom=T, digits = 2,
          covariate.labels = c("$\\text{Distance}^{2}$", "$\\text{Distance}$^{2}"),
          star.cutoffs = c(0.05,0.01,0.001))
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:12:59
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{Dependent variable: Mean foreign airstrikes} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
## \hline \\[-1.8ex] 
##  $\text{Distance}^{2}$ & $-$0.30$^{***}$ \\ 
##   & (0.04) \\ 
##   & \\ 
##  $\text{Distance}$^{2} & $-$11.38$^{***}$ \\ 
##   & (0.35) \\ 
##   & \\ 
##  Constant & 0.31$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.08 \\ 
## Adjusted R$^{2}$ & 0.08 \\ 
## Residual Std. Error & 0.29 (df = 12204) \\ 
## F Statistic & 543.51$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.05; $^{**}$p$<$0.01; $^{***}$p$<$0.001} \\ 
## \end{tabular} 
## \end{table}
```

```r
# now we can add some different standard errors - our HAC standard errors from
# before

stargazer(m2, title = "Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets",
          dep.var.caption = "Dependent variable: Mean foreign airstrikes",
          intercept.bottom=T, digits = 2,
          covariate.labels = c("$\\text{Distance}^{2}$", "$\\text{Distance}$^{2}"),
          star.cutoffs = c(0.05,0.01,0.001), se.list = m2_HAC_ses)
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:12:59
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{Dependent variable: Mean foreign airstrikes} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
## \hline \\[-1.8ex] 
##  $\text{Distance}^{2}$ & $-$0.30$^{***}$ \\ 
##   & (0.04) \\ 
##   & \\ 
##  $\text{Distance}$^{2} & $-$11.38$^{***}$ \\ 
##   & (0.35) \\ 
##   & \\ 
##  Constant & 0.31$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.08 \\ 
## Adjusted R$^{2}$ & 0.08 \\ 
## Residual Std. Error & 0.29 (df = 12204) \\ 
## F Statistic & 543.51$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.05; $^{**}$p$<$0.01; $^{***}$p$<$0.001} \\ 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:12:59
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} ccc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $\text{Distance}^{2}$ & $\text{Distance}$^{2} & md\_ab\_square \\ 
## \hline \\[-1.8ex] 
## $0.01$ & $0.06$ & $0.64$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table}
```

```r
# now lets add another regression - including the time dummies - and choose
# not to display the dummies as they look messy

m_list <- as.list(m2, m_time)

# computing some HAC standard errors for these models
m_time_HAC_ses <- sqrt(diag(vcovHAC(m_time)))
ses <- as.list(m2_HAC_ses, m_time_HAC_ses)

stargazer(m_list, title = "Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets",
          dep.var.caption = "Dependent variable: Mean foreign airstrikes",
          column.labels = c("No time trend", "Time trend"),
          intercept.bottom=T, digits = 2,
          covariate.labels = c("$\\text{Distance}^{2}$", "$\\text{Distance}$^{2}"),
          star.cutoffs = c(0.05,0.01,0.001), se.list = ses, omit="yr")
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{Dependent variable: Mean foreign airstrikes} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
##  & No time trend \\ 
## \hline \\[-1.8ex] 
##  $\text{Distance}^{2}$ & $-$0.30$^{***}$ \\ 
##   & (0.04) \\ 
##   & \\ 
##  $\text{Distance}$^{2} & $-$11.38$^{***}$ \\ 
##   & (0.35) \\ 
##   & \\ 
##  Constant & 0.31$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.08 \\ 
## Adjusted R$^{2}$ & 0.08 \\ 
## Residual Std. Error & 0.29 (df = 12204) \\ 
## F Statistic & 543.51$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.05; $^{**}$p$<$0.01; $^{***}$p$<$0.001} \\ 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.01$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.06$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.64$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table}
```

```r
# finally, lets output it as a tex file called 'reg_table.tex'

stargazer(m_list, out="reg_table.tex", title = "Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets",
          dep.var.caption = "Dependent variable: Mean foreign airstrikes",
          column.labels = c("No time trend", "Time trend"),
          intercept.bottom=T, digits = 2,
          covariate.labels = c("$\\text{Distance}^{2}$", "$\\text{Distance}$^{2}"),
          star.cutoffs = c(0.05,0.01,0.001), se.list = ses, omit="yr")
```

```
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}}lc} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
##  & \multicolumn{1}{c}{Dependent variable: Mean foreign airstrikes} \\ 
## \cline{2-2} 
## \\[-1.8ex] & fr\_strikes\_mean \\ 
##  & No time trend \\ 
## \hline \\[-1.8ex] 
##  $\text{Distance}^{2}$ & $-$0.30$^{***}$ \\ 
##   & (0.04) \\ 
##   & \\ 
##  $\text{Distance}$^{2} & $-$11.38$^{***}$ \\ 
##   & (0.35) \\ 
##   & \\ 
##  Constant & 0.31$^{***}$ \\ 
##   & (0.003) \\ 
##   & \\ 
## \hline \\[-1.8ex] 
## Observations & 12,207 \\ 
## R$^{2}$ & 0.08 \\ 
## Adjusted R$^{2}$ & 0.08 \\ 
## Residual Std. Error & 0.29 (df = 12204) \\ 
## F Statistic & 543.51$^{***}$ (df = 2; 12204) \\ 
## \hline 
## \hline \\[-1.8ex] 
## \textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.05; $^{**}$p$<$0.01; $^{***}$p$<$0.001} \\ 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.01$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.06$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table} 
## 
## % Table created by stargazer v.5.2.2 by Marek Hlavac, Harvard University. E-mail: hlavac at fas.harvard.edu
## % Date and time: Sun, Oct 03, 2021 - 12:13:06
## \begin{table}[!htbp] \centering 
##   \caption{Effect of closeness to threshold on airstrike intensity in South Vietnamese hamlets} 
##   \label{} 
## \begin{tabular}{@{\extracolsep{5pt}} c} 
## \\[-1.8ex]\hline 
## \hline \\[-1.8ex] 
## $0.64$ \\ 
## \hline \\[-1.8ex] 
## \end{tabular} 
## \end{table}
```

## General tables with XTable

We might want to print something other than a regression table though as a LaTeX
table. In general, we can compute lots of different things we might want to show
our readers. If we can compute them, we can put them in a data frame. We just 
need a way to turn the dataframe into a nice LaTeX or HTML table. The `xtable`
package allows us to do so. 

The `xtable` package is built around the `xtable()` function. This function
creates a nice table in the format we want. Then, we need to `print()` it. This
allows us to save it as a file in the type of our choice. Again, there are lots
of arguments to both functions. We do not go through them all here - instead read
the documentation for the functions if you are interested.

We will demonstrate the functionality by creating a balancing table for some of
our data using the `RCT` package,


```r
# Lets construct a balancing table for hamlets occupied by the US Army vs USMC
library(data.table)
library(RCT)
```

```
## Warning: package 'RCT' was built under R version 4.0.5
```

```r
library(xtable)
df_mar <- as.data.frame(read.csv("marines_hamlet.csv"))

# creating a balacing table using the balance_table function from RCT
# first argument is the data frame containing all our variables we want
# to check, and the second argument is our treatment variable
# lets store it as a dataframe, and rename the columns so they look a bit
# nicer

sum_tbl <- as.data.frame(balance_table(df_mar[,c(4:7, 9:12, 42)], "treat"))
names(sum_tbl) <- c("Variable", "Mean - USMC hamlets", "Mean - US Army hamlets",
                    "P-value for difference in means")

# now lets store our balancing table in a nice format using the xtable 
# function

xtab <- xtable(sum_tbl, caption="Balancing table", digits=2)

# finally, lets store it as a TeX file using `print`
# First argument has to be the xtable object
# file argument gives the file name
# we can change the location in our computer by changing the
# working directory with setwd(file_path_as_string)

print(xtab, include.rownames=FALSE, file="sum_tbl.tex")
```


