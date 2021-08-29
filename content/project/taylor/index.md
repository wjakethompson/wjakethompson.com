---
author: W. Jake Thompson
categories:
- R
- package
date: "2021-08-17"
draft: false
excerpt: |
  Access lyrics and audio features for Taylor Swift's discography using the `taylor` package.
featured: true
layout: single
links:
- icon: door-open
  icon_pack: fas
  name: website
  url: https://taylor.wjakethompson.com/
- icon: github
  icon_pack: fab
  name: code
  url: https://github.com/wjakethompson/taylor
subtitle: ""
tags:
- Taylor Swift
title: Taylor Swift Data
---

The goal of `taylor` is to provide easy access to a curated data set of Taylor Swift songs, including lyrics and audio characteristics. Data comes [Genius](https://genius.com/artists/Taylor-swift) and the [Spotify API](https://open.spotify.com/artist/06HL4z0CvFAxyc27GXpf02).

You can install the released version of `taylor` from [CRAN](https://cran.r-project.org/) with:

``` r
install.packages(taylor)
```

To install the development version from [GitHub](https://github.com/) use:

``` r
# install.packages("remotes")
remotes::install_github("wjakethompson/taylor")
```
