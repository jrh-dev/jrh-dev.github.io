---
layout: article
title : "Refactoring - Writing readable code"
toc : true
tags: [R, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

It's easy to end up with a block of code that is overly verbose. We've all been there, an initial requirement needed two lines of code to handle some simple logic; a little later another line was added to cover an edge case, and before you know it you have 20 lines of copy pasta. 

Finding the time to refactor these blocks of code is nearly always worthwhile and your future self will thank you for it in the long run. <!--more-->

Lets consider the following example data, which mimics a real world dataset I recently encountered.

{% highlight r %}
my_data = data.frame(
  code = glue(
    "{sample(LETTERS, 10000, TRUE)}",
    "{sample(1000:9999, 10000, TRUE)}")
)

print(head(my_data))
#                       code
# 1                     N8862
# 2                     L7316
# 3                     Q8573
# 4                     R4236
# 5                     K1843
# 6                     U7067

{% endhighlight %}

Some code was written to assign a value (by row) based on the first 3 characters of the value in the `code` column.

The code, written in the `tidyverse` style, looked something like this;

{% highlight r %}

my_data %<>%
  mutate(mapped_value = case_when(
    (substr(code,1,1) == "A" & between(as.numeric(substr(code,2,3)), 0, 99) | (substr(code,1,1) == "B" & between(as.numeric(substr(code, 2, 3)), 0, 99))) ~ "Other",
    (substr(code,1,1) == "C" & between(as.numeric(substr(code,2,3)), 0, 99) | (substr(code,1,1) == "D" & between(as.numeric(substr(code, 2, 3)), 0, 48))) ~ "Neoplasms",
    (substr(code,1,1) == "D" & between(as.numeric(substr(code,2,3)), 50, 89)) ~ "Other",
    (substr(code,1,1) == "E" & between(as.numeric(substr(code,2,3)), 00, 90)) ~ "Other",
    (substr(code,1,1) == "I" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Circulatory",
    (substr(code,1,1) == "F" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "M&B",
    (substr(code,1,1) == "G" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "H" & between(as.numeric(substr(code,2,3)), 00, 59)) ~ "Other",
    (substr(code,1,1) == "H" & between(as.numeric(substr(code,2,3)), 60, 95)) ~ "Other",
    (substr(code,1,1) == "J" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Respiratory",
    (substr(code,1,1) == "K" & between(as.numeric(substr(code,2,3)), 00, 93)) ~ "Other",
    (substr(code,1,1) == "L" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "M" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "N" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "Q" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "R" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other",
    (substr(code,1,1) == "O" & between(as.numeric(substr(code,2,3)), 00, 99) | substr(code,1,1) == "P" & between(as.numeric(substr(code,2,3)), 00, 96) | substr(code,1,1) == "S" & between(as.numeric(substr(code,2,3)), 00, 99)
    |substr(code,1,1) == "T" & between(as.numeric(substr(code,2,3)), 00, 98) | substr(code,1,1) == "V" & between(as.numeric(substr(code,2,3)), 01, 99) | substr(code,1,1) == "Y" & between(as.numeric(substr(code,2,3)), 00, 98)
    |substr(code,1,1) == "Z" & between(as.numeric(substr(code,2,3)), 00, 99) | substr(code,1,1) == "U" & between(as.numeric(substr(code,2,3)), 00, 89)
    |substr(code,1,1) == "W" & between(as.numeric(substr(code,2,3)), 00, 99) | substr(code,1,1) == "X" & between(as.numeric(substr(code,2,3)), 00, 99)) ~ "Other"))

{% endhighlight %}

The code works as intended, but it's hard to read. If we need to update or add to that code in the future we are more likely to make a mistake, and that mistake is going to be difficult to spot. It also violates the DRY (don't repeat yourself) principle of programming.

One challenge upon encountering a code block like this is that it may not initially be clear as to how we can improve things, but by taking a systematic approach we can enhance the code in a number of ways. 

Let's try to make this code;

* more readable
* simpler to update and maintain
* more performant

How you prioritise each of the above areas of improvement should be dictated by your specific project requirements, but I would suggest the order above as a good starting point.

## Make code more readable

### Create functions for repeated logic

So, there's a ***lot*** going on in the original code. Let's start with the first line of the `case_when()` function.

{% highlight r %}

(substr(code,1,1) == "A" & between(as.numeric(substr(code,2,3)), 0, 99) | (substr(code,1,1) == "B" & between(as.numeric(substr(code, 2, 3)), 0, 99))) ~ "Other"

{% endhighlight %}

One of the first things you will notice is that there is a lot of repetition. A simple solution to this is to create a function that we can reuse in place of the repeated code.

{% highlight r %}

meets_criteria = function(x, letter, start, end) {
  bool = substr(x,1,1) == letter & between(as.numeric(substr(x,2,3)), start, end)
  return(bool)
}

{% endhighlight %}

This function can be used to refactor the first line of the `case_when()` function to be much more readable.

{% highlight r %}

meets_criteria(code, "A", 0, 99) | meets_criteria(code, "B", 0, 99))) ~ "Other"

{% endhighlight %}

We haven't done anything to improve performance, but we have cut down a lot of the bloat of the original. We can also re-use the `meets_criteria()` function elsewhere in our code base if we need to.

The clearer code is also easier to update and any potential mistakes when changing the code should be easier to spot.

### Fully utilise the tools available

The `case_when()` function allows us to specify a default value and we can see that in the original code a lot of the cases return `'Other'`, so let's specify `'Other'` as a default value and minimise the evaluations we need to perform.

The refactored code now looks like this.

{% highlight r %}

my_data %<>%
  mutate(mapped_value = case_when(
    identify_mapped_value(code, "C", 0, 99) | 
      identify_mapped_value(code, "D", 0, 48) ~ "Neoplasms",
    identify_mapped_value(code, "I", 00, 99) ~ "Circulatory",
    identify_mapped_value(code, "F", 00, 99) ~ "M&B",
    identify_mapped_value(code, "J", 00, 99) ~ "Respiratory",
    TRUE ~ "Other"))

{% endhighlight %}

We now have less code that is more readable ***AND*** more performant as we have greatly reduced the number of evaluations that need to be performed. Let's benchmark our original code with the refactored version.

Benchmarking is a process for measuring the performance of code. It can help to compare different approaches  to a problem and allow us to choose the one that runs more quickly or utilises less memory.

We can use the `microbenchmark` package to compare our approaches to the original. The usage of the package is as follows;

{% highlight r %}

microbenchmark::microbenchmark(
  benchmark_1 = {
    some_code_here
  },
  benchmark_2 = {
    some_more_code_here
  },
  times = 10 # how many times to run each code block
)

{% endhighlight %}

I haven't included the code used to benchmark our approaches due to its verbosity.

Lets run our original code and the refactored approach side by side.

{% highlight r %}

Unit: milliseconds
             expr       mean    median
         original  39.699060 39.805541         
 better_tidyverse   8.108072  7.775865

{% endhighlight %}

The changes have yielded a 5x speed up over the original based on the median execution time. With this in mind I think its fair to say that we fulfilled our aims to make the code more readable, simpler to update and maintain, and more performant.

The approach I took was to refactor the existing methods and cut some unnecessary evaluations, but I didn't deviate very far from the original approach. Making small incremental changes whilst refactoring can be quick and simple whilst yielding large benefit.

However, if we are prepared to rethink the problem and take a different approach, we might be able to improve things further.

Whilst remaining within the tidyverse, one way to improve things would be to cut down the number of calls to `substr()`. We can do this my making 2 temporary columns in our table, containing the `substr()` evaluations, which means that `substr()` will only be called twice.

{% highlight r %}

my_data %<>%
        mutate(
          tmp_l = substr(code, 1, 1),
          tmp_n = substr(code, 2, 3),
          mapped_value = case_when(
            tmp_l == "C" & tmp_n %in% 0:99 |
              tmp_l == "D" & tmp_n %in% 0:48 ~ "Neoplasms",
            tmp_l == "I" & tmp_n %in% 0:99 ~ "Circulatory",
            tmp_l == "F" & tmp_n %in% 0:99 ~ "M&B",
            tmp_l == "J" & tmp_n %in% 0:99 ~ "Respiratory",
            TRUE ~ "Other")) %>%
        select(-tmp_l, -tmp_n)

{% endhighlight %}

The code is still relatively readable, and should perform well. Let's re-run the benchmarks.

{% highlight r %}

Unit: milliseconds
                  expr      mean    median
              original  39.69906  39.80554         
      better_tidyverse  8.108072  7.775865
 even_better_tidyverse  7.546461  5.343341

{% endhighlight %}

The change in methodology has allowed for us to get a further 1.5x increase in the median execution time of the code. A tidyverse expert could likely extract a little more performance here, but I'm pretty happy with the 7.4x speed up in the median execution time between the original and the refactored code.

But... what if we don't mind straying from the tidy code approach? When deciding on which frameworks and packages to use, one must consider the current project structure, collaborators, and complexity. For example, importing `data.table` to try and speed up this code without utilising it throughout the project isn't really sensible. However, reverting to base R should always be a consideration.

### Harnessing the power of base R

I'm going to start by defining mappings in a nested list structure. This structure closely resembles a list of tuples, though sadly R has no built in tuple object.

{% highlight r %}

lookups = list(
  list("C", 0, 99, "Neoplasms"),
  list("D", 0, 48, "Neoplasms"),
  list("I", 0, 99, "Circulatory"),
  list("F", 0, 99, "M&B"),
  list("J", 0, 99, "Respiratory")
)

{% endhighlight %}

Next, I will define a function to map the values in the code column to the lookup object. 

We start with 2 calls to `substr()` initially to extract the values we will match on into the `letter` and `number` vectors, similarly to how we made temporary columns in the tidyverse approach.

Next, we create a `mapped_value` vector of the same length as the vector that we pass to the `code` argument and populate it with the default value "other".

Finally, we loop through the nested lists of `lookups` and set the final values.

{% highlight r %}

add_mapped_value = function(code, lookups) {
  
  letter = substr(code,1,1) 
  number = as.numeric(substr(code,2,3)) 
  
  mapped_value = rep("Other", length(code))
  
  for (lu in lookups) {
    mapped_value[(letter == lu[[1]]) & (number %in% lu[[2]]:lu[[3]])] = lu[[4]]
  }
  
  return(mapped_value)
}

{% endhighlight %}

To add the values to the data.frame we simply instantiate a new column and assign the output of our function.

{% highlight r %}

my_data$mapped_value = add_mapped_value(my_data$code, lookups)

{% endhighlight %}

Let's run the benchmarks again.

{% highlight r %}

Unit: milliseconds
                  expr      mean    median
              original  39.69906  39.80554         
      better_tidyverse  8.108072  7.775865
 even_better_tidyverse  7.546461  5.343341
                  base  2.968195  2.806773

{% endhighlight %}

The base R approach is over 14x faster than the original and 1.9x faster than the tidyverse alternative.

However, whilst we have improved readability and performance with the base R approach over the original, I think the `add_mapped_value()` function is relatively complex depending on a persons familiarity with the language, so the tidy approach may win on the readability front.

## Next steps

Refactoring code is an excellent way to improve your programming skills. It encourages us to think about why we did something, and how we might do it better.

Start by reviewing code you wrote in the past, ideally something from a few months ago and look for any parts that you find difficult to understand. Could these parts be refactored to be easier to understand and improve performance?

Try to get familiar with the `microbenchmark` package as a method for comparing performance.