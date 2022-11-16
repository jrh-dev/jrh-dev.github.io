---
layout: post
title : "R Basics - Grouping"
toc : true
tags: [R basics, Tutorial]
categories: R Basics
author: <author_id>
---

<script src="https://kit.fontawesome.com/6594d43a2e.js" crossorigin="anonymous"></script>

<style>
div.blue {color:#000000; background-color:#a7c6ff; border-radius: 5px; padding: 12px; opacity:1; margin-bottom:20px;}
</style>

<style>
div.yellow {color:#000000; background-color:#fff0a7; border-radius: 5px; padding: 12px; opacity:1; margin-bottom:20px;}
</style>

<style>
div.red {color:#000000; background-color:#ffa7a7; border-radius: 5px; padding: 12px; opacity:1; margin-bottom:20px;}
</style>

Data often contains information on a multitude of groups. Producing summary or aggregated statistics for these groups is a common task.

## An example dataset

There are many ways to produce summary statistics and aggregations using R, however, the most intuitive way to achieve this is likely to use the `tidyverse` package `dplyr`. The `dplyr` package offers functions for aggregating and summarising data which are simple to use and that avoid some of the pitfalls found in alternative approaches.

We can use the built in `mtcars` dataset. For a quick reminder of the contents of `mtcars`, lets try using the `head()` function.

```r
head(mtcars)
#'                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
#' Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
#' Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
#' Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
#' Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
#' Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

You may have noticed that the names of the vehicles are not actually contained within a column, rather they are stored as row names. Because the row names are unique they're not going to be much use for grouping, however, the vehicles are generally formatted such as the manufacturer name is specified, followed by a space, then the model. Let's add a column with the manufacturer name.

`gsub()` is a function used for string replacement. It allows us to find a pattern within a character string and substitute for a specified value. 

We will use the `pattern` argument to identify the text we want to remove, in this case we want to remove everything after and including the first space from the row names of `mtcars`. 

The `replacement` argument allows us to specify what we want to replace the identified text with. In this instance, we just want to remove it, so we will simply pass `""` as the `replacement` argument.

We will pass `row.names(mtcars)` as the `x` argument.

`gsub(pattern, replacement, x)`

We will use a regular expression to identify the pattern we want to remove, ` .*$`. Regular expressions (regex) are standardised sequences of characters that specify patterns in text. In programming we can use regex to perform find and replace style operations on strings, input validation, and similar tasks.

In our example we want to use the expression ` .*$` to remove everything after the first space.

![](/assets/img/r_basics_groups/img01.png)

If you would like to know more about regex, [RegExr](https://regexr.com/) is an online tool to learn, build, and test regular expressions.

By combining the elements discussed above we can create add a `manufacturer` column to `mtcars`.

```r
mtcars$manufacturer <- gsub(
    pattern = " .*$",
    replacement = "",
    x = row.names(mtcars)
    )

print(mtcars$manufacturer)
#'  [1] "Mazda"    "Mazda"    "Datsun"   "Hornet"   "Hornet"   "Valiant" 
#'  [7] "Duster"   "Merc"     "Merc"     "Merc"     "Merc"     "Merc"    
#' [13] "Merc"     "Merc"     "Cadillac" "Lincoln"  "Chrysler" "Fiat"    
#' [19] "Honda"    "Toyota"   "Toyota"   "Dodge"    "AMC"      "Camaro"  
#' [25] "Pontiac"  "Fiat"     "Porsche"  "Lotus"    "Ford"     "Ferrari" 
#' [31] "Maserati" "Volvo"
```

## Grouping data

Using the `dplyr` package to generate groups to allow for the production of summary statistics and aggregations involves two functions; `group_by()` and `ungroup()`.

`group_by()` is used to create groups, and `ungroup()` to remove them.

### group_by() - single grouping column

The `group_by()` function accepts a number of arguments, you can check these out by running `?group_by`. In its most basic usage though, we only need to specify our data (mtcars) and the column or columns that we want the data to be grouped by. 

Let's start by grouping mtcars using the `manufacturer` column that we added.

```r
dplyr::group_by(mtcars, manufacturer)
#' # A tibble: 32 × 12
#' # Groups:   manufacturer [22]
#'      mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb manufactu…¹
#'    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <chr>      
#'  1  21       6  160    110  3.9   2.62  16.5     0     1     4     4 Mazda      
#'  2  21       6  160    110  3.9   2.88  17.0     0     1     4     4 Mazda      
#'  3  22.8     4  108     93  3.85  2.32  18.6     1     1     4     1 Datsun     
#'  4  21.4     6  258    110  3.08  3.22  19.4     1     0     3     1 Hornet     
#'  5  18.7     8  360    175  3.15  3.44  17.0     0     0     3     2 Hornet     
#'  6  18.1     6  225    105  2.76  3.46  20.2     1     0     3     1 Valiant    
#'  7  14.3     8  360    245  3.21  3.57  15.8     0     0     3     4 Duster     
#'  8  24.4     4  147.    62  3.69  3.19  20       1     0     4     2 Merc       
#'  9  22.8     4  141.    95  3.92  3.15  22.9     1     0     4     2 Merc       
#' 10  19.2     6  168.   123  3.92  3.44  18.3     1     0     4     4 Merc       
#' # … with 22 more rows, and abbreviated variable name ¹​manufacturer
#' # ℹ Use `print(n = ...)` to see more rows
```

Note that whilst using `group_by()` has converted our data into a tibble, no changes have been made to the data itself, though we have lost our row names!

To see the effect of `group_by()` we can check the attributes of our data.

```r
attributes(mtcars)
#' $class
#' [1] "grouped_df" "tbl_df"     "tbl"        "data.frame"
#' 
#' $row.names
#'  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
#' [26] 26 27 28 29 30 31 32
#' 
#' $names
#'  [1] "model"        "mpg"          "cyl"          "disp"         "hp"          
#'  [6] "drat"         "wt"           "qsec"         "vs"           "am"          
#' [11] "gear"         "carb"         "manufacturer"
#' 
#' $groups
#' # A tibble: 22 × 2
#'    manufacturer       .rows
#'    <chr>        <list<int>>
#'  1 AMC                  [1]
#'  2 Cadillac             [1]
#'  3 Camaro               [1]
#'  4 Chrysler             [1]
#'  5 Datsun               [1]
#'  6 Dodge                [1]
#'  7 Duster               [1]
#'  8 Ferrari              [1]
#'  9 Fiat                 [2]
#' 10 Ford                 [1]
#' # … with 12 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

You can see that the `$groups` attribute has been added recording our groupings.

If we want to preserve the row names we need to convert `mtcars` to a tibble explicitly before using the `group_by()` function. To achieve this we use the `as_tibble()` function from the `tibble` package and make sure to set the `rownames` argument to `NA` or `a_string_of_your_choice`. If `rownames = NA`, the row names are preserved, if a string is provided a new column is created containing the row names and is names as per the string provided.

```r
# step 1 - convert to tibble
mtcars <- tibble::as_tibble(mtcars, rownames = "model")

print(mtcars)
#' # A tibble: 32 × 13
#'    model         mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb
#'    <chr>       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
#'  1 Mazda RX4    21       6  160    110  3.9   2.62  16.5     0     1     4     4
#'  2 Mazda RX4 …  21       6  160    110  3.9   2.88  17.0     0     1     4     4
#'  3 Datsun 710   22.8     4  108     93  3.85  2.32  18.6     1     1     4     1
#'  4 Hornet 4 D…  21.4     6  258    110  3.08  3.22  19.4     1     0     3     1
#'  5 Hornet Spo…  18.7     8  360    175  3.15  3.44  17.0     0     0     3     2
#'  6 Valiant      18.1     6  225    105  2.76  3.46  20.2     1     0     3     1
#'  7 Duster 360   14.3     8  360    245  3.21  3.57  15.8     0     0     3     4
#'  8 Merc 240D    24.4     4  147.    62  3.69  3.19  20       1     0     4     2
#'  9 Merc 230     22.8     4  141.    95  3.92  3.15  22.9     1     0     4     2
#' 10 Merc 280     19.2     6  168.   123  3.92  3.44  18.3     1     0     4     4
#' # … with 22 more rows, and 1 more variable: manufacturer <chr>
#' # ℹ Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names

# step 2 - use `group_by()`
mtcars <- dplyr::group_by(mtcars, manufacturer)

print(mtcars)
#' # A tibble: 32 × 13
#' # Groups:   manufacturer [22]
#'    model         mpg   cyl  disp    hp  drat    wt  qsec    vs    am  gear  carb
#'    <chr>       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
#'  1 Mazda RX4    21       6  160    110  3.9   2.62  16.5     0     1     4     4
#'  2 Mazda RX4 …  21       6  160    110  3.9   2.88  17.0     0     1     4     4
#'  3 Datsun 710   22.8     4  108     93  3.85  2.32  18.6     1     1     4     1
#'  4 Hornet 4 D…  21.4     6  258    110  3.08  3.22  19.4     1     0     3     1
#'  5 Hornet Spo…  18.7     8  360    175  3.15  3.44  17.0     0     0     3     2
#'  6 Valiant      18.1     6  225    105  2.76  3.46  20.2     1     0     3     1
#'  7 Duster 360   14.3     8  360    245  3.21  3.57  15.8     0     0     3     4
#'  8 Merc 240D    24.4     4  147.    62  3.69  3.19  20       1     0     4     2
#'  9 Merc 230     22.8     4  141.    95  3.92  3.15  22.9     1     0     4     2
#' 10 Merc 280     19.2     6  168.   123  3.92  3.44  18.3     1     0     4     4
#' # … with 22 more rows, and 1 more variable: manufacturer <chr>
#' # ℹ Use `print(n = ...)` to see more rows, and `colnames()` to see all variable names
```

Now that our data is grouped, we can use `summarise()` form the `dplyr` package to work out the mean miles per gallon (`mpg`) for each manufacturer.

`summarise()` creates a new data frame which will contain a row for each combination of grouping variables that exists in the data, or if there are no grouping variables, a single row summarising all of the observations in the input. The resulting data frame contains a column for each grouping variable and additional columns for each of the summary statistics that have been specified.

<cite >***[dplyr.tidyverse.org](https://dplyr.tidyverse.org/reference/summarise.html)***</cite>

</blockquote>

```r
dplyr::summarise(mtcars, avg_mpg = mean(mpg))
#' # A tibble: 22 × 2
#'    manufacturer avg_mpg
#'    <chr>          <dbl>
#'  1 AMC             15.2
#'  2 Cadillac        10.4
#'  3 Camaro          13.3
#'  4 Chrysler        14.7
#'  5 Datsun          22.8
#'  6 Dodge           15.5
#'  7 Duster          14.3
#'  8 Ferrari         19.7
#'  9 Fiat            29.8
#' 10 Ford            15.8
#' # … with 12 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

We can also add a column with the number of observations in the same operation. The `dplyr` function `n()` allows us to do this.

```r
dplyr::summarise(
    mtcars,
    mean_mpg = mean(mpg),
    observations = dplyr::n()
    )
#' # A tibble: 22 × 3
#'    manufacturer mean_mpg observations
#'    <chr>           <dbl>        <int>
#'  1 AMC              15.2            1
#'  2 Cadillac         10.4            1
#'  3 Camaro           13.3            1
#'  4 Chrysler         14.7            1
#'  5 Datsun           22.8            1
#'  6 Dodge            15.5            1
#'  7 Duster           14.3            1
#'  8 Ferrari          19.7            1
#'  9 Fiat             29.8            2
#' 10 Ford             15.8            1
#' # … with 12 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

### ungroup()

Unfortunately, `mtcars` contains a lot of unique manufacturers. Instead of `mpg` by `manufacturer`, lets look at `mpg` by number of cylinders (`cyl`) and gears (`gear`). Before we can do this we need to remove the current grouping with the `dplyr` function `ungroup()`.

```r
mtcars <- dplyr::ungroup(mtcars)
``` 

Like using `group_by()`, `ungroup()` doesn't appear to change anything obvious in our data. However, if we review the attributes again, the `$groups` attribute has now been removed.

```r
attributes(mtcars)
#' $class
#' [1] "tbl_df"     "tbl"        "data.frame"
#' 
#' $row.names
#'  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
#' [26] 26 27 28 29 30 31 32
#' 
#' $names
#'  [1] "model"        "mpg"          "cyl"          "disp"         "hp"          
#'  [6] "drat"         "wt"           "qsec"         "vs"           "am"          
#' [11] "gear"         "carb"         "manufacturer"
```

### group_by() - multiple grouping column

Let's apply the new grouping by `cyl` and `gear`.

```r
mtcars <- dplyr::group_by(mtcars, cyl, gear)
```

We can now calculate a mean `mpg` value by both `cyl` and `gear`.

```r
dplyr::summarise(mtcars, mean_mpg = mean(mpg))
#' `summarise()` has grouped output by 'cyl'. You can
#' override using the `.groups` argument.
#' # A tibble: 8 × 3
#' # Groups:   cyl [3]
#'     cyl  gear mean_mpg
#'   <dbl> <dbl>    <dbl>
#' 1     4     3     21.5
#' 2     4     4     26.9
#' 3     4     5     28.2
#' 4     6     3     19.8
#' 5     6     4     19.8
#' 6     6     5     19.7
#' 7     8     3     15.0
#' 8     8     5     15.4
```

### group_by - keep all columns
Summarise works well, but it reduces the number of rows down to one row per distinct combination of the grouping variables, and drops columns that aren't grouping variables.

If we want to add the mean `mpg` by `cyl` and `gear` to our data 'as is', we need to use another `dplyr` function; `mutate()`. `mutate()` can be used to add new columns.

```r
mtcars <- dplyr::mutate(mtcars, mean_mpg = mean(mpg))

# subset columns to make tidier print
print(mtcars[c("model", "mpg", "cyl", "gear", "mean_mpg")])
#' # A tibble: 32 × 5
#' # Groups:   cyl, gear [8]
#'    model               mpg   cyl  gear mean_mpg
#'    <chr>             <dbl> <dbl> <dbl>    <dbl>
#'  1 Mazda RX4          21       6     4     19.8
#'  2 Mazda RX4 Wag      21       6     4     19.8
#'  3 Datsun 710         22.8     4     4     26.9
#'  4 Hornet 4 Drive     21.4     6     3     19.8
#'  5 Hornet Sportabout  18.7     8     3     15.0
#'  6 Valiant            18.1     6     3     19.8
#'  7 Duster 360         14.3     8     3     15.0
#'  8 Merc 240D          24.4     4     4     26.9
#'  9 Merc 230           22.8     4     4     26.9
#' 10 Merc 280           19.2     6     4     19.8
#' # … with 22 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

### Caution with group_by() and ungroup()

Using `group_by()` add a grouping attribute to the object it is used on. A number of `tidyverse` functions will use recognise attribute and behave accordingly. It is therefore important that you use `ungroup()` after `group_by()` to remove the groupings.
