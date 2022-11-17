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

We can use the built in `iris` dataset. For a quick reminder of the contents of `iris`, lets try using the `head()` function.

```r
head(iris)
#'   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#' 1          5.1         3.5          1.4         0.2  setosa
#' 2          4.9         3.0          1.4         0.2  setosa
#' 3          4.7         3.2          1.3         0.2  setosa
#' 4          4.6         3.1          1.5         0.2  setosa
#' 5          5.0         3.6          1.4         0.2  setosa
#' 6          5.4         3.9          1.7         0.4  setosa 1
```

We can create a copy of `iris` to work with named `df`.

```r
df <- iris
```

## Grouping data

Using the `dplyr` package to generate groups to allow for the production of summary statistics and aggregations involves two functions; `group_by()` and `ungroup()`.

`group_by()` is used to create groups, and `ungroup()` to remove them.

### group_by() - single grouping column

The `group_by()` function accepts a number of arguments, you can check these out by running `?group_by`. In its most basic usage though, we only need to specify our data (`df`) and the column or columns that we want the data to be grouped by. 

Let's start by grouping `df` using the `Species` column.

```r
df <- dplyr::group_by(df, Species)

print(df)
#' # A tibble: 150 × 5
#' # Groups:   Species [3]
#'    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#'           <dbl>       <dbl>        <dbl>       <dbl> <fct>  
#'  1          5.1         3.5          1.4         0.2 setosa 
#'  2          4.9         3            1.4         0.2 setosa 
#'  3          4.7         3.2          1.3         0.2 setosa 
#'  4          4.6         3.1          1.5         0.2 setosa 
#'  5          5           3.6          1.4         0.2 setosa 
#'  6          5.4         3.9          1.7         0.4 setosa 
#'  7          4.6         3.4          1.4         0.3 setosa 
#'  8          5           3.4          1.5         0.2 setosa 
#'  9          4.4         2.9          1.4         0.2 setosa 
#' 10          4.9         3.1          1.5         0.1 setosa 
#' # … with 140 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

Note that whilst using `group_by()` has converted our data into a tibble, no changes have been made to the data itself. The output from `print()` does show that we have 3 groups based on `Species`; `# Groups:   Species [3]`.

To see the effects of `group_by()` we can check the attributes of our data.

```r
attributes(df)
#' $class
#' [1] "grouped_df" "tbl_df"     "tbl"        "data.frame"
#' 
#' $row.names
#'   [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18
#'  [19]  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36
#'  [37]  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53  54
#'  [55]  55  56  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71  72
#'  [73]  73  74  75  76  77  78  79  80  81  82  83  84  85  86  87  88  89  90
#'  [91]  91  92  93  94  95  96  97  98  99 100 101 102 103 104 105 106 107 108
#' [109] 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125 126
#' [127] 127 128 129 130 131 132 133 134 135 136 137 138 139 140 141 142 143 144
#' [145] 145 146 147 148 149 150
#' 
#' $names
#' [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"     
#' 
#' $groups
#' # A tibble: 3 × 2
#'   Species          .rows
#'   <fct>      <list<int>>
#' 1 setosa            [50]
#' 2 versicolor        [50]
#' 3 virginica         [50]
```

You can see that the `$groups` attribute has been added recording our groupings and an additional element has been added to the `$class` attribute, `grouped_df`. 

<div class = "yellow" markdown="1">
<i class="fa-solid fa-circle-info fa-lg"></i>

***CAUTION:*** Whilst our data remains grouped we need to take care when using functions that behave differently when passed a `grouped_df`. Typically this will be functions from `tidyverse` packages, though there is nothing to prevent authors of other packages utilising the `grouped_df` attributes. If we don't want our output to be effected by the groupings then the grouping will need to be removed explicitly.

</div>

## summarise()

`summarise()`, from the `dplyr` package, creates a new data frame which will contain a row for each combination of grouping variables that exists in the data, or if there are no grouping variables, a single row summarising all of the observations in the input. The resulting data frame contains a column for each grouping variable and additional columns for each of the summary statistics that have been specified. 

We can use `summarise()` to work out the mean petal length (`Petal.Length`) for each `Species`.

```r
dplyr::summarise(df, Mean.Petal.Length = mean(Petal.Length))
#' # A tibble: 3 × 2
#'   Species    Mean.Petal.Length
#'   <fct>                  <dbl>
#' 1 setosa                  1.46
#' 2 versicolor              4.26
#' 3 virginica               5.55
```

We are not limited to adding one summary statistic at a time, if we want the mean and median values for `Petal.Length`, we can add both in the same function call.

```r
dplyr::summarise(
    df,
    Mean.Petal.Length = mean(Petal.Length),
    Median.Petal.Length = median(Petal.Length)
    )
#' # A tibble: 3 × 3
#'   Species    Mean.Petal.Length Median.Petal.Length
#'   <fct>                  <dbl>               <dbl>
#' 1 setosa                  1.46                1.5 
#' 2 versicolor              4.26                4.35
#' 3 virginica               5.55                5.55
```

### n()

It is often useful to have a count of the number of observations within a group. The `dplyr` function `n()` allows us to do this.

```r
dplyr::summarise(df, observations = dplyr::n())
#' # A tibble: 3 × 2
#'   Species    observations
#'   <fct>             <int>
#' 1 setosa               50
#' 2 versicolor           50
#' 3 virginica            50
```

### ungroup()

Once we have finished working with the groupings we created we need to remove them. Not removing the groupings can cause issues further down the line if you forget they are present. To remove the current groupings we need to use the `dplyr` function `ungroup()`.

```r
df <- dplyr::ungroup(df)
``` 

Like when using `group_by()`, `ungroup()` doesn't appear to change anything obvious in our data. However, if we review the attributes again, we see that the `$groups` attribute has now been removed and `grouped_df` is no longer an element of the `$class` attribute.

```r
attributes(df)
#' $class
#' [1] "tbl_df"     "tbl"        "data.frame"
#' 
#' $row.names
#'   [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18
#'  [19]  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36
#'  [37]  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53  54
#'  [55]  55  56  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71  72
#'  [73]  73  74  75  76  77  78  79  80  81  82  83  84  85  86  87  88  89  90
#'  [91]  91  92  93  94  95  96  97  98  99 100 101 102 103 104 105 106 107 108
#' [109] 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125 126
#' [127] 127 128 129 130 131 132 133 134 135 136 137 138 139 140 141 142 143 144
#' [145] 145 146 147 148 149 150
#' 
#' $names
#' [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```

### group_by() - multiple grouping column

Grouping can also be applied based on multiple columns. Lets add a new column to `df` to use for grouping. `Big.Petal` will add a logical value base don whether `Petal.Length` and `Petal.Width` are above average.

```r
df$Big.Petal <- df$Petal.Length > mean(df$Petal.Length) & df$Petal.Width > mean(df$Petal.Width)

print(df)
#' # A tibble: 150 × 6
#'    Sepal.Length Sepal.Width Petal.Length Petal.Width Species Big.Petal
#'           <dbl>       <dbl>        <dbl>       <dbl> <fct>   <lgl>    
#'  1          5.1         3.5          1.4         0.2 setosa  FALSE    
#'  2          4.9         3            1.4         0.2 setosa  FALSE    
#'  3          4.7         3.2          1.3         0.2 setosa  FALSE    
#'  4          4.6         3.1          1.5         0.2 setosa  FALSE    
#'  5          5           3.6          1.4         0.2 setosa  FALSE    
#'  6          5.4         3.9          1.7         0.4 setosa  FALSE    
#'  7          4.6         3.4          1.4         0.3 setosa  FALSE    
#'  8          5           3.4          1.5         0.2 setosa  FALSE    
#'  9          4.4         2.9          1.4         0.2 setosa  FALSE    
#' 10          4.9         3.1          1.5         0.1 setosa  FALSE    
#' # … with 140 more rows
#' # ℹ Use `print(n = ...)` to see more rows
```

We can now group our data by `Species` and `Big.Petal`.

```r
df <- dplyr::group_by(df, Species, Big.Petal)
```

This grouping allows us to mean `Sepal.Length` value by both `Species` and `Big.Petal`.

```r
dplyr::summarise(df, Mean.Sepal.Length = mean(Sepal.Length))
#' # A tibble: 4 × 3
#' # Groups:   Species [3]
#'   Species    Big.Petal Mean.Sepal.Length
#'   <fct>      <lgl>                 <dbl>
#' 1 setosa     FALSE                  5.01
#' 2 versicolor FALSE                  5.43
#' 3 versicolor TRUE                   6.08
#' 4 virginica  TRUE                   6.59
```

### group_by - keep all columns
Summarise works well, but it reduces the number of rows down to one row per distinct combination of the grouping variables, and drops columns that aren't grouping variables.

If we want to add the mean `Sepal.Length` value by `Species` and `Big.Petal` to our data 'as is', we need to use another `dplyr` function; `mutate()`. `mutate()` is used to add new columns.

```r
df <- dplyr::mutate(df, Mean.Sepal.Length = mean(Sepal.Length))

print(df)
#' # A tibble: 150 × 7
#' # Groups:   Species, Big.Petal [4]
#'    Sepal.Length Sepal.Width Petal.Length Petal.Width Species Big.Petal Mean.Se…¹
#'           <dbl>       <dbl>        <dbl>       <dbl> <fct>   <lgl>         <dbl>
#'  1          5.1         3.5          1.4         0.2 setosa  FALSE          5.01
#'  2          4.9         3            1.4         0.2 setosa  FALSE          5.01
#'  3          4.7         3.2          1.3         0.2 setosa  FALSE          5.01
#'  4          4.6         3.1          1.5         0.2 setosa  FALSE          5.01
#'  5          5           3.6          1.4         0.2 setosa  FALSE          5.01
#'  6          5.4         3.9          1.7         0.4 setosa  FALSE          5.01
#'  7          4.6         3.4          1.4         0.3 setosa  FALSE          5.01
#'  8          5           3.4          1.5         0.2 setosa  FALSE          5.01
#'  9          4.4         2.9          1.4         0.2 setosa  FALSE          5.01
#' 10          4.9         3.1          1.5         0.1 setosa  FALSE          5.01
#' # … with 140 more rows, and abbreviated variable name ¹​Mean.Sepal.Length
#' # ℹ Use `print(n = ...)` to see more rows
```

## Next steps

Grouping is a powerful and effective way to add summary statistics to your data and the `dplyr` package offers easy to use functions to achieve this.

Try some of the tasks below to put the theory into practice.

Create a `data.frame` named `df` using the below code.

<details>
  <summary>Reveal code</summary>

```r
df <- structure(list(Date = c(20210201L, 20210201L, 20210201L, 20210201L, 
20210201L, 20210201L, 20210201L, 20210201L, 20210201L, 20210201L, 
20210201L, 20210201L, 20210201L, 20210201L, 20210201L, 20210201L, 
20210201L, 20210201L, 20210201L, 20210201L, 20210202L, 20210202L, 
20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 
20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 
20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 20210202L, 
20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 
20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 
20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 20210203L, 
20210203L, 20210203L, 20210204L, 20210204L, 20210204L, 20210204L, 
20210204L, 20210204L, 20210204L, 20210204L, 20210204L, 20210204L, 
20210204L, 20210204L, 20210204L, 20210204L, 20210204L, 20210204L, 
20210204L, 20210204L, 20210204L, 20210204L, 20210205L, 20210205L, 
20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 
20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 
20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 20210205L, 
20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 
20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 
20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 20210206L, 
20210206L, 20210206L, 20210207L, 20210207L, 20210207L, 20210207L, 
20210207L, 20210207L, 20210207L, 20210207L, 20210207L, 20210207L, 
20210207L, 20210207L, 20210207L, 20210207L, 20210207L, 20210207L, 
20210207L, 20210207L, 20210207L, 20210207L), Sex = c("Female", 
"Female", "Female", "Female", "Female", "Female", "Female", "Female", 
"Female", "Female", "Male", "Male", "Male", "Male", "Male", "Male", 
"Male", "Male", "Male", "Male", "Female", "Female", "Female", 
"Female", "Female", "Female", "Female", "Female", "Female", "Female", 
"Male", "Male", "Male", "Male", "Male", "Male", "Male", "Male", 
"Male", "Male", "Female", "Female", "Female", "Female", "Female", 
"Female", "Female", "Female", "Female", "Female", "Male", "Male", 
"Male", "Male", "Male", "Male", "Male", "Male", "Male", "Male", 
"Female", "Female", "Female", "Female", "Female", "Female", "Female", 
"Female", "Female", "Female", "Male", "Male", "Male", "Male", 
"Male", "Male", "Male", "Male", "Male", "Male", "Female", "Female", 
"Female", "Female", "Female", "Female", "Female", "Female", "Female", 
"Female", "Male", "Male", "Male", "Male", "Male", "Male", "Male", 
"Male", "Male", "Male", "Female", "Female", "Female", "Female", 
"Female", "Female", "Female", "Female", "Female", "Female", "Male", 
"Male", "Male", "Male", "Male", "Male", "Male", "Male", "Male", 
"Male", "Female", "Female", "Female", "Female", "Female", "Female", 
"Female", "Female", "Female", "Female", "Male", "Male", "Male", 
"Male", "Male", "Male", "Male", "Male", "Male", "Male"), AgeGroup = c("0 to 14", 
"0 to 59", "15 to 19", "20 to 24", "25 to 44", "45 to 64", "60+", 
"65 to 74", "75 to 84", "85plus", "0 to 14", "0 to 59", "15 to 19", 
"20 to 24", "25 to 44", "45 to 64", "60+", "65 to 74", "75 to 84", 
"85plus", "0 to 14", "0 to 59", "15 to 19", "20 to 24", "25 to 44", 
"45 to 64", "60+", "65 to 74", "75 to 84", "85plus", "0 to 14", 
"0 to 59", "15 to 19", "20 to 24", "25 to 44", "45 to 64", "60+", 
"65 to 74", "75 to 84", "85plus", "0 to 14", "0 to 59", "15 to 19", 
"20 to 24", "25 to 44", "45 to 64", "60+", "65 to 74", "75 to 84", 
"85plus", "0 to 14", "0 to 59", "15 to 19", "20 to 24", "25 to 44", 
"45 to 64", "60+", "65 to 74", "75 to 84", "85plus", "0 to 14", 
"0 to 59", "15 to 19", "20 to 24", "25 to 44", "45 to 64", "60+", 
"65 to 74", "75 to 84", "85plus", "0 to 14", "0 to 59", "15 to 19", 
"20 to 24", "25 to 44", "45 to 64", "60+", "65 to 74", "75 to 84", 
"85plus", "0 to 14", "0 to 59", "15 to 19", "20 to 24", "25 to 44", 
"45 to 64", "60+", "65 to 74", "75 to 84", "85plus", "0 to 14", 
"0 to 59", "15 to 19", "20 to 24", "25 to 44", "45 to 64", "60+", 
"65 to 74", "75 to 84", "85plus", "0 to 14", "0 to 59", "15 to 19", 
"20 to 24", "25 to 44", "45 to 64", "60+", "65 to 74", "75 to 84", 
"85plus", "0 to 14", "0 to 59", "15 to 19", "20 to 24", "25 to 44", 
"45 to 64", "60+", "65 to 74", "75 to 84", "85plus", "0 to 14", 
"0 to 59", "15 to 19", "20 to 24", "25 to 44", "45 to 64", "60+", 
"65 to 74", "75 to 84", "85plus", "0 to 14", "0 to 59", "15 to 19", 
"20 to 24", "25 to 44", "45 to 64", "60+", "65 to 74", "75 to 84", 
"85plus"), FirstInfections = c(45L, 454L, 28L, 44L, 203L, 176L, 
132L, 37L, 25L, 28L, 36L, 395L, 19L, 46L, 162L, 166L, 114L, 41L, 
22L, 17L, 41L, 449L, 26L, 50L, 192L, 162L, 121L, 45L, 34L, 20L, 
33L, 407L, 22L, 48L, 183L, 148L, 72L, 20L, 20L, 5L, 31L, 375L, 
28L, 45L, 153L, 150L, 91L, 28L, 18L, 13L, 29L, 369L, 27L, 39L, 
154L, 143L, 95L, 35L, 25L, 12L, 27L, 362L, 25L, 38L, 160L, 146L, 
93L, 22L, 20L, 17L, 37L, 356L, 18L, 30L, 150L, 149L, 87L, 30L, 
19L, 10L, 33L, 360L, 31L, 51L, 141L, 127L, 119L, 39L, 22L, 35L, 
34L, 349L, 21L, 32L, 160L, 132L, 107L, 33L, 35L, 9L, 27L, 323L, 
28L, 34L, 140L, 113L, 74L, 28L, 15L, 12L, 30L, 267L, 16L, 28L, 
104L, 105L, 42L, 15L, 7L, 4L, 23L, 280L, 18L, 29L, 127L, 112L, 
71L, 17L, 10L, 15L, 31L, 246L, 15L, 20L, 106L, 92L, 57L, 18L, 
19L, 2L), Reinfections = c(0L, 5L, 0L, 1L, 3L, 1L, 2L, 1L, 0L, 
1L, 1L, 2L, 0L, 0L, 1L, 0L, 0L, 0L, 0L, 0L, 0L, 2L, 0L, 0L, 0L, 
2L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 1L, 
7L, 1L, 1L, 1L, 3L, 1L, 1L, 0L, 0L, 0L, 1L, 0L, 0L, 0L, 1L, 0L, 
0L, 0L, 0L, 0L, 2L, 0L, 1L, 0L, 1L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 
0L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 2L, 0L, 0L, 1L, 1L, 0L, 0L, 0L, 
0L, 0L, 2L, 0L, 0L, 1L, 1L, 1L, 1L, 0L, 0L, 0L, 2L, 0L, 0L, 2L, 
0L, 0L, 0L, 0L, 0L, 1L, 2L, 0L, 0L, 1L, 0L, 0L, 0L, 0L, 0L, 0L, 
1L, 0L, 0L, 1L, 0L, 1L, 0L, 0L, 1L, 0L, 0L, 0L, 0L, 0L, 0L, 0L, 
0L, 0L, 0L)), class = "data.frame", row.names = c(NA, -140L))
```
</details>

The data is from the 1st 7 days of February 2021 presenting daily counts of positive COVID-19 cases, broken down by age group and sex. The data is available from the [PHS Scotland open data platform](opendata.nhs.scot).

1. Group the data by sex and calculate the mean and median number of first infections.

2. Group the data by sex and age group and calculate the mean and median of both first infections and reinfections.

3. Group the data by sex and age group and calculate the totals of both first infections and reinfections.


<details>
  <summary>Answers</summary>

  You should aim for outputs like these;

  1.

```r
#'   # A tibble: 2 × 3
#'   Sex    mean_first_inf median_first_inf
#'   <chr>           <dbl>            <dbl>
#' 1 Female           94.4               40
#' 2 Male             84.7               35
```

2.

```r
#' # A tibble: 20 × 6
#' # Groups:   Sex [2]
#'    Sex    AgeGroup mean_first_inf median_first_inf mean_reinf median_reinf
#'    <chr>  <chr>             <dbl>            <int>      <dbl>        <int>
#'  1 Female 0 to 14           32.4                31      0.143            0
#'  2 Female 0 to 59          372.                362      3                2
#'  3 Female 15 to 19          26.3                28      0.143            0
#'  4 Female 20 to 24          41.6                44      0.429            0
#'  5 Female 25 to 44         159.                153      1.14             1
#'  6 Female 45 to 64         141.                146      1.14             1
#'  7 Female 60+              100.                 93      0.571            0
#'  8 Female 65 to 74          30.9                28      0.286            0
#'  9 Female 75 to 84          20.6                20      0                0
#' 10 Female 85plus            20                  17      0.286            0
#' 11 Male   0 to 14           32.9                33      0.286            0
#' 12 Male   0 to 59          341.                356      1                1
#' 13 Male   15 to 19          19.7                19      0                0
#' 14 Male   20 to 24          34.7                32      0                0
#' 15 Male   25 to 44         146.                154      0.429            0
#' 16 Male   45 to 64         134.                143      0.286            0
#' 17 Male   60+               82                  87      0.143            0
#' 18 Male   65 to 74          27.4                30      0.143            0
#' 19 Male   75 to 84          21                  20      0                0
#' 20 Male   85plus             8.43                9      0                0
```

3.

```r
#' # A tibble: 20 × 4
#' # Groups:   Sex [2]
#'    Sex    AgeGroup total_first_inf total_reinf
#'    <chr>  <chr>              <int>       <int>
#'  1 Female 0 to 14              227           1
#'  2 Female 0 to 59             2603          21
#'  3 Female 15 to 19             184           1
#'  4 Female 20 to 24             291           3
#'  5 Female 25 to 44            1116           8
#'  6 Female 45 to 64             986           8
#'  7 Female 60+                  701           4
#'  8 Female 65 to 74             216           2
#'  9 Female 75 to 84             144           0
#' 10 Female 85plus               140           2
#' 11 Male   0 to 14              230           2
#' 12 Male   0 to 59             2389           7
#' 13 Male   15 to 19             138           0
#' 14 Male   20 to 24             243           0
#' 15 Male   25 to 44            1019           3
#' 16 Male   45 to 64             935           2
#' 17 Male   60+                  574           1
#' 18 Male   65 to 74             192           1
#' 19 Male   75 to 84             147           0
#' 20 Male   85plus                59           0
```

</details>