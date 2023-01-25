---
layout: article
title : "R Apply Functions"
toc : true
tags: [R basics, Tutorial]
author: <author_id>
show_edit_on_github: false
---

We previously looked at loops in R , but R also provides the 'apply' family of functions which can be used to perform similar actions to loops.

## When to use an 'apply' function

Historically, there was some consensus in the R community that the 'apply' family of functions were superior to traditional for loops, though this is certainly no longer the case (if it ever was). However, in some circumstances using an 'apply' function may enable you to write cleaner, more concise code.

## Meet the family

The functions we will look at are;

* `apply()`

* `lapply()`

* `sapply()`

* `tapply()`

* `mapply()`

## `apply()`

`apply(X, MARGIN, FUN, ..., simplify = TRUE)`

The documentation for `apply()` tell's us that it "returns a vector or array or list of values obtained by applying a function to margins of an array or matrix.". Whilst that might sound quite complex, we can think of the 'margins' as being analogous to rows and columns. 

Lets start with a matrix.

{% highlight r %}

> mat <- matrix(1:9, 3, 3)
> print(matrix)
#'      [,1] [,2] [,3]
#' [1,]    1    4    7
#' [2,]    2    5    8
#' [3,]    3    6    9

{% endhighlight r %}

Lets use `apply()` to calculate the sum of each row.

{% highlight r %}

> apply(mat, MARGIN = 1, FUN = sum)
#' [1] 12 15 18

{% endhighlight r %}

We use the `MARGIN` argument to specify whether we want to perform an operation over a row or column, with `1` selecting rows, and `2` selecting columns. With that in mind, we can attain sums of the columns by changing the `MARGIN` argument.

{% highlight r %}

> apply(mat, MARGIN = 2, FUN = sum)
#' [1]  6 15 24

{% endhighlight r %}

We can also use `apply` with a `data.frame` object.


{% highlight r %}

>mat <- as.data.frame(mat)

> apply(mat, MARGIN = 1, FUN = sum)
[1] 12 15 18

{% endhighlight r %}

However, we would typically use a `data.frame` to store data of multiple types, if we add a character based column we run into problems.

{% highlight r %}

> mat$id <- letters[1:3]

> print(mat)
#'   V1 V2 V3 id
#' 1  1  4  7  a
#' 2  2  5  8  b
#' 3  3  6  9  c

> apply(mat, MARGIN = 1, FUN = sum)
#' Error in FUN(newX[, i], ...) : invalid 'type' (character) of argument

{% endhighlight r %}

Let's add another new column, 'total', to store the sum of 'V1', 'V2', and 'V3' from our example. We can still use the apply function for this, but we need to ensure that we only point it to the columns that we want to be included in the sum.

{% highlight r %}

> mat$total <- apply(mat[c("V1","V2","V3")], MARGIN = 1, FUN = sum)

> print(mat)
  V1 V2 V3 id total
1  1  4  7  a    12
2  2  5  8  b    15
3  3  6  9  c    18

{% endhighlight r %}

## `lapply()`

`lapply(X, FUN, ...)`

`lapply()` is used to apply a function over a list or vector and always returns a list of the same length as the list or vector that it operates over.

