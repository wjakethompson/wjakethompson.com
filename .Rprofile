if (file.exists("~/.Rprofile")) {
  base::sys.source("~/.Rprofile", envir = environment())
}

if (interactive()) {
  suppressMessages(require(blogdown))
}

options(
  blogdown.author = "Jake Thompson",
  blogdown.ext = ".Rmd",
  blogdown.subdir = "post",
  blogdown.yaml.empty = TRUE,
  blogdown.new_bundle = TRUE,
  blogdown.title_case = TRUE,
  blogdown.hugo.version = "0.71.1",
  blogdown.serve_site.startup = FALSE,
  blogdown.knit.on_save = FALSE,
  blogdown.files_filter = blogdown:::md5sum_filter
)
