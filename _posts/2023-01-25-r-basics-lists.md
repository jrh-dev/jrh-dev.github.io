---
layout: article
title : "R Basics - Lists"
toc : true
tags: [R basics, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

Lists are a little different to other data structures you typically encounter in R. They can contain elements of different types, including other lists, and offer a large amount of flexibility. 

<!--more-->

Lists can be created with `list()` and may be returned by some functions such as `lapply()`.

## Creating a list

The `list()` function is the most common way to create a list object.

{% highlight r %}

> my_list <- list("string", 1:5, list("string", 1:5), c(TRUE, FALSE))

> print(my_list)
#' [[1]]
#' [1] "string"
#' 
#' [[2]]
#' [1] 1 2 3 4 5
#' 
#' [[3]]
#' [[3]][[1]]
#' [1] "string"
#' 
#' [[3]][[2]]
#' [1] 1 2 3 4 5
#' 
#' 
#' [[4]]
#' [1]  TRUE FALSE

{% endhighlight r %}

## Accessing list elements

Our list object `my_list` contains a character vector, a numeric vector, a list, and a logical vector.

We can access elements within our list using double square brackets `[[ ]]`. To access the first element of `my_list` we would use `[[1]]`. As with all indexing in R, the index for list objects starts at 1.

{% highlight r %}

> print(my_list[[1]])
#' [1] "string"

{% endhighlight r %}

The 2nd element of `my_list` is a numeric vector, we can interact with it like any other vector by combining the list subsetting with a vector subset. We could use this approach to return the 2nd and 3rd element of the vector.

{% highlight r %}

> print(my_list[[2]][2:3])
#' [1] 2 3

{% endhighlight r %}

We can also perform actions over list elements.

{% highlight r %}

# sum the entire vector
> print(sum(my_list[[2]]))
#' [1] 15

# sum a subset of the vector
> print(sum(my_list[[2]][2:3]))
#' [1] 5

{% endhighlight r %}

The 3rd element of `my_list` is a list itself, which we can refer to as a nested list. To access an element of the nested list we need to utilise the `[[ ]]` syntax twice.

{% highlight r %}

# access the 3rd element of my_list
> print(my_list[[3]])
#' [[1]]
#' [1] "string"
#' 
#' [[2]]
#' [1] 1 2 3 4 5

# access the 1st element of the nested list
> print(my_list[[3]][[1]])
#' [1] "string"

# subset the 2nd element of the nested list
>  print(my_list[[3]][[2]][2:3])
#' [1] 2 3

{% endhighlight r %}


## Named lists

We can also create named lists. Notice that we don't wrap the list element names in quotations (`" "`).

{% highlight r %}

> my_list <- list(
    string_vector = "string",
    numeric_vector = 1:5,
    list_object = list("string", 1:5), 
    logical_vector = c(TRUE, FALSE)
    )

> print(my_list)
#' $string_vector
#' [1] "string"
#' 
#' $numeric_vector
#' [1] 1 2 3 4 5
#' 
#' $list_object
#' $list_object[[1]]
#' [1] "string"
#' 
#' $list_object[[2]]
#' [1] 1 2 3 4 5
#' 
#' 
#' $logical_vector
#' [1]  TRUE FALSE

{% endhighlight r %}

We can now access the elements of `my_list` using the name and `$` operator, though the index system still works.

{% highlight r %}

> print(my_list$string_vector)
#' [1] "string"

> identical(my_list$string_vector, my_list[[1]])
#' [1] TRUE

{% endhighlight r %}

We can also apply names to an existing list with the `names()` function.

{% highlight r %}

> my_list <- list("string", 1:5, list("string", 1:5), c(TRUE, FALSE))

> names(my_list) = c("string_vector", "numeric_vector", "list_object", "logical_vector")

> print(my_list)
#' $string_vector
#' [1] "string"
#' 
#' $numeric_vector
#' [1] 1 2 3 4 5
#' 
#' $list_object
#' $list_object[[1]]
#' [1] "string"
#' 
#' $list_object[[2]]
#' [1] 1 2 3 4 5
#' 
#' $logical_vector
#' [1]  TRUE FALSE

{% endhighlight r %}

## Appending to a list

### append()
We can add to a list using `append()`. Lets append a `data.frame` object to `my_list`.

{% highlight r %}

> print(append(my_list, list(df_object = data.frame(a = 1:5, b = 6:10)))
#' $string_vector
#' [1] "string"
#' 
#' $numeric_vector
#' [1] 1 2 3 4 5
#' 
#' $list_object
#' $list_object[[1]]
#' [1] "string"
#' 
#' $list_object[[2]]
#' [1] 1 2 3 4 5
#' 
#' 
#' $logical_vector
#' [1]  TRUE FALSE
#' 
#' $df_object
#'   a  b
#' 1 1  6
#' 2 2  7
#' 3 3  8
#' 4 4  9
#' 5 5 10

{% endhighlight r %}

<p>
<strong><i>Information:</i></strong> If you are coming to R from another language such as python, you need to be aware that <code>append()</code> does not take care of assignment and doesn't modify <code>my_list</code> in place, so you do need to assign the output of <code>append()</code> to store it (i.e. <code>my_list <- append(...</code>).
<br><br>
You should also be aware that with an object such as a <code>data.frame</code>, you need to ensure it is within a list itself when being appended to an existing list. If we omit this step then the individual columns will be appended rather than the <code>data.frame</code> as a whole. This is because beneath the surface, <code>data.frames</code> are actually list objects.
</p>
{:.info}

{% highlight r %}

> print(append(my_list, data.frame(a = 1:5, b = 6:10))
#'$string_vector
#'[1] "string"
#'
#'$numeric_vector
#'[1] 1 2 3 4 5
#'
#'$list_object
#'$list_object[[1]]
#'[1] "string"
#'
#'$list_object[[2]]
#'[1] 1 2 3 4 5
#'
#'
#'$logical_vector
#'[1]  TRUE FALSE
#'
#'$a
#'[1] 1 2 3 4 5
#'
#'$b
#'[1]  6  7  8  9 10

{% endhighlight r %}

### c()

We can also append to a list with `c()`.

{% highlight r %}

>  my_list <- c(my_list, list(df_object = data.frame(a = 1:5, b = 6:10)))

> print(my_list)
#' $string_vector
#' [1] "string"
#' 
#' $numeric_vector
#' [1] 1 2 3 4 5
#' 
#' $list_object
#' $list_object[[1]]
#' [1] "string"
#' 
#' $list_object[[2]]
#' [1] 1 2 3 4 5
#' 
#' 
#' $logical_vector
#' [1]  TRUE FALSE
#' 
#' $df_object
#'   a  b
#' 1 1  6
#' 2 2  7
#' 3 3  8
#' 4 4  9
#' 5 5 10

{% endhighlight r %}

## Removing list elements

Removing list elements can be achieved with the `NULL` keyword. Lets remove the `data.frame` object we added earlier as the 5th element of `my_list`.

{% highlight r %}

> my_list[[5]] <- NULL

> print(my_list)
$string_vector
[1] "string"

$numeric_vector
[1] 1 2 3 4 5

$list_object
$list_object[[1]]
[1] "string"

$list_object[[2]]
[1] 1 2 3 4 5


$logical_vector
[1]  TRUE FALSE

{% endhighlight r %}

We're not limited to removing just elements from the end of the list. We can use the same technique to remove the 2nd element of `my_list`.

{% highlight r %}

> my_list[[2]] <- NULL

> print(my_list)
$string_vector
[1] "string"

$list_object
$list_object[[1]]
[1] "string"

$list_object[[2]]
[1] 1 2 3 4 5


$logical_vector
[1]  TRUE FALSE

{% endhighlight r %}

## Iterating over lists

There are functions, such as `lapply()` that exist to iterate over lists which can help to provide optimised performance, though these will be covered elsewhere.

Iteration over a list with a loop works in much the same way as with other R objects.

{% highlight r %}

> for (element in my_list) {
      print(element)
  }

#'[1] "string"
#'
#'[[1]]
#'[1] "string"
#'
#'[[2]]
#'[1] 1 2 3 4 5
#'
#'[1]  TRUE FALSE

{% endhighlight r %}


## Next steps

Lists are an important data structure within R and are particularly useful for solving some more advanced programming problems as well as for optimisation.

Their versatility makes them perfect for use in a variety of situations.

Try creating some lists consisting of different R objects and accessing the objects.
