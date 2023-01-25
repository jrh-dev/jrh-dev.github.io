---
layout: article
title : "R Basics - Factors"
toc : true
tags: [R basics, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

In statistics we often talk about categorical variables. These are variables with a typically limited and potentially fixed number of possible values. An observation of a categorical variable is based on a qualitative property.

<!--more-->

Some common examples of categorical variables are:

* Eye colour ("blue", "brown", "green", etc).
* Rating a service ("good", "ok", "bad").
* Blood group ("A", "AB", "O", etc).
* A numeric value, but where options are limited, for example the result of a dice being rolled (1 to 6).

R was primarily designed with statistical applications in mind and the existence of the factor class is a direct result of this. R factors are intended for use with categorical variables. 

## Using factors in R

### Creating factors

The primary function we will need to work with factors in R is **`factor()`**.

Let's say we asked 5 customers to rate their experience of a service and stored their answers in a character vector named `satisfaction`. The customers could choose one of 5 values to rate their experience;

* very good
* good
* ok
* bad
* very bad

{% highlight r %}

satisfaction <- c("good", "bad", "very_good", "ok", "ok")

{% endhighlight %}

We can convert `satisfaction` to a factor with the `factor()` function. To test whether a vector is a factor we use `is.factor()`.

{% highlight r %}

satisfaction <- factor(satisfaction)

is.factor(satisfaction)
#' [1] TRUE

{% endhighlight %}

Of course, we don't need to create the character vector first and then convert to a factor, we can also create it directly.

{% highlight r %}

satisfaction <- factor(c("good", "bad", "very_good", "ok", "ok"))

{% endhighlight %}

<p>
<strong><i>Information:</i></strong> If we use <code>typeof()</code> to check the type of our vector, we can see that it is an integer (surprise!).
<br><br>
<code>
typeof(satisfaction)
#' [1] "integer"
</code>
<br><br>
Whilst factors may appear to be strings based upon the elements that they contain, they also have a numerical representation, which is why `typeof(satisfaction)` returns `integer`. This is important as it can also lead to errors occurring if you haven't realised that factors are being used. This was a common cause of frustration for R users which was addressed by the R Core team and as of R version 4.0.0 many functions which used to set strings as factors by default ceased to do so, including the `data.frame()` function.
<br><br>
Those using a version of R prior to the 4.0.0 release should take care to pass the `stringsAsFactors = FALSE` argument inside relevant functions if factors are not desired. Alternatively, the behaviour can be turned off globally during a session by running `options(stringsAsFactors = FALSE)`.
</p>
{:.info}

### Levels

If we use the `print()` function, both the elements of our vector and the levels that have been automatically detected are printed to the console. We can also use the `levels()` function to return the levels specifically. Levels are the unique elements that the factor contains, or might contain.

{% highlight r %}

print(satisfaction)
#' [1] good      bad       very_good ok        ok    
#'
#' Levels: bad good ok very_good

levels(satisfaction)

#' [1] "bad"       "good"      "ok"        "very_good"

{% endhighlight %}

For simpler categorical variables the levels which have been detected by default may be sufficient. However, as we haven't specified an order for our factor, if we try to sort it the result won't be very meaningful.

{% highlight r %}

sort(satisfaction)
#' [1] bad       good      ok        ok        very_good
#' Levels: bad good ok very_good

{% endhighlight %}

R is not able to identify an order for our variable automatically, so `sort()` has simply returned the elements of satisfaction on alphabetical order and functions like `min()` and `max()` will return errors if we try them.

{% highlight r %}

min(satisfaction)
#' Error in Summary.factor(c(2L, 1L, 4L, 3L, 3L), na.rm = FALSE) : 
#'   ‘min’ not meaningful for factors

max(satisfaction)
#' Error in Summary.factor(c(2L, 1L, 4L, 3L, 3L), na.rm = FALSE) : 
#'   ‘max’ not meaningful for factors

{% endhighlight %}

This behaviour is likely preferable when dealing with a categorical variable with no implied order, for example hair colour, but for our customer satisfaction ratings we want our ratings to be ordered from worse to best. Lets specify an order of;

`very_bad < bad < ok < good < very_good`

We can use the `factor()` function and specify our preferred order by passing a vector to the `levels` argument and setting the `ordered` argument to `TRUE`. You will notice that we are able to specify `levels` that don't actually exist in our data, 'very_bad' for example. None of our 5 respondents actually rated the service as being 'very bad', but it was a valid option and therefore can be included as a level.

{% highlight r %}

satisfaction <- factor(satisfaction, levels = c("very_bad", "bad", "ok", "good", "very_good"), ordered = TRUE)

print(satisfaction)
#' [1] good      bad       very_good ok        ok       
#' Levels: very_bad < bad < ok < good < very_good

{% endhighlight %}

We can now sort `satisfaction`.

{% highlight r %}

sort(satisfaction)
#' [1] bad       ok        ok        good      very_good
#' Levels: very_bad < bad < ok < good < very_good

{% endhighlight %}

We can also use functions like `min()` and `max()` with our vector too.

{% highlight r %}

min(satisfaction)
#' [1] bad
#' Levels: very_bad < bad < ok < good < very_good

max(satisfaction)
#' [1] very_good
#' Levels: very_bad < bad < ok < good < very_good

{% endhighlight %}

### Reverse a factor

The `forcats` package contains the function `fct_rev()` which deserves an honourable mention here, it allows us to quickly reverse the order of our factor.

{% highlight r %}

forcats::fct_rev(satisfaction)
[1] good      bad       very_good ok        ok       
Levels: very_good < good < ok < bad < very_bad

{% endhighlight %}

`fct_reverse` can be particularly useful when using factors to order the axes of plots.

## Conversions

Converting our factor, `satisfaction`, to a character vector is straight forward using the `as.character()` function. Note that converting a factor removes the levels.

{% highlight r %}

as.character(satisfaction)
#' [1] "good"      "bad"       "very_good" "ok"        "ok"  

{% endhighlight %}

Sometimes you may have a factor where the elements represent something numeric by nature, but that could still be considered a categorical variable, for example rolls of a dice. Let's create a factor with 5 observations of dice rolls.

{% highlight r %}

rolls <- factor(c(1,6,2,3,4), levels = c(1,2,3,4,5,6))

print(rolls)
#' [1] 1 6 2 3 4
#' Levels: 1 2 3 4 5 6

{% endhighlight %}

We can convert `rolls` to a numeric vector.

{% highlight r %}

as.numeric(rolls)
#' [1] 1 6 2 3 4

{% endhighlight %}

However, care must be taken when using `as.numeric()` with a factor. In the example above, the result appears correct, but this is only because our levels matched up to our vector elements. Let's try the same thing, but without specifying the levels explicitly.

{% highlight r %}

rolls <- factor(c(1,6,2,3,4))

print(rolls)
#' [1] 1 6 2 3 4
#' Levels: 1 2 3 4 6

as.numeric(rolls)
#' [1] 1 5 2 3 4

{% endhighlight %}

Rather than returning the elements of `rolls`, converted to numeric type, we have instead received the index position of each element's level. This is a simple issue to resolve, one can simply convert to a character then numeric `as.numeric(as.character(rolls))`, but represents another reason why caution should be taken when working with factors.


## Next steps

Factors are a unique and useful structure which are highly effective when working with categorical variables. However, there are some more complicated aspects to their usage. Try converting some simple vectors to factors and ordering them, before transforming to another type.

