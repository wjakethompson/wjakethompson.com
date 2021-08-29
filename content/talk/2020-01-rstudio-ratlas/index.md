---
title: Branding and Packaging Reports with R Markdown
author: W. Jake Thompson & Noelle Pablo
excerpt: |
  Create custom R Markdown templates and ggplot2 themes, then package them up to distribute.
abstract_short: ""
event: rstudio::conf(2020)
event_url: https://www.rstudio.com/resources/rstudioconf-2020/
location: San Francisco, CA

# Talk start and end times.
date: "2020-01-30T10:30:59Z"
date_end: "2020-01-30T10:52:59Z"
all_day: false
publishdate: "2020-01-01"
draft: false
featured: true

categories:
- talk
- conference
- R
- package
tags:
- ATLAS
- rmarkdown
- ggplot2

links:
- icon: images
  icon_pack: fas
  name: slides
  url: https://speakerdeck.com/wjakethompson/branding-and-packaging-reports-with-r-markdown
- icon: play-circle
  icon_pack: fas
  name: video
  url: https://rstudio.com/resources/rstudioconf-2020/branding-and-packaging-reports-with-r-markdown/
- icon: door-open
  icon_pack: fas
  name: website
  url: https://ratlas.netlify.app/
- icon: github
  icon_pack: fab
  name: code
  url: https://github.com/atlas-aai/ratlas
---

The creation of research reports and manuscripts is a critical aspect of the work conducted by organizations and individual researchers. Most often, this process involves copying and pasting output from many different analyses into a separate document. Especially in organizations that produce annual reports for repeated analyses, this process can also involve applying incremental updates to annual reports. It is important to ensure that all relevant tables, figures, and numbers within the text are updated appropriately. Done manually, these processes are often error prone and inefficient. R Markdown is ideally suited to support these tasks. With R Markdown, users are able to conduct analyses directly in the document or read in output from a separate analyses pipeline. Tables, figures, and in-line results can then be dynamically populated and automatically numbered to ensure that everything is correctly updated when new data is provided. Additionally, the appearance of documents rendered with R Markdown can be customized to meet specific branding and formatting requirements of organizations and journals. In this presentation, we will present one implementation of customized R Markdown reports used for Accessible Teaching, Learning, and Assessment Systems (ATLAS) at the University of Kansas. A publicly available R package, `ratlas`, provides both Microsoft Word and LaTeX templates for different types of projects at ATLAS with their own unique formatting requirements. We will discuss how to create brand-specific templates, as well as how to incorporate the templates into an R package that can be used to unify report creation across an organization. We will also describe other components of branding reports beyond R Markdown templates, including customized `ggplot2` themes, which can also be wrapped into the R package. Finally, we will share lessons learned from incorporating the R package workflow into an existing reporting pipeline.

Here are links to the various (and very good!) resources I recommend for building branded R Markdown reports and packages:

* For creating templates for MS Word documents:
    * Daniel Hadley's 2018 rstudio::conf() talk, [Branding and Automating with R Markdown](https://www.danielphadley.com/branding-rmarkdown/)
    * Richard Layton's [Happy Collaboration with Rmd to docx](https://rmarkdown.rstudio.com/articles_docx.html)
    * Section 3.4 of Yihui Xie, J. J. Allaire, and Garrett Grolemund's [*R Markdown: The Definitive Guide*](https://bookdown.org/yihui/rmarkdown/word-document.html)
* For polishing reports with custom [`ggplot2`](https://ggplot2.tidyverse.org) themes and default [`knitr`](https://yihui.org/knitr/) chunk options:
    * Bob Rudis's [`hrbrthemes`](https://github.com/hrbrmstr/hrbrthemes), Emily Riederer's [`Rtistic`](https://github.com/emilyriederer/rtistic), and the BBC's [`bbplot`](https://github.com/bbc/bbplot) all provide great examples and/or templates for creating `ggplot2` themes and wrapping them into an R package.
    * A comprehensive list of availalbe {knitr} chunk options are availabe on [Yihui Xie's website](https://yihui.org/knitr/options/).
* Finally, examples of wrapping R Markdown templates and/or {ggplot2} themes into packages for an organization:
    * The [`ratlas`](https://github.com/atlas-aai/ratlas) package used by Accessible Teaching, Learning, and Assessment Systems at the University of Kansas.
    * The [`sorensonimpact`](https://github.com/Sorenson-Impact/sorensonimpact) package used by the Sorenson Impact Center at the University of Utah.
    * The [`thesisdown`](https://github.com/ismayc/thesisdown) package for writing theses at Reed College.
        * This package has many forks for other colleges, which are linked to in the {thesisdown} README. This is a great example for how to take an existing package and adapt it to the needs of your organization.
    * The [`rticles`](https://github.com/rstudio/rticles) package provides wrappers for templates for a variety of academic journals.
