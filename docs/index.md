--- 
title: "Introduction to R for Econometrics"
author: "Kieran Marray"
date: "`Econometrics 1 - Tinbergen Institute"
site: bookdown::bookdown_site
documentclass: book
bibliography: [book.bib, packages.bib]
biblio-style: apalike
github_repo: kmarray98/Introduction_to_R_for_econometrics.github.io
link-citations: yes
description: ""
---

# Overview

## Motivation

This is a short introduction to R to go with the first year econometrics courses at the
Tinbergen Institute. It is aimed at people who are relatively new to R, or programming in general. 

The goal is to give you enough of knowledge of the fundamentals of R to write and
adapt code to fit econometric models to data, and to simulate your own data, working
alone or with others. You will be able to easily read in data into a dataframe, plot it,
manipulate it into the form you want, fit models based on routines others have built, 
and present the results in a nice format. Most importantly,  when things inevitably go wrong,
you will be able to begin to interpret error
messages and adapt others' solutions
to fit your needs. Of course, this will not be easy to begin
with. There is no substitute for experience. But hopefully this
introduction will give you a good start.

This is a work in progress, and certainly not a definitive guide to R. If you see 
something that is wrong or that would be useful to add, please let me know. 

## Contents

In the first section, we introduce the basics - objects and functions you need to
know to work effectively in R.  In the second, we
go through some basic data analysis and cleaning: how to store data, plot it, and work with it. 
In the third, we go through how to implement models using packages - sets of functions that other 
people in the community have developed. We do this using the linear regression object, as it
is the core of many other econometric models. At the end of this
section, we list packages that others have written that may help you
in your assignments. In the fourth, we
show how to take the results of our estimators and automatically generate nice reports.
In the fifth section, we cover some more advanced material that might be useful
if you would like to do more complicated things with R like programming your own
estimators. 

## Why R?

R is a free, open-source programming language specifically designed for statistical
programming. It is a great language to use for econometrics, data science, and statistics, 
as it combines the best parts of both 'pure' programming languages like Julia
with the best parts of pre-built statistical software like Stata. R centers around
packages - sets of functions built by others in the community that you can load
and use yourself. This means that, unlike say Julia, you can easily find a routine
with good documentation that someone else has built to fit whatever econometric 
model you want without having to program it yourself. But R is an actual programming 
language unlike Stata or SPSS. Thus, you can write your own routines or perform basic data analysis
without it causing you a massive headache.

R is very widely used in academia and industry, for example in companies
like Google and Uber. This means that 
there is a large and vibrant community of R programmers online who are keen to 
help others with their problems. They answer
questions, organise conferences, and even make podcasts where they talk about
data science and/or coffee  https://nssdeviations.com/ .

## Downloading R and RStudio

To use R, you first need to download it. I recommend downloading the latest 
release from here https://cran.r-project.org/src/base/R-4/. You should also
use an IDE (interactive development environment) to program in R initially. I
recommend downloading RStudio https://www.rstudio.com/products/rstudio/ .

## Further reading

There are many great R resources online. In particular, the Chief Scientist at 
RStudio, Hadley Wickham, makes lots of good guides to R available for free on
his website http://hadley.nz/.I recommend checking out 'R for Data
Science'. Once you have done some programming in R, 
'Advanced R' is a really thorough overview of all the intricacies. RStudio publish
some great R cheatsheets https://www.rstudio.com/resources/cheatsheets/. For econometrics,
Nick Huntingdon-Klein has a great collection of resources
on his website https://nickchk.com/econometrics.html.
