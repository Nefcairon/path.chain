
<!-- README.md is generated from README.Rmd. Please edit that file -->

# path.chain <img src='man/figures/logo.png' align="right" height="139" />

<!-- badges: start -->

[![Documentation](https://img.shields.io/badge/documentation-path.chain-orange.svg?colorB=E91E63)](http://krzjoa.github.io/path.chain/)
[![Travis build
status](https://travis-ci.org/krzjoa/path.chain.svg?branch=master)](https://travis-ci.org/krzjoa/path.chain)
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/krzjoa/path.chain?branch=master&svg=true)](https://ci.appveyor.com/project/krzjoa/path.chain)
[![Buy hex
stciker](https://img.shields.io/badge/buy%20hex-path.chain-green)](http://www.redbubble.com/people/krzjoa/works/45140988-path-chain-r-package-hex-sticker?p=sticker&asc=u)
<!-- badges: end -->

> Concise structure for chainable paths

## Installation

``` r
# install.packages("devtools")
devtools::install_github("krzjoa/path.chain")
```

## Example

If you are using RStudio, you know that among many excellent features of
this IDE there is a **path autocompletion**.

![rstudio](man/figures/rstudio-autocompletion.gif)

However, you can also meet situations, when that may be not enough. Most
of all, I mean bigger projects, where you store a complex file structure
in the **config** file. You can handle such configuration YAML file
using the library named [`config`](https://github.com/rstudio/config).
You may encounter a situation, when you’ll want to save current
directory structure in this config.

``` r
library(magrittr)
library(path.chain)

# Create an example file stucture
create_sample_dir(".", name = "files")
#> [1] TRUE

# Sample structure we've already created looks as follows
fs::dir_tree("files")
#> files
#> ├── data
#> │   ├── example1.RData
#> │   ├── example2.RData
#> │   └── persons.csv
#> └── docs
#>     └── schema.txt

# Loading stucture
file.structure <- create_path_chain("files")
file.structure$data$example1.RData
#> [1] "files/data/example1.RData"

# Loading stucture with naming convention
file.structure <- create_path_chain("files", naming = naming_k)
file.structure$kData$kExample1
#> [1] "files/data/example1.RData"

# Saving file structure
file.structure %>% 
  as.list(root.name = "kRoot") %>%
  as_config() %>%  # Required by `{config}` package
  yaml::write_yaml("config.yaml")
```

``` yaml
default:
  kDirs:
    kRoot: files/
    kData:
      kRoot: data/
      kExample1: kExample1
      kExample2: kExample2
      kPersons: kPersons
    kDocs:
      kRoot: docs/
      kSchema: kSchema
```

``` r
k.dirs <- config::get("kDirs", "default", "config.yaml") %>% 
  as_path_chain()

class(k.dirs)
#> [1] "path_chain"

k.dirs$kData$.
#> NULL
k.dirs$kData$kExample1
#> NULL
```
