---
layout: article
title : "R Functions - Part 1"
toc : true
tags: [R Intermediate, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

A function is a 'chunk' of code that can be called and re-used. It often accepts 'arguments' that can be used to modify the behaviour or value which the function returns. <!--more-->

Functions allow us to break code down into smaller logical chunks and write focused modular units which assist in producing well written code.

## Functions in R

In R, we have what are referred to as first class functions. This means that R allows us pass functions as arguments to other functions, return them as the values from other functions, and assign them to variables or store them in a data structure.

## Creating a function

To create a function, we will assign an identifier with which to call it, in this instance "my_fun" will suffice. It is also possible to create functions without an identifier, known as anonymous functions, though anonymous functions are outside the scope of this tutorial. 

Let's assign `my_fun` to the output of `function()` to create a basic function.

```r
my_fun <- function() {}
```

Note that we need to follow `function()` with `{ }`. The curly-braces (`{}`) are used to contain the body of the function (the code that will run when we call the function). It is possible to write a one line function without `{ }`, but for readability and consistency, I advocate to always include them.

So far we haven't included anything in the body of the function, so whilst we can call it, it will simply return `NULL`.

Having defined a function, calling it is simple.

```r
my_fun()

NULL  
```

To capture the return of a function we assign it in the typical way.

```r
my_fun_return <- my_fun()

print(my_fun_return)

NULL
```

A `NULL` return isn't very exciting, so let's add some functionality to our function. For this example we will create a function that sums 3 numbers. We want to be able to specify the 3 numbers that are summed, so we will allow our function to accept 3 arguments.

Arguments are a way to pass information to your function, the information will be available during execution. Arguments can be single values like `1` or `'a'`, vectors such as `c(1,2,3)`, or more complex structures such as a `data.frame`.

The arguments that a function accepts are defined inside the body of `function()` itself.

## Arguments - positional and named

When defining a function with `function()`, we place the names of arguments we wish to accept within the parenthesis `( )`.

```r
my_fun <- function(num_1, num_2, num_3) {}
```

We can then refer to each argument by identifier within the function body. To demonstrate this, let's add some code to print each argument when the function is called.

```r
my_fun <- function(num_1, num_2, num_3) {

    print(glue::glue("1st argument is {num_1}"))
    print(glue::glue("2nd argument is {num_2}"))
    print(glue::glue("3rd argument is {num_3}"))

}
```

We have 3 options when calling the function in regards to how we specify the arguments.

### Arguments by reference

The first way is to pass the arguments by reference.

```r
my_fun(num_1 = 10, num_2 = 20, num_3 = 30)

1st argument is 10
2nd argument is 20
3rd argument is 30
```

Note that arguments are assigned with `=` and not `<-`. This is important and many beginners accidentally use `<-` incorrectly here and suffer unexpected side effects.


### Arguments by position

The second approach is to pass the arguments by position.

```r
my_fun(10, 20, 30)

1st argument is 10
2nd argument is 20
3rd argument is 30
```

When we don't specify the name of the argument, R will pass the 1st value to the 1st argument, 2nd value to the 2nd argument and so on. The values themselves have no effect as demonstrated below.

```r
my_fun(30, 20, 10)

1st argument is 30
2nd argument is 20
3rd argument is 10
```

### Arguments by name and position

The third option is to use a mixture of names and positions.

```r 
my_fun(num_1 = 10, 20, num_3 = 30)

1st argument is 10
2nd argument is 20
3rd argument is 30
```

This approach can be useful in some circumstances, particularly in functions with a lot of optional or default arguments (we will cover this later). However, it should be used with caution. For example, misuse can make code difficult to comprehend and reason about.

```r
my_fun(10, 20, num_1 = 30)

1st argument is 30
2nd argument is 10
3rd argument is 20
```

## Function returns

Let's implement our intended behaviour for the function, that it returns the sum of its 3 arguments.

```r
my_fun <- function(num_1, num_2, num_3) {
  return(num_1 + num_2 + num_3)
}

my_fun(10,20,30)
[1] 60
```

You will notice that I chose to wrap the additions inside `return()`. R is something of an outlier amongst programming languages in that it also allows for automatic returns. If the `return()` function isn't included then R returns the value of the last line it evaluated, for example;

```r
my_fun <- function(num_1, num_2, num_3) {
  num_1
  num_2
  num_3 # will be returned
}

my_fun(10,20,30)
[1] 30

my_fun <- function(num_1, num_2, num_3) {
  num_3
  num_2
  num_1 # will be returned
}

my_fun(10,20,30)
[1] 10
```

Whether to rely on automatic returns or include an explicit `return()` is a choice for the individual. [Google's R style guide](https://google.github.io/styleguide/Rguide.html) (which I highly recommend) advises that we *"do not rely on R’s implicit return feature. It is better to be clear about your intent to return() an object."*.

I strongly agree with the Google style guide in this area. To borrow from the zen of python *"explicit is better than implicit"*.

Including the return statement makes your intentions clearer to your future self and collaborators and avoids misinterpretation. I advise to ***always*** include `return()` inside any function that you create. If you create a function solely for it's side effects (for example a function to write an object to disk) you should include `return(invisible())` as the final line.

## Default values

We can expand our function to allow it to provide some extra functionality, in this case we can enable it to return the sum of squares of its arguments instead of the sum of its arguments directly. We will include an extra argument `squares` to which we can pass a logical value to allow us to switch between the 2 behaviours.

When `squares = FALSE` we want our function to return the sum of its arguments, and when `squares = TRUE` it should return the sum of the squares of its arguments.

```r
my_fun <- function(num_1, num_2, num_3, squares) {

  if (squares) {
    return(num_1 + num_2 + num_3)
  } else {
    return(num_1^2 + num_2^2 + num_3^2)
  }

}

my_fun(10,20,30,FALSE)

[1] 60

my_fun(10,20,30,TRUE)

[1] 1400
```

Notice that the function now includes 2 `return()` calls. This can be useful and is an acceptable approach so long as only one call to `return()` can run for any given call to the function. 

When `return()` runs, it effectively ends the function, so any code after the return will not be executed.

```r
example_return <- function() {
  print("this runs")
  return(invisible())
  print("this doesn't run")
}

example_return()

[1] "this runs"
```

### Setting a default value for an argument

The `squares` argument of `my_fun()` is a good candidate for setting a default value. To do this, we assign a value when defining the function.

```r
my_fun <- function(num_1, num_2, num_3, squares = FALSE) {

  if (squares) {
    return(num_1 + num_2 + num_3)
  } else {
    return(num_1^2 + num_2^2 + num_3^2)
  }

}
```

Now, when we call the function, we can omit the `squares` argument entirely, and the function will run with the default `FALSE`.

```r

my_fun <- function(num_1, num_2, num_3, squares = TRUE) {

  if (squares) {
    return(num_1 + num_2 + num_3)
  } else {
    return(num_1^2 + num_2^2 + num_3^2)
  }

}

my_fun(10, 20, 30) # use default value for squares arg

[1] 60

my_fun(10, 20, 30, TRUE) # explicitly pass squares arg as TRUE

[1] 1400

my_fun(10, 20, 30, FALSE)  # explicitly pass squares arg as FALSE

[1] 60
```

## The function environment

In R an environment can be thought of as a container for all of the objects that you have created (including defined functions) and anything that you have imported (for example with `library()`).

When you first start R you will be working in the global environment. If I define an object, `my_value <- 10`, `my_value` is now available in the global environment.

```r
my_value <- 10

print(my_value)

[1] 10

# try to use an object which isn't in the global environment
print(undefined_value)

Error in print(undefined_value) : object 'undefined_value' not found
```

When a function is called it creates it's own environment which persists until it finishes execution and is then destroyed. This is useful as it means that anything we do inside the body of a function can be discarded upon completion, except for any value that we choose to return. The function environment also serves to isolate anything we do inside the function.

As an example, lets create a function that creates 2 objects during execution and returns the sum of both.

```r
set.seed(999) # ensure reproducible results from rnorm

example_fun <- function() {

  temp_1 <- rnorm(1)
  temp_2 <- rnorm(1)
  return(temp_1 + temp_2)

}

example_fun()

[1] -1.5943

print(temp_1)

Error in print(temp_1) : object 'temp_1' not found
```

Here we can see that `temp_1` existed inside the function environment, otherwise we wouldn't have received the result, but when we tried to call `temp_1` from the global environment (outside of the function call) we received an error as it was not available. `temp_1` never existed outside of the function environment.

But, what about the other way around? Can we access an object in the global environment from within a function without passing it as an argument?

```r
example_fun <- function() {

  return(temp_1 + temp_2)

}

temp_1 <- 1
temp_2 <- 2

example_fun()

[1] 3
```

Yes we can! The reason that this works is that during execution when `temp_1` is referenced R looks inside the function environment and fails to find it, but it then proceeds to look inside the parent environment (i.e the environment from where the function was called). In this case the function was called from the global environment and the global environment does contain `temp_1`, so the object can be used from the function environment. 

Environments can be nested multiple times and R will search through all of the nested environments, moving up one level at a time. It will use the first object matching the target name that it encounters.

Typically, I would advise against allowing this behaviour or relying upon it as it hurts the reproducibility of our code and may lead to unexpected errors and bugs. I prefer to always explicitly pass objects into a function that are required using function arguments.


## Next steps

To get to grips with functions try writing a function for each of the following specifications;

1. Write a function that accepts 2 strings as arguments and combines them. You might want to ensure that a space is places between the strings when combined.

    Test the function with the following strings;

    1. "This function"
    2. "combines strings.

    The output should be;

    "This function combines strings."

2. Write a function to calculate a percentage increase between 2 numbers.

    Test the function by calculating the percentage increase between 3,548 and 4,289, which should return 20.89% (rounded to 2 dp).


<details>
  <summary>Answers</summary>
  
  1. You might have written something similar to this;
    
  <code>
    comb_str(str_1, str_2) {
      return(glue::glue("{str_1} {str_2}"))
    }
  </code>
    
  2. You might have written something similar to this;
  
  <code>
    percent_increase <- function(x, y) {
      return(round(((y - x) / x) * 100, 2))
    }
  </code>
</details>
