---
layout: post
title : "R Basics"
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

## Vectors

In R a vector is a series of elements with the same type. There are 4 important types to be aware of, that you will encounter commonly;

* Character
* Logical
* Double
* Integer

You may hear these being referred to as atomic vectors. The term 'numeric' is used interchangeably for double and integer types.

```r
# a character type vector
c("a", "b", "c", "d", "e")

# a logical type vector
c(TRUE, FALSE, FALSE, TRUE, TRUE)

# a double type vector
c(1.2, 4.9, 2.1, 6.3, 1.7)

# an integer type vector
c(1L, 2L, 3L, 4L, 5L)
```

### `c()`

`c()` is a built-in R function that combines the elements it is passed into a vector. You can provide any number of elements to `c()`. 

It is important to be aware that `c()` coerces elements to a common type. This is a common occurrence in R and understanding the implications can help to avoid errors.

Thinking of the 4 common types in R type coercion is performed according to a hierarchy;

`Logical |> Integer |> Double |> Character`

You can test how this works using `typeof()`.

```r
# logical and double coerced to double
typeof(c(TRUE, 1.5))
[1] "double"

# integer and double coerced to double
typeof(c(1L, 1.5))
[1] "double"

# logical and character coerced to character
typeof(c(TRUE, "a"))
[1] "character"

# logical, integer, double, and character coerced to character
typeof(c(TRUE, 1, 1.5, "a"))
[1] "character"
```

### Assignment

When we want to be able to refer to or use an object in R, we need to assign it a name. The R assignment operator `<-` is the best way of achieving this. It is also possible to use `=` exclusively for this task, however, this goes against the community consensus so should be avoided.

```r
my_value <- "assign this string to my_value"

my_value
[1] "assign this string to my_value"
```

### Functions - arguments and use

In R we use functions extensively, in the simplest form a function takes an input (referred to as an arguments) and returns an output. We can look at the `sum()` function.

The `sum()` function returns the sum of a numeric (or logical; `TRUE` = 1 & `FALSE` = 0) value.

```r
sum(c(1, 2, 3, 4, 5))
[1] 15
```

Most functions allow for more than one argument, `sum()` for example has an argument `na.rm`. The `na.rm` argument accepts a logical (`TRUE` or `FALSE`) value. Setting the argument to `TRUE` will remove missing values.

```r
sum(c(1, 2, NA, 4, 5))
[1] NA

sum(c(1, 2, NA, 4, 5), na.rm = TRUE)
[1] 12
```

Function arguments are positional, so it is not strictly necessary to write the `na.rm = TRUE` part of the code in full. As `na.rm` is the 2nd argument, passing `TRUE` in the second position (positions are seperated by `,`) is sufficient.

```r
sum(c(1, 2, NA, 4, 5), TRUE)
[1] 12
```

## Indicies in R

In R indices start at 1. Its useful to know that you can define a range of numbers using the `:` operator. Instead of writing `c(1, 2, 3, 4, 5)` to generate a vector of the number 1 to 5, you can write `1:5`.

```r

t1 <- c(1L, 2L, 3L, 4L, 5L)

t2 <- 1:5

identical(t1, t2)

[1] TRUE
```

You can use the index to access elements of a vector. `letters` is a constant built into R, it is in effect a vector of the letters of the alphabet in lower case.

To access a single element of a vector, for example the 13th letter of the alphabet, you can do so using the index system.

```r
letters[13]
[1] "m"
```

If you want to get the first 5 letters of the alphabet you can also use the index system with a range.


```r
letters[1:5]
[1] "a" "b" "c" "d" "e"
```

The next 5 letters can be accessed in the same way.


```r
letters[6:10]
[1] "f" "g" "h" "i" "j"

```

To get the 2nd, 4th, and 6th letters of the alphabet, you can use `c()`.

```r
letters[c(2, 4, 6)]
[1] "b" "d" "f"
```

If you need to know the length of a vector, the `length()` function is particularly useful.

```r
length(letters)
[1] 26
```
Knowing how to check the length of a vector can be quite useful, it allows you to do things like get everything after the 20th element (inclusive), or even the last 5 elements.

```r
letters[20:length(letters)]
[1] "t" "u" "v" "w" "x" "y" "z"

len <- length(letters)

letters[(len - 4):len]
[1] "v" "w" "x" "y" "z"

```

You can also use indexing to change the order of the elements returned, for example you can return the first 5 letters of the alphabet in reverse order.

```r
letters[5:1]
[1] "e" "d" "c" "b" "a"
```

The `which()` function returns the indices of a vector that evaluate to `TRUE`. You can find the index of `j` in the `letters` constant with it.

```r
which(letters == "j")
[1] 10
```

You can also use this to create a subset from the letters vector.

```
letters[which(letters == "j")]
[1] "j"
```

However, the which function is actually unecessary, as it's much simpler to do;

```r
letters[letters == "j"]
[1] "j"
```














