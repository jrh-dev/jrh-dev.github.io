---
layout: post
title : "R Basics - Data Frames Part 2"
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

In Data Frames Part 1 we looked at creating data frames, accessing their columns, and taking subsets of them using the indices. We also looked at some useful functions for interacting with them, such as `ncol()`, `nrow()`, and `names()`.

## Working with `data.frame`'s

R comes with a variety of built in data sets which are typically used in tutorials. `mtcars` is one of these data sets. We can view the first few rows of `mtcars` to get a feel for the data set using the `head()` function.

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
The `tail()` function works similarly to `head()`, returning the last rows of a `data.frame`. `head()` and `tail()` can also be used on the columns, or in fact any vector.

```r
head(mtcars$mpg)

#` [1] 21.0 21.0 22.8 21.4 18.7 18.1
```

It's worth noting that `mtcars` doesn't actually have a column containing the car names, rather they are stored as row names. We can check and confirm this using the `row.names()` function.

```r
row.names(mtcars)

#' [1] "Mazda RX4"           "Mazda RX4 Wag"      
#' [3] "Datsun 710"          "Hornet 4 Drive"     
#' [5] "Hornet Sportabout"   "Valiant" 
#' ...
```

### Subset selected rows based on criteria

We know that we can access a data.frame's elements using the indices of the rows and columns that we want to include.

![](/assets/img/r_basics_dataframes_p2/img02.png)

If we don't include the comma and only pass one index (i.e. `df[1]`) then R assumes that we want a column. See [Data Frames Part 1](https://jrh-dev.github.io/posts/r_basics_dataframes_p1/) for further details.

We can work through some examples to explore more advanced ways in which to create subsets of `data.frame`'s.

#### Example - Create a `data.frame` from `mtcars` containing all rows for cars with over 200 horsepower

To achieve our goal we could look at `mtcars` and write down the row numbers where the value in the `hp` column is greater than 200, which would be rows 7, 15, 16, 17, 24, 29, and 31.

Using the index system we are able to create a subset of `mtcars` containing those rows.

```r
mtcars[c(7, 15, 16, 17, 24, 29, 31), ]

#'                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
#' Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
#' Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
#' Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
#' Camaro Z28          13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
#' Ford Pantera L      15.8   8  351 264 4.22 3.170 14.50  0  1    5    4
#' Maserati Bora       15.0   8  301 335 3.54 3.570 14.60  0  1    5    8
```

Note that we have used the `c()` function to create a vector containing the row numbers, and we have included the `,` even though we aren't specifying any columns. If we try to select the rows without including the `,`, R will assume we want columns, and since we only have 11 columns will return an error.

```r
mtcars[c(7, 15, 16, 17, 24, 29, 31)]

#' mtcars[c(7, 15, 16, 17, 24, 29, 31)]
#' Error in `[.data.frame`(mtcars, c(7, 15, 16, 17, 24, 29, 31)) : 
#' undefined columns selected
```

Whilst this approach works, its clearly not very scalable. It requires manually counting the rows we want and would be impractical with larger or more complex data sets. 

One way to approach the problem in a programmatic way is to use the `which()` function.

```r
hp_200 <- which(mtcars$hp > 200)

print(hp_200)

#' [1]  7 15 16 17 24 29 31
```

`which()` has returned the indices of elements with values greater than 200 in the `mtcars` `hp` column. We have assigned the output of `which()` to `hp_200`, so we can now reuse those values elsewhere, in this case to subset our data.frame.

```r
mtcars[hp_200, ]

#'                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
#' Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
#' Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
#' Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
#' Camaro Z28          13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
#' Ford Pantera L      15.8   8  351 264 4.22 3.170 14.50  0  1    5    4
#' Maserati Bora       15.0   8  301 335 3.54 3.570 14.60  0  1    5    8
```

It is possible to skip the step of assigning the output from `which()` altogether by using it within `[ ]` directly.

```r
# not run
mtcars[which(mtcars$hp > 200),]
```

We can also achieve the same result using logical (TRUE and FALSE) vectors. If we run `mtcars$hp > 200` we get a logical vector, where `TRUE` indicates that the vector element was greater than 200.

```r
mtcars$hp > 200

#'  [1] FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE
#' [12] FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE
#' [23] FALSE  TRUE FALSE FALSE FALSE FALSE  TRUE FALSE  TRUE FALSE
```

Logical vectors can be used to subset other vectors and `data.frame`'s. We can explore this in action with a simple example.

```r
my_vector <- c("Alan", "Harry", "Frances", "Polly", "Walt")

logical_vector <- c(TRUE, FALSE, TRUE, FALSE, TRUE)

my_vector[logical_vector]

#' [1] "Alan" "Frances" "Walt"
```

We can use this approach with `mtcars` to create the subset `data.frame` of cars with over 200 hp.

```r
mtcars[mtcars$hp > 200, ]

#'                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
#' Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
#' Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
#' Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
#' Camaro Z28          13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
#' Ford Pantera L      15.8   8  351 264 4.22 3.170 14.50  0  1    5    4
#' Maserati Bora       15.0   8  301 335 3.54 3.570 14.60  0  1    5    8
```

R often provides us with lots of ways to do the same thing. Which approach you choose to use will often come down to how the wider project is structured and is something that becomes more intuitive with experience. Considerations such as readability and how obvious the functionality of a piece of code is to a third party, or indeed yourself upon returning to it are important. 

For newcomers it is often best to work with the option you find most intuitive, but in essence all of these approaches give you the same output.

```r
# not run - all return the same object

mtcars[c(7, 15, 16, 17, 24, 29, 31), ]

mtcars[which(mtcars$hp > 200), ]

mtcars[mtcars$hp > 200, ]
```

#### A tidy approach?

Use of the tidyverse is somewhat ubiquitous in R and the tidy style is now often taught before base R. The example below demonstrates how we could produce a subset `data.frame` of cars with over 200 hp from `mtcars` in a tidy fashion.

```r
library(dplyr)

mtcars %>%
  dplyr::filter(hp > 200)
  
#'                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
#' Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
#' Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
#' Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
#' Camaro Z28          13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
#' Ford Pantera L      15.8   8  351 264 4.22 3.170 14.50  0  1    5    4
#' Maserati Bora       15.0   8  301 335 3.54 3.570 14.60  0  1    5    8
```

You will notice the use of the pipe operator `%>%` and that the `hp` column is referred to simply as `hp` rather than `mtcars$hp`. The `dplyr` functions try to be more intuitive by assuming `hp` in this instance will be a column in `mtcars`.

<div class = "blue" markdown="1">
<i class="fa-solid fa-circle-info fa-lg"></i>

***About pipes***

Pipes are commonly used in R to chain together multiple functions. There is also a native pipe operator in R `|>` (available in versions 4.1.0 and above). In its simplest usage the pipe operator takes the object to its left and passes it to its right as the first argument. 

In the example above, the pipe operator takes `mtcars` and passes it to the first argument of `dplyr::filter()`.

![](/assets/img/r_basics_dataframes_p2/img01.png)

We can write the snippet without pipes, whilst still using `dplyr::filter()`.

```r
# not run
dplyr::filter(mtcars, hp > 200)
```
</div>

<div class = "yellow" markdown="1">
<i class="fa-solid fa-circle-info fa-lg"></i>

***Base or tidy?***

A natural question to arise whilst learning R is whether to prefer base R or tidy R. As you become comfortable with the language you will likely develop a preference for approaching problems is a certain way and may prefer one framework over another. 

However, an either or approach is only likely to lead to problems down the line. Whilst learning the language it is preferable to be exposed to multiple approaches in order that you can understand code written by others and be aware of alternatives if you encounter a challenging problem.

</div>

### Subset selected columns based on criteria

We can select a subset of columns from `mtcars` in a few different ways.

#### Example - Create a data.frame containing the first 3 columns of `mtcars`

We can select columns using their indices. We don't need to include `,` within `[ ]` if we only want columns. It doesn't cause any issues if we do include `,`, but it must come ***before*** the column indices.

```r
mtcars[1:3]
#'                      mpg cyl  disp
#' Mazda RX4           21.0   6 160.0
#' Mazda RX4 Wag       21.0   6 160.0
#' Datsun 710          22.8   4 108.0
#' Hornet 4 Drive      21.4   6 258.0
#' Hornet Sportabout   18.7   8 360.0

identical(mtcars[, 1:3], mtcars[1:3])
#' [1] TRUE

identical(mtcars[1:3, ], mtcars[1:3])
#' [1] FALSE
```

#### Example - Create a data.frame containing specific columns of `mtcars`

We can use the column names to access specific columns of `mtcars`. The `names()` function can be really helpful here and allows us to check the name values available.

```r
names(mtcars)

#'  [1] "mpg"  "cyl"  "disp" "hp"   "drat" "wt"   "qsec" "vs"   "am"   "gear" "carb"
```

We can use the column names to extract the `mpg`, `cyl`, and `hp` columns from `mtcars`. Selecting columns in this way the return will always return a `data.frame`.

```r
mtcars[c("mpg", "cyl", "hp")]

#'                      mpg cyl  hp
#' Mazda RX4           21.0   6 110
#' Mazda RX4 Wag       21.0   6 110
#' Datsun 710          22.8   4  93
#' Hornet 4 Drive      21.4   6 110
#' Hornet Sportabout   18.7   8 175
#' ...
```

Sometimes you may want to select columns in a more dynamic way. Lets say we want a `data.frame` consisting of any column from `mtcars` with a name that is 2 characters long.

We can first get the number of characters in each of the column names and store the vector as `name_len`.

```r
name_len <- nchar(names(mtcars))

print(name_len)
#' [1] 3 3 4 2 4 2 4 2 2 4 4
```

We then convert the `name_len` numeric vector to logical one.

```r
name_len <- name_len == 2

print(name_len)
 [1] FALSE FALSE FALSE  TRUE FALSE TRUE FALSE  TRUE  TRUE FALSE FALSE
```

Finally, we can now use `name_len` to select all of the columns from `mtcars` with names 2 characters long.

```r
mtcars[name_len]

#'                      hp    wt vs am
#' Mazda RX4           110 2.620  0  1
#' Mazda RX4 Wag       110 2.875  0  1
#' Datsun 710           93 2.320  1  1
#' Hornet 4 Drive      110 3.215  1  0
#' Hornet Sportabout   175 3.440  0  0
#' ...
```

As with our examples above, we can avoid the intermediate steps by writing a concise one-liner.

```r
mtcars[nchar(names(mtcars)) == 2]

#'                      hp    wt vs am
#' Mazda RX4           110 2.620  0  1
#' Mazda RX4 Wag       110 2.875  0  1
#' Datsun 710           93 2.320  1  1
#' Hornet 4 Drive      110 3.215  1  0
#' Hornet Sportabout   175 3.440  0  0
#' ...
```

#### Example - Create a data.frame containing specific columns and rows of `mtcars`

We can combine what we have looked at so far to create a subset of both columns and rows from `mtcars`. To extract the `mpg`, `cyl`, and `hp` columns from `mtcars`, but only where the hp is greater than 200, we can combine the `mtcars[mtcars$hp > 200]` and `mtcars[c("mpg", "cyl", "hp")]` code snippets we used earlier.

At this point we also need to remember the order in which we specify rows and columns inside `[ ]`.

![](/assets/img/r_basics_dataframes_p2/img02.png)

```r
mtcars[mtcars$hp > 200, c("mpg", "cyl", "hp")]

#'                      mpg cyl  hp
#' Duster 360          14.3   8 245
#' Cadillac Fleetwood  10.4   8 205
#' Lincoln Continental 10.4   8 215
#' Chrysler Imperial   14.7   8 230
#' Camaro Z28          13.3   8 245
#' Ford Pantera L      15.8   8 264
#' Maserati Bora       15.0   8 335
```

It's often useful to be able to select rows based on the values in a column that you don't want to retain. For example, if we want the `mpg`, `cyl`, and `hp` columns from `mtcars`, where `carb` is greater than 4, we don't actually need to include `carb` in our new `data.frame`.

```r
mtcars[mtcars$carb > 4, c("mpg", "cyl", "hp")]

#'                mpg cyl  hp
#' Ferrari Dino  19.7   6 175
#' Maserati Bora 15.0   8 335
```

### & AND | OR

The logical `&` (AND) and `|` (OR) operators are very important in any programming language, allowing us to build logic based on multiple conditions. The following examples demonstrate the returns generated by using the logical operators in different situations.

```r
TRUE & TRUE
[1] TRUE

TRUE & FALSE
[1] FALSE

FALSE & FALSE
[1] FALSE

TRUE & TRUE & TRUE
[1] TRUE

TRUE & TRUE & FALSE
[1] FALSE

TRUE | FALSE
[1] TRUE

> TRUE | TRUE
[1] TRUE

TRUE | FALSE | FALSE
[1] TRUE
```

In the examples above we returned rows from `mtcars` where the `hp` value is greater than 200 and the `carb` value is greater than 4. We can combine these to return rows where both conditions are `TRUE` using the `&` operator.

```r
mtcars[mtcars$carb > 4 & mtcars$hp > 200, ]

#'               mpg cyl disp  hp drat   wt qsec vs am gear carb
#' Maserati Bora  15   8  301 335 3.54 3.57 14.6  0  1    5    8
```

We can also return the rows of `mtcars` where either of the conditions are `TRUE`.

```r
mtcars[mtcars$carb > 4 | mtcars$hp > 200, ]

#'                      mpg cyl disp  hp drat    wt  qsec vs am gear carb
#' Duster 360          14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
#' Cadillac Fleetwood  10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
#' Lincoln Continental 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
#' Chrysler Imperial   14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
#' Maserati Bora       15.0   8  301 335 3.54 3.570 14.60  0  1    5    8
#' ...
```

We might not always want to create a new `data.frame`, perhaps we simply want to know how many rows fulfil a certain criteria. We can use the `nrow()` function for this.

```r
nrow(mtcars[mtcars$carb > 4,])
[1] 2

nrow(mtcars[mtcars$carb > 4 & mtcars$hp > 200, ])
[1] 1

nrow(mtcars[mtcars$carb > 4 | mtcars$hp > 200, ])
[1] 8
```













