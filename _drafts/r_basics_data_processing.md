---
layout: post
title : "R Basics - Data Processing"
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

A typical workflow built using R will likely involve a number of sequential steps from importing the data into the R environment right through to outputting a completed analysis.

## An R workflow

The steps involved are not always clearly distinguishable from one another and the format that the data starts off in and the required output will dictate the steps involved and the precise order in which they are performed, though broadly speaking you will be...

### Extracting or importing data

Getting the data into the R environment is the first step towards starting an analysis. Data may be retrieved from a database, read from a static file on a local or remote drive, or scraped from the internet, amongst many other options.

Whilst often overlooked in terms of its importance, getting this step right can lay strong foundations for the rest of the project. Taking care to use the most suitable function to accurately and quickly access the data should always be an important consideration. 

For more information on reading static files you can check out [Reading Data with R](https://jrh-dev.github.io/posts/r_reading_data/).

### Performing exploratory analysis

This step involves interacting with the data, identifying its structure, checking for missing data or data quality issues, and generally building an understanding of the data.

Exploratory analysis helps us to avoid nasty surprises during later steps and to choose suitable techniques to build robust, performant, and correct analyses.

### Cleansing data 

In the real world data is rarely ready to use right away. Datasets may often contain extra data you don't require. Sometimes datasets may contain data items such as postal codes in multiple different formats, or in a format that won't allow you to join it to another source.

This step can also involve parsing the data, for example rearranging nested data into a flat format or extracting content from a http request.

Other common tasks include deduplication, handling missing values, recoding, and steps to improve data quality and accuracy.

### Performing analysis

Analysis typically includes adding to the dataset, perhaps calculating summary statistics by groups within the data, adding means, calculating confidence intervals and so on. 

This step often involves formatting or manipulating data structures into mediums suitable for presentation or to meet the needs of the end user. It may also include using additional tools and frameworks such as markdown or shiny.

### Creating outputs

The final step will typically be to export an output. This might be publishing a shiny dashboard, writing a report or data file to disk, deploying to the cloud, or anything else.

## A working example

We have 10 observations counting a number of patients entering a hospital ward by day, and 10 observations counting the number of adverse events. We can put these 2 sets of observations into a `data.frame`.

```r
patients <- c(56, 53, 45, 44, 46, 50, 48, 48, 42, 46)
events <- c(11, 12, 11, 12, 4, 12, 15, 13, 9, 14)

df <- data.frame(patients = patients, events = events)
#' patients events
#' 1        56     11
#' 2        53     12
#' 3        45     11
#' 4        44     12
#' 5        46      4
#' 6        50     12
#' 7        48     15
#' 8        48     13
#' 9        42      9
#' 10       46     14
```

We're interested in identifying any unusual variation in our data. One way we can approach this is to use a statistical process control methodology, namely a p-chart.

To create the chart we will need to calculate:

* the proportion of adverse events
* the average proportion of adverse events
* the variation
* an upper control limit
* a lower control limit

### Adding columns to a `data.frame`

We can use the `$` operator to create a new column. To start, lets add an identifier to our observations. As the `data.frame` already has row names we can capture these using the `row.names()` function and assign to a column named `observation`. The `data.frame` row names are stored as strings, so we can also use the `as.numeric()` function to create the `observation` column as a numeric type.

```r
df$observation <- as.numeric(row.names(df))
```

Nesting functions like this allows us to perform multiple operations without having to assign the returns at each step. In this example, `as.numeric()` evaluates the return of `row.names()`. There is no limit to the depth that functions can be nested to, but typically code should be written to be readable, so deeply nested code should be used sparingly.

We can create columns for the proportion and average proportion of adverse events named `percent_events` and `average`. To get the proportion of adverse events per day we need to divide the number of events by the number of patients for each of the 10 observations.

```r
df$percent_events <- df$events / df$patients
```

When we run `df$events / df$patients` we are actually performing the divide operation with 2 vectors od equal length. As such, the first element of `df$events` is divided by the first element of `df$patients`, then the second element of each, and so on. This operation therefore returns a vector. If we look at our `data.frame` we can see a value calculated individually for each row.

```r
print(df)
#'    patients events observation percent_events
#' 1        56     11           1     0.19642857
#' 2        53     12           2     0.22641509
#' 3        45     11           3     0.24444444
#' 4        44     12           4     0.27272727
#' 5        46      4           5     0.08695652
```

The syntax to create `percent_events` is very similar at first glance.

```r
df$average <- sum(df$events) / sum(df$patients)
```

However, `sum(df$events) / sum(df$patients)` returns a vector of length 1. The difference in behavior is due to the use of the `sum()` function which means that we only pass one element to each side of the division operator.

You might be wondering at this point how the single element returned by `sum(df$events) / sum(df$patients)` populates the 10 rows of our `data.frame`. The answer is that in this instance R 'recycles' the single element and uses it in each of our rows.

```r
print(df)
#'   patients events observation percent_events   average
#'1        56     11           1     0.19642857 0.2364017
#'2        53     12           2     0.22641509 0.2364017
#'3        45     11           3     0.24444444 0.2364017
#'4        44     12           4     0.27272727 0.2364017
#'5        46      4           5     0.08695652 0.2364017
```

We can run the lines side by side to compare the result.

```r
print(df$events / df$patients)
#' [1] 0.19642857 0.22641509 0.24444444 0.27272727 0.08695652 0.24000000 0.31250000 0.27083333 0.21428571
#' [10] 0.30434783

print(sum(df$events) / sum(df$patients))
#' [1] 0.2364017
```

The variation calculation is a little more complex.

```r
df$variation <- sqrt(df$average * (1 - df$average) / df$patients)
```

The `sqrt` function computes the square root a number, in this instance, the return of `df$average * (1 - df$average) / df$patients`. Although we have 3 vectors here (`df$average`, `1 - df$average`, `df$patients`) they are all of equal length, so the code returns a vector, again where the first element of each vector is processed then the second and so on.

Our final task is to calculate the upper and lower control limits.

```r
df$upper_limit <- df$average + 3 * df$variation

df$lower_limit <- df$average - 3 * df$variation

print(df)
#'    patients events observation percent_events   average  variation #' upper_limit lower_limit
#' 1        56     11           1     0.19642857 0.2364017 0.05677586   0.#' 4067293  0.06607408
#' 2        53     12           2     0.22641509 0.2364017 0.05836061   0.#' 4114835  0.06131984
#' 3        45     11           3     0.24444444 0.2364017 0.06333613   0.#' 4264101  0.04639329
#' 4        44     12           4     0.27272727 0.2364017 0.06405181   0.#' 4285571  0.04424624
#' 5        46      4           5     0.08695652 0.2364017 0.06264391   0.#' 4243334  0.04846995
```

We now have all the columns we need to create our p-chart, but The formatting isn't quite right for presentation. Our figures can be converted to a percentage and rounded to make them easier to interpret.

One approach is to round each column individually. 

```r
df$percent_events <- round(df$percent_events * 100, 1)
df$average <- round(df$average * 100, 1)
df$variation <- round(df$variation * 100, 1)
df$upper_limit <- round(df$upper_limit * 100, 1)
df$lower_limit <- round(df$lower_limit * 100, 1)
```

However, this type of approach violates a fundamental programming principle, "Don't Repeat Yourself (DRY)". The DRY principle dictates that we should strive to avoid writing the same line of code more than once, and is fundamental consideration when learning how to write efficient code.

A better approach in this instance is to take advantage of the fact that we want to perform the same operation on each column and they are all of the same type (numeric). With this in mind we can select all of the values simultaneously and apply our function.

```r
df[4:8] <- round(df[4:8] * 100, 1)

print(df)
#'    patients events observation percent_events average variation upper_limit lower_limit
#' 1        56     11           1           19.6    23.6       5.7        40.7         6.6
#' 2        53     12           2           22.6    23.6       5.8        41.1         6.1
#' 3        45     11           3           24.4    23.6       6.3        42.6         4.6
#' 4        44     12           4           27.3    23.6       6.4        42.9         4.4
#' 5        46      4           5            8.7    23.6       6.3        42.4         4.8
```

### Removing columns from a `data.frame`

Now that we have completed our calculations we don't actually need to retain the `variation` column. We can drop the column easily enough by creating a subset of our `data.frame`.

```r
df <- df[which(names(df) != "variation")]
```

### Change a `data.frame` layout

Our final step is to put our data into a format which we can easily use to create a chart. To this end we reduce the number of columns and create additional rows to present the same data.

The syntax for achieving this is as follows;

```r
df <- as.data.frame(
    tidyr::pivot_longer(
        df,
        cols = names(df[4:7]),
        names_to = "measure"
        )
    )

print(df)
#'    patients events observation        measure value
#' 1        56     11           1 percent_events  19.6
#' 2        56     11           1        average  23.6
#' 3        56     11           1    upper_limit  40.7
#' 4        56     11           1    lower_limit   6.6
#' 5        53     12           2 percent_events  22.6
#' 6        53     12           2        average  23.6
#' 7        53     12           2    upper_limit  41.1
#' 8        53     12           2    lower_limit   6.1
#' ...
```

There are a number of ways to transform a wide `data.frame` into a longer one, but `pivot_longer()` from the `tidyr` package is arguably one of the easier to use. However, being a part of the `tidyverse`, `pivot_longer()` returns a `tibble` rather than a `data.frame`. Whilst a `tibble` would work fine, we can keep the `data.frame` object by wrapping `pivot_longer()` with `as.data.frame()`.

<div class = "blue" markdown="1">
<i class="fa-solid fa-circle-info fa-lg"></i>
***Tibbles?***

> A tibble, or tbl_df, is a modern reimagining of the data.frame, keeping what time has proven to be effective, and throwing out what is not. Tibbles are data.frames that are lazy and surly: they do less (i.e. they don’t change variable names or types, and don’t do partial matching) and complain more (e.g. when a variable does not exist). This forces you to confront problems earlier, typically leading to cleaner, more expressive code. Tibbles also have an enhanced print() method which makes them easier to use with large datasets containing complex objects. <br><br>
***tibble.tidyverse.org***

</div>

### Plotting the p-chart

Having wrangled our data into the correct format we can now create the p-chart using the `ggplot2` package.

```r
library(ggplot2)

p <- ggplot(df, aes(observation, value, color = measure)) + 
        geom_point() +
        geom_line()
```

![](/assets/img/r_basics_data_processing/img01.png)