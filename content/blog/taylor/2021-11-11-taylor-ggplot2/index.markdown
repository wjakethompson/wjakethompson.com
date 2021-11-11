---
title: Creating ggplot2 fill and color scales
author: Jake Thompson
date: '2021-11-11'
categories:
  - package
  - R
tags:
  - Taylor Swift
  - ggplot2
excerpt: Learn to create custom fill and color scales for ggplot2 to make your
  plots *Gorgeous*.
draft: no
weight: 1
layout: single-series
---



In the first post in the [taylor series](https://www.wjakethompson.com/blog/taylor/), I introduced the [taylor](https://taylor.wjakethompson.com) package for accessing data on Taylor Swift's discography.
We then began exploring some of the internals of the package by examining how taylor uses vctrs to [create a custom color palette class](../2021-10-24-taylor-palettes/).
In this post, we'll extend that work to see how the color palettes can be wrapped into color and fill scales for ggplot2.

