---
layout: post
title : "R Basics - Vectors"
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
 

In R, a vector is a series of elements with the same type. There are 4 important types to be aware of, that you will commonly encounter;

* Character
* Logical
* Double
* Integer

You may hear these being referred to as atomic vectors. It's also useful to note that the term 'numeric' is often used interchangeably for both double and integer types.

```r
# a character type vector
> c("a", "b", "c", "d", "e")

[1] "a" "b" "c" "d" "e"

# a logical type vector
> c(TRUE, FALSE, FALSE, TRUE, TRUE)

[1]  TRUE FALSE FALSE  TRUE  TRUE

# a double type vector
> c(1.2, 4.9, 2.1, 6.3, 1.7)

[1] 1.2 4.9 2.1 6.3 1.7

# an integer type vector
> c(1L, 2L, 3L, 4L, 5L)

[1] 1 2 3 4 5
```

### c()

`c()` is a built-in R function that combines the elements it is passed into a vector. You can provide any number of elements to `c()`. 

It is important to be aware that `c()` coerces elements to a common type. This is a common occurrence in R and understanding the implications can help to avoid errors. R is what is referred to as a weakly typed language, this means that sometimes a type will be coerced into another when code is executed.

Thinking of the 4 common types in R type coercion is performed according to a hierarchy;

`Logical >>> Integer >>> Double >>> Character`

You can test how this works using `typeof()`.

```r
# logical and double coerced to double
> typeof(c(TRUE, 1.5))

[1] "double"

# integer and double coerced to double
> typeof(c(1L, 1.5))

[1] "double"

# logical and character coerced to character
> typeof(c(TRUE, "a"))

[1] "character"

# logical, integer, double, and character coerced to character
> typeof(c(TRUE, 1, 1.5, "a"))

[1] "character"
```

### Assignment

In order to be able to refer to or use an object in R after it's creation, we first need to assign it a name. The R assignment operator `<-` is the best way of achieving this. It is also possible to use `=` exclusively for this task, however, this goes against the community consensus so should be avoided.

```r
> my_value <- "assign this string to my_value"

> print(my_value)

[1] "assign this string to my_value"
```

### Functions - arguments and use

In R we use functions extensively, in their simplest form a function takes inputs (referred to as arguments) and returns an output. The `sum()` function returns the sum of all the values present in its arguments.

```r
> sum(c(1, 2, 3, 4, 5))

[1] 15
```

Most functions allow for more than one argument, `sum()` for example has an argument `na.rm`. The `na.rm` argument accepts a logical (`TRUE` or `FALSE`) value. Setting the argument to `TRUE` will remove missing values.

```r
> sum(c(1, 2, NA, 4, 5))

[1] NA

> sum(c(1, 2, NA, 4, 5), na.rm = TRUE)

[1] 12
```

Function arguments are positional, so it is not strictly necessary to write the `na.rm = TRUE` part of the code in full. As `na.rm` is the 2nd argument, passing `TRUE` in the second position (positions are seperated by `,`) is sufficient.

```r
> sum(c(1, 2, NA, 4, 5), TRUE)

[1] 12
```

## Indicies in R

In R indices start at 1. Its useful to know that you can define a range of numbers using the `:` operator. Instead of writing `c(1, 2, 3, 4, 5)` to generate a vector of the numbers 1 to 5, you can write `1:5`.

```r
> t1 <- c(1L, 2L, 3L, 4L, 5L)

> t2 <- 1:5

> identical(t1, t2)

[1] TRUE
```

You can use the index system to access elements of a vector. `letters` is a constant built into R, it is in effect a vector of the letters of the alphabet in lower case.

To access a single element of a vector, for example the 13th letter of the alphabet, you can use the index system.

```r
> letters[13]

[1] "m"
```

If you want to get the first 5 letters of the alphabet you can also use the index system with a range.


```r
> letters[1:5]

[1] "a" "b" "c" "d" "e"
```

The next 5 letters can be accessed in the same way.


```r
> letters[6:10]

[1] "f" "g" "h" "i" "j"

```

To get the 2nd, 4th, and 6th letters of the alphabet, you can use `c()`.

```r
> letters[c(2, 4, 6)]

[1] "b" "d" "f"
```

If you need to know the length of a vector, the `length()` function is particularly useful.

```r
length(letters)
[1] 26
```
Knowing how to check the length of a vector can be quite useful, it allows you to do things like get everything after the 20th element (inclusive), or even the last 5 elements.

```r
> letters[20:length(letters)]

[1] "t" "u" "v" "w" "x" "y" "z"

> len <- length(letters)

> letters[(len - 4):len]

[1] "v" "w" "x" "y" "z"

```

You can also use indexing to change the order of the elements returned, for example you can return the first 5 letters of the alphabet in reverse order.

```r
> letters[5:1]

[1] "e" "d" "c" "b" "a"
```

The `which()` function returns the indices of a vector that evaluate to `TRUE`. You can use it to find the index of `j` in the `letters` constant.

```r
> which(letters == "j")

[1] 10
```

You can also use `which()` to create a subset from the letters vector.

```
> letters[which(letters == "j")]

[1] "j"
```

However, the which function is actually unnecessary here, as it's much simpler to do;

```r
> letters[letters == "j"]

[1] "j"
```

## Working with vectors

The `Nile` data-set, a time-series of length 100, is bundled with R. It provides measurements of the annual flow of the river Nile at Aswan between 1871 and 1970.

```r
> Nile

Time Series:
Start = 1871 
End = 1970 
Frequency = 1 
  [1] 1120 1160  963 1210 1160 1160  813 1230 1370 1140  995  935 1110
 [14]  994 1020  960 1180  799  958 1140 1100 1210 1150 1250 1260 1220
 [27] 1030 1100  774  840  874  694  940  833  701  916  692 1020 1050
 [40]  969  831  726  456  824  702 1120 1100  832  764  821  768  845
 [53]  864  862  698  845  744  796 1040  759  781  865  845  944  984
 [66]  897  822 1010  771  676  649  846  812  742  801 1040  860  874
 [79]  848  890  744  749  838 1050  918  986  797  923  975  815 1020
 [92]  906  901 1170  912  746  919  718  714  740
```

You can get the mean measurement with the `mean()` function.

```r
> mean(Nile)

[1] 919.35
```

The mean of the most recent 10 years worth of data can be found by using the index to get a subset of the vector.

```r
> mean(Nile[91:100])

[1] 874.6

# or obtaining the last 10 years programatically
> len <- length(Nile)

> mean(Nile[(len - 9):len])

[1] 874.6
```

You could check the number of years in which the flow was over 1000.

```r
> Nile > 1000

Time Series:
Start = 1871 
End = 1970 
Frequency = 1 
  [1]  TRUE  TRUE FALSE  TRUE  TRUE  TRUE FALSE  TRUE  TRUE  TRUE FALSE
 [12] FALSE  TRUE FALSE  TRUE FALSE  TRUE FALSE FALSE  TRUE  TRUE  TRUE
 [23]  TRUE  TRUE  TRUE  TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE
 [34] FALSE FALSE FALSE FALSE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE
 [45] FALSE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [56] FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
 [67] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE
 [78] FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE
 [89] FALSE FALSE  TRUE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE
[100] FALSE
```

The `TRUE` values indicate elements greater than 1000. However, counting these manually wouldn't be very efficient. Helpfully, logical values can be interpreted as numbers, with `TRUE` being 1 and `FALSE` being 0. With that in mind, the `sum()` function can be used to get the number of observations `>` 1000.

```r
> sum(Nile > 1000)

[1] 30
```

You might have noticed that in `Nile > 1000`, `Nile` is a vector of 100 numeric elements, doubles to be precise, whereas the '1000' is a single element. In this case R 'recycles' the '1000' and uses it against each of the elements of `Nile`.


## Next Steps

The `WWWusage` is another time-series data-set that is bundled with R, this time recording the numbers of users connected to the Internet through a server every minute.

1. What is the length of `WWWusage`?

2. What is the mean number of connected users?

3. How many observations recorded less than 100 users?

4. What is the sum of observations of greater than 100 users?

5. Calculate the sum of the means of the first and last 20 observations?


<details>
  <summary>Answers</summary>
  
  1. 100 <br>
    
  2. 137.08 <br>
    
  3. 27 <br>
    
  4. 11,313 <br>
    
  5. 292.25 <br>
    
</details>


