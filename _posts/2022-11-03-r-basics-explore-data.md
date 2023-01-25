---
layout: article
title : "R Basics - Exploring A Dataset"
toc : true
tags: [R basics, Tutorial]
categories: R Basics
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

R is often used interactively and as users grow in confidence they will often begin to find it simpler and more convenient to explore new data with R directly rather than opening the data in another application. A good IDE such as RStudio can make using R in this way easier. Exploring data before proceeding to perform analysis or further processing allows for making informed decisions regarding how to structure a project and helps to identify the types of techniques which may work well with the data you have.

<!--more-->

## Getting an overview

The built in `iris` dataset was used and made famous by the British statistician and biologist Ronald Fisher in his 1936 paper. 

If we know very little about a dataset, the `head()` and `tail()` functions can be used to print the first or last 6 rows of a `data.frame` to the console by default; though the `n` argument of the functions also allows us to specify a number of rows.

{% highlight r %}
head(iris, n = 5)

#'   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#' 1          5.1         3.5          1.4         0.2  setosa
#' 2          4.9         3.0          1.4         0.2  setosa
#' 3          4.7         3.2          1.3         0.2  setosa
#' 4          4.6         3.1          1.5         0.2  setosa
#' 5          5.0         3.6          1.4         0.2  setosa
{% endhighlight %}

The `typeof()` function is useful for identifying the type of column.

{% highlight r %}
typeof(iris$Sepal.Length)
#' [1] "double"

typeof(iris$Species)
#' [1] "integer"
{% endhighlight %}

The output of `typeof(iris$Species)` may have surprised you. Whilst you may have expected the return `"character"`, we got `"integer"`. The `Species` column is actually a factor class object. We can use `class()` to check an objects class, and `is.factor()` if we wish to check for a factor specifically.

{% highlight r %}
class(iris$Sepal.Length)
#' [1] "numeric"

class(iris$Species)
#' [1] "factor"

is.factor(iris$Species)
#' [1] TRUE
{% endhighlight %}

***A note on factors:*** R was primarily designed with statistical applications in mind and the existence of the factor class is a direct result of this. R factors are intended for use with categorical variables, eye colour for example. Whilst factors can appear to be strings they also have a numerical representation, which is why `typeof(iris$Species)` returns `integer`. 

Factors can be useful when you want to apply an order to categorical vector. For example, I could ask some people to rate this explanation of factors as "good", "bad", or "average" and record the responses as a character vector.

{% highlight r %}
rating <- c("good", "bad", "average", "bad", "average")
{% endhighlight %}

There is no inherent order to the vector from R's perspective, so if we apply the `sort()` function it will sort the elements alphabetically.

{% highlight r %}
sort(rating)
#' [1] "average" "bad"     "good"   
{% endhighlight %}

However, we can use the factor class in R to add a custom order to the vector. To do so we use the `factor` function and specify our preferred order with the `levels` argument.

{% highlight r %}
rating <- factor(
    c("good", "bad", "average", "bad", "average"),
    levels = c("bad", "average", "good")
)

sort(rating)
#' [1] bad     bad     average average good   
#' Levels: bad average good
{% endhighlight %}

<p>
<strong><i>Caution</i></strong>: Factors can be useful, but they can also lead to errors occurring if you haven't realised that they are being used. This common cause of frustration was addressed by the R Core team and as of R version 4.0.0 many functions which used to set strings as factors by default ceased to do so, including the <code>data.frame()</code> function.
<br><br>
Those using a version of R prior to the 4.0.0 release should take care to pass the <code>stringsAsFactors = FALSE</code> argument inside relevant functions if factors are not desired. Alternatively, the behavior can be turned off globally during a session by running <code>options(stringsAsFactors = FALSE)</code>.
</p>
{:.warning}

## Taking a closer look

Once you have an idea of the general structure and contents of a dataset it can be useful to learn more about specific columns. The `summary()` function is particularly useful for taking a closer look at a dataset and can handle columns of different types.

{% highlight r %}
summary(iris)
#'   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
#'  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
#'  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
#'  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
#'  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
#'  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
#'  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
#'        Species  
#'  setosa    :50  
#'  versicolor:50  
#'  virginica :50 
{% endhighlight %}

`fivenum()` is another useful function which quickly provides Tukey's five number summary (minimum, lower-hinge, median, upper-hinge, maximum) for a numeric vector.

{% highlight r %}
fivenum(iris$Petal.Width)
#' [1] 0.1 0.3 1.3 1.8 2.5
{% endhighlight %}

Other helpful functions to try include `min()`, `max()`, `range()`, `mean()`, and `median()`.

{% highlight r %}
min(iris$Petal.Width)
#' [1] 0.1

max(iris$Petal.Width)
#' [1] 2.5

range(iris$Petal.Width)
#' [1] 0.1 2.5

mean(iris$Petal.Width)
#' [1] 1.199333

median(iris$Petal.Width)
#' [1] 1.3
{% endhighlight %}

## Next Steps

You can view a full list of the built in R datasets by running `library(help="datasets")`. Try running some of the functions discussed above on different datasets.
