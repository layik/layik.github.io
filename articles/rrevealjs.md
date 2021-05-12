---
permalink: /rrevealjs.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

16th Apr 2021
# Reproducible Slides with RevealJS

In the latest Drupal Yorkshire meeting, and I am an outsider, they discussed choice of presentations and without thinking about dependencies I recommended revealjs via Rmarkdown. As you can see in this repo (may be I should remove the builds) you can find few revealjs presentations, but I am still happy to consider alternatives of course.

I also said and I have said it elsewhere, R people or Rstats people are generally academic smart people and they need publications. Therefore, it is not surprising that [Rmarkdown](https://rmarkdown.rstudio.com) does a great job in driving [pandoc](https://pandoc.org) to generate revealjs presentations and much more. If the job needs LaTeX that is another blog post. If you do not want to install yet another tool, and manage HTML tags as it is shown in the revealjs docs, then you can stop here.

Clearly you want to read on at least about the route via R. Here is what it looks like to start as someone who has never heard of R to get to some slides in 5 minutes i a clean Docker session:

Setup step does not count, you must be on a "deb" based distribution, so installing docker to run following snippets is not recommended. I spin up an Ubuntu latest image container on macOS Docker to show a clean environment for reproducibility only:

```sh
docker run -it --rm ubuntu 
```

Inside the container now:

### Step one: install R
```sh
root@4d7869d6beda: apt update && apt install r-base -y
```

Should have R:

```sh
root@4d7869d6beda: R
```

### Step two: install rmarkdown && revealjs packages
```r
R version 3.6.3 (2020-02-29) -- "Holding the Windsock"
Copyright (C) 2020 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

...
# standard R stuff
> install.packages("rmarkdown")
> library(rmarkdown)
> render("hello.Rmd") # I know this file does not exist, but we have 5 mins
Error: pandoc version 1.12.3 or higher is required and was not found (see the help page ?rmarkdown::pandoc_available).
> rmarkdown::pandoc_available()
[1] FALSE
```

Now, I could have saved you by installing pandoc, but I wanted you to see the error. One more thanks to open source, out of R console by `ctrl+d` and (you could stay and do it by the way):

### Step three: we need pandoc
```sh
root@4d7869d6beda: apt install pandoc
```

No pressure, four minutes gone.

We have Rmarkdown working:

```sh
# root@4d7869d6beda: apt install nano # yep not there by default
root@4d7869d6beda: echo "# Hello World" >> hello.Rmd
root@4d7869d6beda: R
```
```r
> rmarkdown::render("hello.Rmd")
...blah blah
output...
```

And we should find a `<h1> Hello World</h1>` in the output `.html` which we never defined as output format in our Rmd file but is the default. Now I *wish* I could get you past Rmarkdown and straight use pandoc, but Rmd does a lot of work so I stay with it.

```r
# we have not installed revealjs
# this step is forward counted in step two.
> install.packages("revealjs") 
# hope this just got our four minutes back, equivalent to locating a .js bundle and placing it in the right location.
```

### Step four (last): revealjs in Rmd

Speaking of Rmd's functionality, if we can write a book with [bookdown](https://bookdown.org/yihui/rmarkdown/revealjs.html), then we should be able to do `revealjs`, too. This is from the book's [intro](https://bookdown.org/yihui/rmarkdown/revealjs.html):

Again from the [book](https://bookdown.org/yihui/rmarkdown/revealjs.html), here is a revealjs yml/sample that needs to go into `hello.Rmd` file:

```r
---
title: "Habits"
author: John Doe
date: April 15, 2021
output: revealjs::revealjs_presentation
---

# In the morning

## Getting up

- Turn off alarm
- Get out of bed

## Breakfast

- Eat eggs
- Drink coffee

# In the evening

## Dinner

- Eat spaghetti
- Drink wine

## Going to sleep

- Get in bed
- Count sheep

```

Here we are, 4:58 seconds later and no need to go into the R console anymore:

```sh
root@4d7869d6beda: R -e "rmarkdown::render('hello.Rmd')"
```

Now then, did it work?

```sh
root@4d7869d6beda: grep "Drink wine" hello.html 
<li>Drink wine</li>
```

I do not even need to check that on a browser. Was it five minutes? Please let me know.