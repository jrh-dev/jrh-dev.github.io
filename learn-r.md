---
layout: article
titles:
    # @start locale config
    en      : &EN       Learn R
    en-GB   : *EN
    en-US   : *EN
    en-CA   : *EN
    en-AU   : *EN
    zh-Hans : &ZH_HANS  
    zh      : *ZH_HANS
    zh-CN   : *ZH_HANS
    zh-SG   : *ZH_HANS
    zh-Hant : &ZH_HANT  
    zh-TW   : *ZH_HANT
    zh-HK   : *ZH_HANT
    ko      : &KO       
    ko-KR   : *KO
    fr      : &FR       
    fr-BE   : *FR
    fr-CA   : *FR
    fr-CH   : *FR
    fr-FR   : *FR
    fr-LU   : *FR
    tr      : &TR       
    # @end locale config
key: page-learn-r
---

## An introduction

R is a free software environment for statistical computing and graphics. It is a complete programming language, particularly well suited to statistical and data analysis. R is known for having a comprehensive range of packages available offering extended functionality, and for its active community.

I am a data analyst and R developer. I also work extensively with python and generally enjoy technology, building PC's, running multiple linux distros, and self hosting all-sorts of services with various levels of success, though I don't know how to fix printers. I'm largely self-taught when it comes to programming and don't have a traditional CS background.

Many of the current resources available for learning R focus heavily on the 'tidyverse', a collection of packages (add-ons to the R language) which push an opinionated view of best practice and coding style. The packages themselves are of very high quality, but I feel many training materials focus too heavily on the 'tidyverse' at the expense of providing a more thorough understanding of the language itself. 

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
      <h4><h4><a href="{% link _posts/2022-09-21-r-basics-packages.md %}">R Basics - Packages</a></h4></h4>
    </div>
    <div class="item__description">
      <p>R is often praised for the extensive range of high quality packages available for it. But what are packages? Where do they come from? How do you use them? This article aims to provide some answers.</p>
    </div>
  </div>
</div>

<div class="item">
  <div class="item__content">
    <div class="item__header">
      <h4><h4><a href="{% link _posts/2022-09-28-r-basics-vectors.md %}">R Basics - Vectors</a></h4></h4>
    </div>
    <div class="item__description">
      <p>Vectors are a basics and fundamental data structure in R. In essence a collection of elements of the same type. This article goes into detail of how to use these important structures.</p>
    </div>
  </div>
</div>