--- 
title: "Introduction to R for Econometrics"
author: "Kieran Marray"
date: "2021-10-02"
site: bookdown::bookdown_site
documentclass: book
bibliography: [book.bib, packages.bib]
biblio-style: apalike
github_repo: kmarray98/Introduction_to_R_for_econometrics
link-citations: yes
description: ""
---

# Overview

## Motivation

This is a short introduction to R to go with the first year econometrics courses at the
Tinbergen Institute. It is aimed at people who are relatively new to programming,
or have only used programs like Stata before. It tries to give you enough knowledge
to easily program the estimators in the first year
econometrics sequence. 

We do not focus on telling you
how to program specific estimators. You can easily look these up online. But, it is
hard to actually implement an estimator in a programming language without knowing how the language works or how to work
with data in that language. When, you inevitably make an error or have to adapt the code you found
online you will be stuck. 


Thus, we instead we cover the fundamentals
of the language and working with data. The goal is that, by the end of the first
few chapters, you will be able to easily read in data, transform it into the
form you want, fit models based on routines others have built, and present their
output in a nice format. Most importantly,  when things inevitably go wrong,
you should be able to begin to interpret error
messages and adapt others' solutions you might find on websites like StackOverflow
to fit your needs. Of course, you will not be able to do this perfectly to begin
with. There is no substitute for experience. But hopefully this
introduction will give you a good start.

I have written these notes to try and ease the burden of the first year econometrics
sequence, based of the experience of others in my cohort. 
Many people have not done any programming before entering graduate school, or 
only used basic programs like Stata. This makes every single assignment a lot harder,
and much more work than it has to be. The questions are enough without struggling to
program the estimators as well.
Being able to flexibly write your own code pays large dividends.

This is a work in progress, and certainly not a definitive guide to R. If you see 
something that is wrong or that would be useful to add, please let me know. 

## Contents

In the first section, we introduce the basics of the R language In the second, we
go through some basic data analysis and cleaning: how to store data, extract variables etc. 
In the third, we go through how to implement models using functions that other 
people in the community have developed. We do this using the linear regression object, which
is at the core of many of the functions you will implement. At the end of this
section, we list many sets of functions that others have written that may help you
in your assignments. In the fourth, we
show how to take the results of our estimators and automatically generate nice reports.
In the fifth section, we cover some more advanced material that might be useful
if you would like to do more complicated things with R like programming your own
estimators. 

## Why R?

R is a free, open-source programming language specifically designed for statistical
programming. It is a great language to use for econometrics, data science, and statistics, 
as it combines the best parts of both 'pure' programming languages like Python and Julia
with the best parts of pre-built statistical software like Stata. R is based around
packages - sets of functions built by others in the community that you can load
and use yourself. This means that, unlike say Julia, you can easily find a routine
with good documentation that someone else has built to fit whatever econometric 
model you want. But unlike pre-built software like Stata, R is an actual programming 
language. Thus, you can transform your data, adapt others models, and so on
without it causing you a massive headache.

Furthermore, it is very widely used in academia and industry. This means that 
there is a large and vibrant community of R programmers online. They answer
questions, organise conferences, and even make podcasts where they talk about
data science and/or coffee  https://nssdeviations.com/ . It is
in high demand on the marketplace. For example, companies like Google
and Uber use R internally. 

R can initially be quite a frustrating language to use initially. If you are
getting annoyed initially, persist! Once you
begin to know it is is very rewarding and easy to use.

## Downloading R and RStudio

To use R, you first need to download it. I recommend downloading the latest 
release from here https://cran.r-project.org/src/base/R-4/. You should also
use an IDE (interactive development environment) to program in R initially. I
recommend downloading RStudio https://www.rstudio.com/products/rstudio/ .

## Further reading

There are many great R resources online. In particular, the Chief Scientist at 
RStudio, Hadley Wickham, makes lots of good guides to R available for free on
his website http://hadley.nz/.I recommend checking out `R for Data
Science'. Once you have done some programming in R, 
`Advanced R' is a really thorough overview of all the intricacies. RStudio publish
some great R cheatsheets https://www.rstudio.com/resources/cheatsheets/ . For econometrics
in particular, Nick Huntingdon-Klein has a great collection of resources
on his website https://nickchk.com/econometrics.html.


```
