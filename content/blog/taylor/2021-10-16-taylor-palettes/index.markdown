---
title: "Creating a custom color palette class with vctrs"
subtitle: ""
excerpt: "We're in screaming color! Learn how the custom color palette methods for taylor were made using vctrs."
date: 2021-10-16
author: "Jake Thompson"
draft: false
weight: 1
categories:
  - R
  - package
tags:
  - Taylor Swift
layout: single-series
---



In the first post in the [taylor series](https://www.wjakethompson.com/blog/taylor/), I introduced the [taylor](https://taylor.wjakethompson.com) package for accessing data on Taylor Swift's discography.
In this post, we'll dig into how one particular feature works under the hood: the custom color palettes.

First, credit where credit is due. This idea was 100% inspired by [Josiah Parry's](https://twitter.com/JosiahParry) work on the [cpcinema](https://github.com/JosiahParry/cpcinema) package.
That repository was a great starting point to build off of.
Although I ended up re-building many of the functions to better suit the needs of taylor, this feature would not exist had I not seen this initial work.
Second, the [vctrs](https://vctrs.r-lib.org) has excellent documentation that makes building custom classes as straightforward as possible.
Specifically, I spent a lot of time with the [S3 vectors vignette](https://vctrs.r-lib.org/articles/s3-vector.html).
It's a great resources for building your own classes.
Without further ado, let's get to it!

# What do you mean 'custom color palettes'?

Let's start with what we're actually talking about.
There are many ways to specify colors in R.
For example, we can use any of the color names included in `colors()`, or we can specify our own hexadecimal values.
And we can use `scales::show_col()` to see what these colors actually look like.


```r
my_colors <- c("wheat", "firebrick", "#009FB7")
my_colors
#> [1] "wheat"     "firebrick" "#009FB7"

scales::show_col(my_colors)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/plain-pal-1.png" width="80%" style="display: block; margin: auto;" />

