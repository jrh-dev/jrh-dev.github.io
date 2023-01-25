---
layout: article
title : "Learn R"
key: page-learn-r
---

## An introduction

R is a free software environment for statistical computing and graphics. It is a complete programming language, particularly well suited to statistical and data analysis. R is known for having a comprehensive range of packages available offering extended functionality, and for its active community.

I am a data analyst and R developer. I also work extensively with python and generally enjoy technology, building PC's, running multiple linux distros, and self hosting all-sorts of services with various levels of success, though I don't know how to fix printers.

Many of the current resources available for learning R focus heavily on the 'tidyverse', a collection of packages (add-ons to the R language) which push an opinionated view of best practice and coding style. The packages themselves are of very high quality, but I feel many training materials focus too heavily on the 'tidyverse' at the expense of providing a more thorough understanding of the language itself. I also believe that a lot of designs and best practice developed within the python community can translate well into R and try to advocate for an approach that may feel more familiar to python users whilst recognising R's key strengths.

With that in mind I started to develop a collection of training materials that I hope will allow beginners, and perhaps more experience users alike, to build a strong foundational understanding of the language, that encourages the use of base R, with additional packages where appropriate.

## The topics

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-09-15-intro-to-rstudio.md %}">Introduction to RStudio</a></h4></h4>
    </div>
    <div class="item__description">
      <p>We start by not looking at the R language at all, rather we look at the popular RStudio integrated development environment. Use of RStudio is completely optional, but it does provide a great tool for working in R which will make using the language easier.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-09-21-r-basics-packages.md %}">Packages</a></h4></h4>
    </div>
    <div class="item__description">
      <p>R is often praised for the extensive range of high quality packages available for it. But what are packages? Where do they come from? How do you use them? This article aims to provide some answers.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-09-28-r-basics-vectors.md %}">Vectors</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Vectors are a basics and fundamental data structure in R. In essence a collection of elements of the same type. This article goes into detail of how to use these important structures.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-10-05-r-basics-dataframes-part-1.md %}">Data Frames - Part 1</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Data frames are a fundamental part of R and the functionality they provide plays an integral role in many analysis and data science based workflows. Part 1 covers the basics of interacting with these key data structures.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-10-12-r-basics-dataframes-part-2.md %}">Data Frames - Part 2</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Part 2 of data frame basics provides more details on working with these key data structures and how they fit into a workflow.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-11-03-r-basics-explore-data.md %}">Explore Data</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Exploring data before proceeding to perform analysis or further processing allows for making informed decisions regarding how to structure a project and helps to identify the types of techniques which may work well with the data you have. This article looks at some useful techniques.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-11-03-r-basics-data-processing.md %}">Data Processing</a></h4></h4>
    </div>
    <div class="item__description">
      <p>A look at some of the steps involved in a typical R workflow involving a number of sequential steps from importing the data into the R environment through to outputting a completed analysis.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-11-10-r-basics-data-transformation.md %}">Data Transformation</a></h4></h4>
    </div>
    <div class="item__description">
      <p>R provides plenty of options for data transformation and the rich package system means that there is typically a ready made solution for most of the problems that you might encounter. This article looks at common transformation tasks for pivoting and joining data.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-11-17-r-basics-grouping.md %}">Grouping</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Data often contains information pertaining to a multitude of groups and producing summary or aggregated statistics for these groups is a common task. An introduction to some powerful tools for working with groups.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-11-22-r-basics-factors.md %}">Factors</a></h4></h4>
    </div>
    <div class="item__description">
      <p>A look at factors. One of R's more mysterious data structures and a reminder of its statistical heritage.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-12-01-r-basics-loops.md %}">Loops</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Forget what you've heard, loops in R perform just as well as in other languages. But there are a few things that you should know about.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2023-01-25-r-basics-lists.md %}">Lists</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Lists are a little different to other data structures you typically encounter in R. They can contain elements of different types, including other lists, and offer a large amount of flexibility. </p>
    </div>
  </div>
</div>