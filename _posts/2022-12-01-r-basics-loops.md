---
layout: article
title : "R Basics - Loops"
toc : true
tags: [R basics, Tutorial]
author: <author_id>
show_edit_on_github: false
---

Loops are a fundamental element of computer programming. They facilitate iteration by running a piece of code repeatedly and provide a flexible approach to solving many problems that you may encounter.


Loops have gained something of a bad reputation amongst the R community, largely owing to the fact that poor implementation of loops in R can result in code that is slow and inefficient. However, with proper use loops can be a powerful and effective tool for the R programmer.

## for loops

Lets start with a simple example, a loop to print "a", "b" and "c" to the console.

{% highlight r %}

for (x in c("a", "b", "c")) {
    
    print(x)

}

#' [1] "a"
#' [1] "b"
#' [1] "c"

{% endhighlight %}

To understand what's happening with our loop we can break it down into the component parts.

### The header

`for (x in c("a", "b", "c"))`

The header of our for loop is used to define our loop variable, what is going to change during each iteration. During each iteration `x` will take on a different value from `c("a", "b", "c")`. In effect, executing the for loop assigns a temporary value to `x` for each iteration.

**1st iteration**
`x = "a"`

**2nd iteration**
`x = "b"`

**3rd iteration**
`x = "c"`

### The body

`print(x)`

The body of our for loop is used to define the action or actions to be performed during each iteration. The body will always use the current value of `x`, which changes with each iteration. Therefore, our for loop actually produces 3 different outputs.

**1st iteration**
`[1] "a"`

**2nd iteration**
`[1] "b"`

**3rd iteration**
`[1] "c"`

### Constructing the header

The syntax used in the header of a for loop is a little different from typical R syntax in terms of the way in which it is structured and this can cause confusion. The simplest way to think about writing the loop is to consider the left hand side (LHS) and right hand side (RHS) of `in`. The value that you place on the LHS of `in` will change with each iteration to the next element of the vector passed to the RHS of `in`.

There are no rules as to what the LHS value should be named. All the below examples would produce the same result.

{% highlight r %}

for (x in c("a", "b", "c")) {
    
    print(x)

}
#' [1] "a"
#' [1] "b"
#' [1] "c"

for (letter in c("a", "b", "c")) {
    
    print(letter)

}
#' [1] "a"
#' [1] "b"
#' [1] "c"

for (xyzw in c("a", "b", "c")) {
    
    print(xyzw)

}
#' [1] "a"
#' [1] "b"
#' [1] "c"

{% endhighlight %}

The RHS can realistically be any R object that can be iterated over. The following code examples produce the same output.

{% highlight r %}

for (x in c("a", "b", "c")) {
    
    print(x)

}
#' [1] "a"
#' [1] "b"
#' [1] "c"

a_vector <- c("a", "b", "c")

for (x in a_vector) {
    
    print(x)

}
#' [1] "a"
#' [1] "b"
#' [1] "c"

{% endhighlight %}

### Constructing the body

The body will contain more traditional R code, but should (almost always) be wrapped in curly braces (`{ }`). If we try to write a simple loop without enclosing the body with `{ }`, things may not work as intended. Let's look at a loop with 2 print statements written with and without the curly braces.

{% highlight r %}

// with enclosed body
for (x in c("a", "b", "c")) {
    
    print(x)
    print(x)

}
#' [1] "a"
#' [1] "a"
#' [1] "b"
#' [1] "b"
#' [1] "c"
#' [1] "c"

// without enclosed body
for (x in c("a", "b", "c")) 
    
    print(x)
    print(x)

#' [1] "a"
#' [1] "b"
#' [1] "c"
#' [1] "c"

{% endhighlight %}

In the example with the enclosed body R has executed all of the code contained within the curly braces for each operation, thus our letters have been printed twice. In the example without an enclosed body, R has only included the next line following the header in the loop iterations, our second print statement has therefore only ran once, after the loop has completed. The extra "c" that was printed came from the final value assigned to `x`.

<p>
<strong><i>Information:</i></strong> It is important to note that unlike function calls, loops do not execute within an enclosed environment. This means that any temporary assignments made in the body will exist in your workspace once the loop completes. We can see this by running <code>print(x)</code> in isolation.
<br><br>
<code>

print(x)
#' [1] "c"

</code>
<br><br>
<code>x</code> now exists in our global environment and its value is as per the last element of the vector we iterated over in our loop (<code>c("a", "b", "c")</code>). 
</p>
{:.info}

## Storing an output

Often we will want to use a loop to apply the same manipulation to multiple elements of a vector. Let's start with an example of a loop that adds 1 to another number and prints the result to the console.

{% highlight r %}

numbers <- 1:5

for (n in numbers) {
    
    print(n + 1)

}
#' [1] 2
#' [1] 3
#' [1] 4
#' [1] 5
#' [1] 6

{% endhighlight %}

If we want to store the outputs as a vector rather than print them to the console, however, we can't create a new object within the loop itself to capture all of the iterations. If we do, the output might not be what you initially expect.

{% highlight r %}

numbers <- 1:5

for (n in numbers) {
    
    output <- (n + 1)

}

print(output)
#' [1] 6

{% endhighlight %}

In the above example each iteration overwrites the previous value of `output`, so we only capture the value from the final loop iteration. We actually need to create the `output` vector before we execute the loop. Let's create an empty vector of the correct type named `output` with the `vector()` function. We can then use `c()` inside the body to append the output of each iteration to `output`.

{% highlight r %}

numbers <- 1:5

output <- vector(mode = "numeric")

for (n in numbers) {
    
    output <- c(output, (n + 1))

}

print(output)
#' [1] 2 3 4 5 6

{% endhighlight %}

It worked! We managed to store all of our outputs in the `output` vector. However, just because we can do something doesn't mean that we should. Its important to understand that this method can be used and it is typically the solution most people will come up with first, but in practice **NEVER DO THIS**. 

Instead lets look at pre-allocation.

## Pre-allocation

Whilst the example above achieves our desired outcome it is inefficient and should be avoided. When used with toy data to provide a simple example the approach won't produce any noticeable performance issues, however, if we scaled this to a larger number of iterations or increased the complexity we would have slow and memory hungry code. This issue is a key driver of negativity within the R community around the use of loops.

To understand why this approach doesn't work well, we need to look at R's copy on modify behaviours. Every R object exists in memory (the computers RAM specifically), the object has an address so that R knows where to find it, and it has an amount of space allocated to it. The space an object is given is always precisely the amount it requires in its current form.

When we add more elements to a vector using `c()` the new object requires more space than has been assigned to the original one; `c(1,2,3,4)` won't fit in the same space as `c(1,2,3,4,5)`. R therefore has to move the object to a new space each time that the vector increases in length and moving is time consuming as it involves making a copy of the original object. There is also a compounding effect in that the bigger the object, the more time it takes to move, so each iteration takes longer.

Thankfully, there is a simple solution to the problem in the form of pre-allocation.

Pre-allocation allows us to create an object to store our output up front, ensuring that it has enough memory assigned to it from the beginning, and then populating it. Whilst it may sound quite technical the implementation is rather simple. 

Firstly, we need to identify the size that we require `output` to be. We know that our loop will produce one value during each iteration, and the number of iterations will be equal to the length of `numbers`. Let's start by capturing the length of `numbers`.

{% highlight r %}

numbers <- 1:5

len_numbers <- length(numbers)

print(len_numbers)
#' [1] 5

{% endhighlight %}

We can now create our vector, `output`, ensuring that it is sufficient in size to capture all of out loop outputs. We need to ensure that our vector is of the correct type using the `mode` argument of the `vector()` function. In this case we are working with numeric values so we specify `mode = "numeric"`. We can pass the `len_numbers` object that we created earlier to the `length` argument.

{% highlight r %}

output <- vector(mode = "numeric", length = len_numbers)

print(output)
#' [1] 0 0 0 0 0

{% endhighlight %}

We now need to rewrite our loop head a little to make use of pre-allocation. Our original `for (n in numbers)` allows us to work on the elements of `numbers` directly, however, we are now going to want to access the elements of numbers with the index system which will allow us to assign the output of each iteration to our `output` vector. We can use the `seq_len()` function for this.

{% highlight r %}

for (n in seq_len(len_numbers)) {
    
    output[n] <- numbers[n] + 1

}

print(output)
#' [1] 2 3 4 5 6

{% endhighlight %}

`seq_len(len_numbers)` returns a vector of the numbers 1 to `len_numbers` (5).

{% highlight r %}

seq_len(len_numbers)
'# [1] 1 2 3 4 5

{% endhighlight %}

So `n`s value during the 1st iteration of the loop will be 1, then 2 during the second iteration and so on. We use the value of `n` along with R's indexing system to access the individual elements of `numbers` during each iteration and assign the output to the corresponding element of `output`.

For example;

In our 1st iteration;

1. `n` equals 1

2. element 1 of `numbers` (`numbers[1]`) has 1 added to it (1 + 1 = 2)

3. the value is assigned to element 1 of `output` (`output[1]`)

In our 2nd iteration;

1. `n` equals 2

2. element 2 of `numbers` (`numbers[2]`) has 2 added to it (2 + 1 = 3)

3. the value is assigned to element 2 of `output` (output[2])

### Benchmarking pre-allocation

To demonstrate the difference between growing a vector and utilising pre-allocation we can use a benchmarking tool. I recommend the `microbenchmark` package.

Lets compare 2 approaches to adding 1 to a vector of length 10,000, one using pre-allocation, and one which grows the output with every iteration.

{% highlight r %}

microbenchmark::microbenchmark(

    no_pre_alloc = {
        output = vector(mode="numeric")
        for (i in 1:10000) output <- c(output, i + 1)
    },

    pre_alloc = {
        output = vector(mode="numeric", length = 1000)
        for (i in 1:10000) output[i] <- i + 1
    },
    
    times = 10
)

Unit: milliseconds
         expr         mean
 no_pre_alloc   137.423091
    pre_alloc     3.563711

{% endhighlight %}

The pre-allocation method is 38 times quicker for this example, and that ratio increases further the larger the data involved.

## while loops

Where a for loop typically performs an iteration for each element of a predetermined input, a while loop keeps executing until a condition is met.

Let's write a loop that prints numbers, starting at 1, increases the number by 1 in each iteration, and stops at 10. 

{% highlight r %}

it = 1

while (it <= 10) {

    print(it)

    it <- it  + 1

}

#' [1] 1
#' [1] 2
#' [1] 3
#' [1] 4
#' [1] 5
#' [1] 6
#' [1] 7
#' [1] 8
#' [1] 9
#' [1] 10

{% endhighlight %}

Our loop first prints the current value of `it` to the console, it then adds 1 to `it` and reassigns the new value. The test `it <= 10` is performed before the code block runs on each iteration.  Once the while condition evaluates to `TRUE`, when `it` is greater than or equal to 10, the loop ends.


## break

We can use the `break` key word inside a loop to cause it to stop executing if a condition is met. Lets write a for loop to find the factors of 42, but (rather inefficiently) we are going to check whether numbers 1 through to 100 are factors of 42. We can include an `if` statement to ensure only factors of 42 are printed to the console.

{% highlight r %}

for (ii in 1:100) {

    if (42 %% ii == 0) {
        print (ii)
    }

}

#' [1] 1
#' [1] 2
#' [1] 3
#' [1] 6
#' [1] 7
#' [1] 14
#' [1] 21
#' [1] 42

{% endhighlight %}

It worked! But our loop executed 100 times. We can check this by looking at the value of `ii`.

{% highlight r %}

print(ii)
#' 100

{% endhighlight %}

 We know we won't find any factors once we pass the value of 42 itself, so lets add a break condition. We will use an `if` statement to check whether `ii` is equal to 42 during each iteration and if it is we will break the loop. To use `break` we simply include the key word, in this example, inside the body of the `if` statement.

{% highlight r %}

for (ii in 1:100) {

    if (42 %% ii == 0) {
        print (ii)
    }

    if (ii == 42) {
        break
    }
}

#' [1] 1
#' [1] 2
#' [1] 3
#' [1] 6
#' [1] 7
#' [1] 14
#' [1] 21
#' [1] 42

{% endhighlight %}

The output is unchanged, but our loop 'broke' once `ii` was equal to 42 and no more iterations were performed. Again, we can check this by looking at the value of `ii` once the the loop has completed.

{% highlight r %}

print(ii)
#' 42

{% endhighlight %}

## next

The `next` key word causes the loop to skip the remainder of a single iteration, but unlike `break` it continues to perform the rest of the remaining iterations. To demonstrate the functionality lets print the numbers 1 to 10, but skip any numbers that are multiples of 3.

{% highlight r %}

for (ii in 1:10) {

    if (ii %% 3 == 0) {
        next
    }

    print(ii)
}

#' [1] 1
#' [1] 2
#' [1] 4
#' [1] 5
#' [1] 7
#' [1] 8
#' [1] 10

{% endhighlight %}

Whenever `ii` is divisible by 3 the condition within the `if` statement is triggered and the `next` command causes the current iteration to end and the next one to begin. When `next` is triggered the `print()` function is never executed within that iteration.

## Using loops - examples

### In the absence of vectorisation

The examples above demonstrate how loops function, but don't represent very efficient real world uses. In R many functions are 'vectorised' which means that operations are performed in parallel. Loops offer a solution for when vectorisation isn't available. Lets explore this with some simple vectors, `x`, `y`, and `z`.

{% highlight r %}

x <- 1:5

y <- 6:10

z <- 11

{% endhighlight %}

Vectorisation is the reason that `x + y` gives us `7  9 11 13 15`.

In essence `x + y` is actually returning;

{% highlight r %}

x[1] + y[1]
x[2] + y[2]
x[3] + y[3]
x[4] + y[4]
x[5] + y[5]

{% endhighlight %}

`z + y` is a little different, the value of `z` is recycled and used against each element of `y`, so we actually get the return of;

{% highlight r %}

z[1] + y[1]
z[1] + y[2]
z[1] + y[3]
z[1] + y[4]
z[1] + y[5]

{% endhighlight %}

Vectorisation is also the reason why vectorised functions like `sqrt()` act on each element of a vector.

{% highlight r %}

sqrt(10:15)
#' 3.162278 3.316625 3.464102 3.605551 3.741657 3.872983

{% endhighlight %}

However, when a function isn't vectorised we have to proceed a little differently. The digest package provides the function `digest()` which can be used to generate hash digests of R objects. Given its intended usage it is not vectorised. Lets run `digest()` over a vector of names.

{% highlight r %}

digest::digest(c("Alan", "Harry", "Frances", "Polly", "Walt"))
[1] "463e094dab0323c06acf39d4c0a9d62c"

{% endhighlight %}

`digest()` has taken the entire vector and returned a hash for it (as intended). If we want to get a hash for each element of the vector then we will have to try something else, like a loop.

Using a simple loop we can hash each element of the vector.

{% highlight r %}

for(name in c("Alan", "Harry", "Frances", "Polly", "Walt")) {
    print(digest::digest(name))
}

#' [1] "59a25be57d59ee4534e4d3d32baebca7"
#' [1] "dd82d09e51606fe5006f38acdb19e9cb"
#' [1] "407f522c0d10c7f1692fd14aaa2d951a"
#' [1] "7597bee67ba5e2cdc44896451977e786"
#' [1] "1caacbe3b8acd3454b3c6a9ae79aa1f6"

{% endhighlight %}

### To perform the same operation on multiple columns

We can use loops to perform the same operation multiple times on a different input. Consider the following data frame.

{% highlight r %}

df <- data.frame(
    col_a = 1:5,
    col_b = 6:10,
    col_c = 11:15,
    col_d = 16:20,
    col_e = 21:25,
    col_f = 26:30
)

print(df)
#'   col_a col_b col_c col_d col_e col_f
#' 1     1     6    11    16    21    26
#' 2     2     7    12    17    22    27
#' 3     3     8    13    18    23    28
#' 4     4     9    14    19    24    29
#' 5     5    10    15    20    25    30

# a quick way to check the types of a data frame's colums
sapply(df, typeof) 
#'     col_a     col_b     col_c     col_d     col_e     col_f 
#' "integer" "integer" "integer" "integer" "integer" "integer" 

{% endhighlight %}

If we want to specify the type of `col_a`, `col_c`, and `col_e` as `double` we could do something like this;

{% highlight r %}

df$col_a <- as.double(df$col_a)
df$col_c <- as.double(df$col_c)
df$col_e <- as.double(df$col_e)

{% endhighlight %}

Or in the tidyverse;

{% highlight r %}

df <- df %>%
    dplyr::mutate(
       col_a <- as.double(col_a),
       col_c <- as.double(col_c),
       col_e <- as.double(col_e) 
    )

{% endhighlight %}

However, with 3 columns to transform the code is already cumbersome, in a larger data set we could easily find the approach becoming difficult to maintain. The tidyverse does offer a solution to reduce the amount of code required here but it involves learning additional functions and adds complexity.

Loops provide an alternative approach here that is well suited to the task at hand, compact, and simple to understand.

{% highlight r %}

for (col in c("col_a", "col_c", "col_e")) {  
    df[, col] <- as.double(df[, col] )
}

sapply(df, typeof) # check types
#'     col_a     col_b     col_c     col_d     col_e     col_f 
#'  "double" "integer"  "double" "integer"  "double" "integer" 

{% endhighlight %}

## Next steps

Loops provide a powerful and flexible way to perform iteration. Correct usage can help to reduce the amount of code you need to write to achieve a desired outcome. Try the tasks below to put your knowledge into practice.

1. Write a for loop to calculate the mean value of all columns in the built in `mtcars` dataset. Store the means in a vector named `mtcars_means`. What is the sum of `mtcars_means`?

2. Create 2 objects, `x` and `y`, and assign the value of `1` to each. Write a while loop that;

    * doubles the value of `x` in each iteration.
    * adds 1 to the value of `y` in each iteration.
    * ends when `y` is equal to 20

    What is the value of `x` when the loop ends?

3. Write a loop to print all the numbers between 1 and 100 (inclusive) which are divisible by 33. Try to include the `next` keyword. Which numbers printed to the console?

<details>
  <summary>Answers</summary>
  
  1. 435.6938 <br>

  2. 524,288 <br>

  3. 33, 66, and 99 <br>
    
</details>