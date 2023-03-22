---
layout: article
title : "R Functions - Part 2"
toc : true
tags: [R Intermediate, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

In R Functions - Part 1 we looked at creating functions, positional and named arguments, returns, and default values. We also considered the function environment. This provides all the basic knowledge to start using functions in your code. <!--more-->

In Part 2, we will delve deeper into the advanced usage of functions in R. Specifically, we will cover topics such as input validation, anonymous functions, ellipsis (`...`), lazy evaluation, recursion, and method dispatch. 

By the end of this article, you will have a solid understanding of how to leverage these advanced function features to write more efficient and powerful code in R. So, let's get started!

## Input validation

Function input validation isn't essential to constructing a function in R, but it is highly recommended. Two key benefits are;

* To protect against unexpected changes in your source data
* To ensure correct usage by your future self or others

Let's say that we have a function like this;

```r
my_fun <- function(x) {
  
    Sys.sleep(10)
  
    return(x + 1)
}
```

`Sys.sleep()` is used to suspend the execution of R expressions. In a real world scenario `Sys.sleep(10)` could be replaced with a long running block of code that isn't directly dependent on our the value of the `x` argument.

The function is intended to take a numeric value, so both integers and double types can be passed as `x`.

```r
my_fun(5)

# [1] 6

my_fun(5.5)

# [1] 6.5
```

However, passing a character value as `x` will cause an error.

```r
my_fun("1")

# Error in x + 1 : non-numeric argument to binary operator
```

The error message isn't too difficult to understand, but if you're not familiar with the function internals it may not be immediately obvious what has gone wrong.

Due to `x` not being used until later in the function we also have another issue, time and compute resources were wasted whilst the earlier code (not dependent on `x`) ran, only for that work to be discarded once the error was raised.

Input validation can help us to make the function more robust.

### Validation with `if ()`

Arguably the simplest form of validation is to utilise an `if (0)` statement.

```r
my_fun <- function(x) {

    if (!is.numeric(x)) {
        stop("x must be numeric")
    }
  
    Sys.sleep(10)
  
    return(x + 1)
}
```

We use `!is.numeric(x)` (which returns a logical value) to test whether the value of `x` is numeric. Negating the function return by pre-pending `!` 'flips' the return so we're going to get `TRUE` if `x` ***is not*** numeric, and `FALSE` if `x` ***is*** numeric.

`stop()` stops execution and raises an error. We can specify the error message to be returned.

Now, if we run our function with a character value as `x` we will get an informative error, and no unnecessary processing will take place.

```r
my_fun("1")

# Error in my_fun("1") : x must be numeric
```

### Validation with `stopifnot()`

`stopifnot()` is a commonly used alternative to using an `if` statement for input validation.

```r
my_fun <- function(x) {

    stopifnot(is.numeric(x))

    Sys.sleep(10)
  
    return(x + 1)
}

my_fun("1")

# Error in my_fun("1") : is.numeric(x) is not TRUE
```

`stopifnot()` has the benefit of being simpler to write than the example using an `if` statement. 

### Validation with the `assertthat` package

I'm normally not one to recommend additional libraries when adequate base R functionality exists, however, I do like the the `asserthat` package for input validation.

```r
library(assertthat)

my_fun <- function(x) {

    assert_that(is.numeric("1"), msg = "x must be numeric")

    Sys.sleep(10)
  
    return(x + 1)
}

my_fun("1")

# Error: x must be numeric
```

One advantage of `assertthat` is that it gives pretty good error messages by default.

```r
library(assertthat)

my_fun <- function(x) {

    assert_that(is.numeric("1"), msg = "x must be numeric")

    Sys.sleep(10)
  
    return(x + 1)
}

my_fun("1")

# Error: "1" is not a numeric or integer vector
```

The quality of the default messages and the simplicity of use make a strong argument for using `assertthat` and it's pretty light with few dependencies.

### Ensuring valid choices with `match.arg()`

Let's write a function that can be used to get the sum or product of a vector.

```r
my_fun <- function(x, perform = c("sum", "prod")) {
  match.arg(perform, c("sum", "prod"))
  
  if (perform == "sum") {
    return(sum(x))
  } else {
    return(prod(x))
  }
}

my_fun(1:10, perform = "sum")

# [1] 55

my_fun(1:10, perform = "prod")

# [1] 3628800

my_fun(1:10, perform = "mean")

# Error in match.arg(perform, c("sum", "prod")) :
#   'arg' should be one of "sum", "prod"
```

Our function enforces the use of a predefined value for `perform` and returns an informative error when passed something else.

## Anonymous functions

Anonymous functions is R are a little ugly, there's no way around it. that's not to say, however, that they aren't without their uses.

An anonymous function is never assigned to anything, so it can't be called again later, hence the name. It is used for creating small one-off functions, simplifying code, and implementing functional programming concepts.

The main advantages of using an anonymous function are:

* One-off usage: If you have a small task that you need to accomplish just once, creating a named function can be cumbersome. In such cases, you can use an anonymous function to perform the task quickly and easily whilst maintaining the benefits of functional programming, particularly the function scope and isolation. 

* Functional programming: R is a functional programming language, and anonymous functions are a fundamental tool in functional programming.

* Avoid namespace pollution: If you have a script that defines many single use functions, there may be a lot of names in the global environment, which can be confusing and increase the risk of name conflicts. By using anonymous functions, you can avoid polluting the namespace with unnecessary function names and benefit from any objects created within the closure staying out of the global environment.


Let's look at an example of an anonymous function which divides 2 numbers.

```r
(function(x, y) { x / y })(10, 2)

# [1] 5
```

Notice how we don't assign the function to anything, and wrap the entire function (including the body) in parentheses `( )`. We also follow the function definition with another set of parentheses in which we define the values of the arguments `x` and `y`. This may be simpler to demonstrate if we use named arguments;

```r
# not run
(function(x, y) { x / y })(x = 10, y = 2)
```

We can assign the output of the anonymous function.

```r
answer <- (function(x, y) { x / y })(x = 10, y = 2)

print(answer)

# [1] 5
```

Note how this assignment catches the output of the anonymous function and not the function itself. We can't use `answer` as a function call.

```r
answer(10, 2)

# Error in answer(10, 2) : could not find function "answer"
```

The main place where you will commonly see anonymous functions used in R is within other functions. For example;

```r
my_list <- list(a = 1, b = 2, c = 3)

lapply(my_list, function(x) { x + 1 })

# [[1]]
# [1] 2
# 
# [[2]]
# [1] 3
# 
# [[3]]
# [1] 4
```

Here we have passed an anonymous function to the `FUN` argument of `lapply()`. The anonymous function is applied to each element of the list in turn.

## `...` (ellipsis)

In R, ... (called "ellipsis") is a special function argument that allows to pass any number of arguments to a function. It can be used to make a function more flexible and generic and to facilitate the future expansion of a function.

Let's create a function to concatenate strings.

```r
concat <- function(...) {
  result <- paste(..., sep = " ")
  return(result)
}

concat("hello", "world")

# [1] "hello world"

concat("I", "like", "to", "concatenate", "strings")

# [1]"I like to concatenate strings"

concat()

# character(0)

```
In the example, we define a function `concat()`, that uses `...` to allow the function to accept a variable number of arguments. Inside the function, we use the paste() function to concatenate the arguments together into a single string.

We can pass any number of arguments to `concat()`, even no arguments.

## Lazy evaluation

Lazy evaluation is a feature where the expressions passed as arguments to a function are not evaluated until they are required within the function's body. This means that the actual computation is deferred until it is required, which can improve performance and reduce memory usage. If an argument is never used then the evaluation doesn't take place at all.

Let's check this out with a simple example. We will define 3 functions.

```r
expensive_fun <- function() {
  print("having expensive fun")
  Sys.sleep(10)
  return(invisible())
}

cheap_fun <- function() {
  print("having cheap fun")
  return(invisible())
}

runner <- function(x, y, option = c(1,2)) {
  if (option == 1) {
    x
  } else {
    y
  }
  return(invisible())
}
```

`expensive_fun()` does nothing besides printing "having expensive fun" and sleeping for 10 seconds. 

`cheap_fun()` prints "having fun" and does nothing else. 

`runner()` accepts 2 arguments, only one of which will ever be used depending upon the value passed as the `option` argument.

We can pass `expensive_fun()` and `cheap_fun()` as the `x` and `y` arguments of `runner()` to help us understand lazy evaluation.

```r
runner(expensive_fun(), cheap_fun(), option = 1)

# [1] "having expensive fun"

runner(expensive_fun(), cheap_fun(), option = 2)

# [1] "having fun"
```

We can see here that because R utilises lazy evaluation, only the arguments we call are evaluated. We can contrast this with the same code written in python, where lazy evaluation is not implemented.

```python
from time import sleep

def expensive_fun() -> None:
    print('having expensive fun')
    sleep(10)

def cheap_fun() -> None:
    print('having cheap fun')

def runner(x: callable, y: callable, option: int) -> None:
    if option == 1:
        x
    else:
        y

runner(expensive_fun(), cheap_fun(), 1)

# having expensive fun
# having cheap fun

runner(expensive_fun(), cheap_fun(), 2)

# having expensive fun
# having cheap fun
```

We can see that in python both the `x` and `y` arguments are evaluated before they are passed to the function, causing both to run in the example.

## Recursion

Recursion is a programming technique where a function calls itself. The topic is broad and outside the scope of this tutorial, but lets explore an example to introduce the concept.

```r
fib <- function(n) {
  if (n < 1) {
    return(NULL)
  } else if (n == 1) {
    return(0)
  } else if (n == 2) {
    return(1)
  } else {
    return(fib(n-1) + fib(n-2))
  }
}
```

This function can be used to return the nth value of a Fibonacci sequence. For values greater than 2 the function actually calls itself. This is recursion.

Let's get the first 10 values of the sequence.

```r
for (i in 1:10){
    print(fib(i)) 
}  
# [1] 0
# [1] 1
# [1] 1
# [1] 2
# [1] 3
# [1] 5
# [1] 8
# [1] 13
# [1] 21
# [1] 34
```

Note, however, that a recursive Fibonacci sequence generator can become inefficient as the value of n increases, so use this as an example only.

## Method dispatch

Method dispatch is arguably a cross-over between functions and R's class systems. We will only briefly mention it here.

Method dispatch is the name of the process in R where an appropriate function (or method) is selected based on the arguments passed to a function. In R, method dispatch is handled by the S3 and S4 object-oriented systems.

To utilise method dispatch we start by defining a generic function. This example contains nothing more than the `UseMethod()` function.

```r
my_fun <- function(x) {
  UseMethod("my_fun")
}
```

`UseMethod()` to select the appropriate method based on the class of the argument. Lets create two methods, one to handle a character value being passed to `my_fun()` and another to handle a numeric value.

```r
my_fun.character <- function(x) {
  paste("The argument is a character string:", x)
}

my_fun.numeric <- function(x) {
  paste("The argument is a numeric value:", x)
}
```

The methods share the name of the generic `my_fun()` but with a `.` followed by the type it is to handle.

If we pass a string to the generic `my_fun()`, the method dispatch system calls `my_fun.character()`.

```r
my_fun("a string")

[1] "The argument is a character string: a string"
```

Likewise, if we pass a numeric value `my_fun.numeric()` is called.

```r
my_fun(1)

[1] "The argument is a numeric value: 1"
```

If we try to pass `my_fun()` a value which has no method defined, we receive an error.

```r
my_fun(TRUE)

# Error in UseMethod("my_fun") :
#   no applicable method for 'my_fun' applied to an object of class "logical"
```

Method dispatch is a powerful feature in R primarily used to allow functions to behave differently based on the type of the input arguments.

## Next steps

This tutorial covers some of the more advanced aspects of functions in R. Many of the topics discussed here, in particular recursion and method dispatch, are part of much wider subject areas and you are encouraged to research these further.
