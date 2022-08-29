---
layout: post
title : "Reading Data with R"
toc : true
tags: [R, R basics, Tutorial, Analysis]
categories: R Basics
author: <author_id>
---

Before we can start working with data we need to import it into the R environment. Base R (the language as installed without additional libraries installed) contains a variety of methods for reading data in multiple formats, but the performance benefits and ease of use of some functions provided by external libraries makes them the go to choice for many users.

The following examples are provided for some of the most common types of data. Whilst by no means an exhaustive list of all options, the functions detailed are performant, stable, and easy to use.

## CSV Data

One of the most common cross-platform methods for storing typically tabular data.

### **utils::read.csv()**

`read.csv()` from the `{utils}` package is included in any installation of R.

Supplying just the path to the CSV file, the data can be imported simply enough, and attempts are made to set appropriate data classes. However, we can see in the example below that the date column has been read as character.

```r
> dat <- utils::read.csv("example/example_1.csv")

> print(dat)
#>   uid       date      col_b        col_c
#> 1   a 2022-08-01 -0.3082584 -0.942751791
#> 2   b 2022-08-01  0.6946568  0.058719041
#> 3   c 2022-08-01  1.1463694 -0.621216248
#> 4   d 2022-08-01  0.2453974 -1.408545363
#> 5   e 2022-08-01  0.4482430  0.006183595

> sapply(dat, class)
#>         uid        date       col_b       col_c 
#> "character" "character"    "double"    "double" 
```

Using the `colClasses` argument we can set the date column more appropriately.

```r
> dat <- utils::read.csv(
      "example/example_1.csv",
      colClasses = c("character", "POSIXct", "double", "double")
      )

> print(dat)
#>  uid       date      col_b        col_c
#> 1   a 2022-08-01 -0.3082584 -0.942751791
#> 2   b 2022-08-01  0.6946568  0.058719041
#> 3   c 2022-08-01  1.1463694 -0.621216248
#> 4   d 2022-08-01  0.2453974 -1.408545363
#> 5   e 2022-08-01  0.4482430  0.006183595

> sapply(dat, class)
#>         uid        date       col_b       col_c 
#> "character" "character"   "numeric"   "numeric" 
```

Whilst the date column now has the class"double", this is simply reflective of the way in which R store dates under the hood.

```r
> typeof(as.POSIXct("2021-01-01"))
#>[1] "double"
```

A full list of `read.csv`'s arguments can be found by running `?utils::read.csv`.

Unfortunately, the performance of `read.csv` is poor compared the alternatives available, so it should be avoided when reading a CSV file of more than a few MB's.

### **readr::read_csv()**

The tidyverse package `readr` offers the more sophisticated `read_csv()` function. Its relatively performant, it makes a good job of inferring types, and for users planning to use the [tidyverse](https://www.tidyverse.org/) it helpfully returns a [tibble](https://tibble.tidyverse.org/).

During interactive use calling the function prints some useful information regarding the delimiter used and column types detected.

```r
> dat <- readr::read_csv("example/example_1.csv")
#> Rows: 5 Columns: 4 
#> ── Column specification ──────────────────────────────────────────────────
#> Delimiter: ","
#> chr  (1): uid
#> dbl  (2): col_b, col_c
#> date (1): date
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

> print(dat)
#> # A tibble: 5 × 4
#> uid   date        col_b    col_c
#> <chr> <date>      <dbl>    <dbl>
#> 1 a     2022-08-01 -0.308 -0.943  
#> 2 b     2022-08-01  0.695  0.0587 
#> 3 c     2022-08-01  1.15  -0.621  
#> 4 d     2022-08-01  0.245 -1.41   
#> 5 e     2022-08-01  0.448  0.00618
```

A full list of `read_csv`'s arguments can be found by running `?readr::read_csv`.

`read_csv` is fast, predictable, and stable. The additional information provided can be useful during interactive analysis. The `readr` package is very widely used and is actively maintained and developed, providing some confidence for developers aiming for long term reproducibility. 

### **data.table::fread()**

When performance and speed is of the utmost importance nothing really comes close to the `data.table` package. The `fread()` function is simple to use, blazingly fast, and the 10,000 row sample of equally spaced data throughout the target file allows it to make very accurate guesses at column type.

```r
> dat <- data.table::fread("example/example_1.csv")

> print(dat)
#>    uid       date      col_b        col_c
#> 1:   a 2022-08-01 -0.3082584 -0.942751791
#> 2:   b 2022-08-01  0.6946568  0.058719041
#> 3:   c 2022-08-01  1.1463694 -0.621216248
#> 4:   d 2022-08-01  0.2453974 -1.408545363
#> 5:   e 2022-08-01  0.4482430  0.006183595

> sapply(dat, class)
#>         uid             date        col_b       col_c 
#> "character"   "IDate" "Date"     "double"    "double" 
```

`fread` returns a `data.table` class object by default, however, we can easily have the function return a `data.frame`by passing the argument `data.table=FALSE`. However, `data.table`'s can be used anywhere you would use a `data.frame`.

```r
> class(data.table::fread("example/example_1.csv"))
#> [1] "data.table" "data.frame"

> class(data.table::fread("example/example_1.csv", data.table=FALSE))
#> [1] "data.frame"
```

A full list of `fread`'s arguments can be found by running `?data.table::fread`.

The `data.table` package is very widely used and is actively maintained and developed, like with `readr` this provides some confidence for developers aiming for long term reproducibility. Given its speed `data.table` is a good option for any process going into a production environment or long running work flows.

#### Which should I use?

`readr::read_csv()` and `data.table::fread()` are both solid choices for reading a CSV file. The most pressing factors may come down to speed and the libraries you intend to use for the rest of your process. If relying heavily upon the tidyverse it makes sense to lean toward `readr`, whereas if working with large datasets `data.table` will likely make more sense. 


## XLSX Data

The ubiquitous format for excel spreadsheets since 2007. Still pervasively popular and [sometimes misused](https://www.bbc.co.uk/news/technology-54423988) despite a wide array of alternatives. Being able to confidently read `.xlsx` files will definitely be come in handy.

### **openxlsx::read.xlsx()**

`openxlsx` is a well established library with less dependencies than some of the alternatives (no Java dependency), and the `read.xlsx()` function is straightforward to use.

```r
> dat <- openxlsx::read.xlsx("example1.xlsx")

> print(dat)
#>   uid  date      col_b        col_c
#> 1   a 44774 -0.3082584 -0.942751791
#> 2   b 44774  0.6946568  0.058719041
#> 3   c 44774  1.1463694 -0.621216248
#> 4   d 44774  0.2453974 -1.408545363
#> 5   e 44774  0.4482430  0.006183595
```

Dates in particular can be read as integers, but this is easily overcome by supplying the argument `detectDates=TRUE`. Generally, the column type detection is adequate.

```r
> print(dat)
#>   uid       date      col_b        col_c
#> 1   a 2022-08-01 -0.3082584 -0.942751791
#> 2   b 2022-08-01  0.6946568  0.058719041
#> 3   c 2022-08-01  1.1463694 -0.621216248
#> 4   d 2022-08-01  0.2453974 -1.408545363
#> 5   e 2022-08-01  0.4482430  0.006183595

> sapply(dat, class)
#>         uid        date       col_b       col_c 
#> "character"      "Date"   "numeric"   "numeric" 
```

When working with excel workbooks we often find notes or titles contained in the first few lines of a worksheet, followed by the tabular data we want to import. We may also have workbooks containing multiple worksheets, where only specific worksheets are of interest.

<!-- insert image of excel workbook -->

`openxlsx::read.xlsx()` makes it easy for us to specify and read data from specific worksheets and cell ranges.

```r
openxlsx::read.xlsx("example.xlsx",sheet="example_sheet",cols=c(1:4),rows(4:10))
```

A full list of `read.xlsx`'s arguments can be found by running `?openxlsx::read.xlsx`.

## JSON

## YAML

## ZIP

## RData

## RDS

## SPSS

## Stata

## SAS



























