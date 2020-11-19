Data visualization
================
L Hama
2020-11-19

  - [Introduction](#introduction)
  - [Why R?](#why-r)
      - [BaseR](#baser)
      - [ggplot2](#ggplot2)
      - [JS (web)](#js-web)
  - [Reading List](#reading-list)
  - [Watching List](#watching-list)
  - [More?](#more)
  - [References](#references)

## Introduction

This session will be focused on the tools and techniques more than the
foundations and theoretical body of work on scientific/information
visualization. And as we have only two hours, we will be spending maybe
just minutes pointing to and referring you to the reading list below.
Therefore we will spend the session discussion choices, libraries and of
course examples of generating visualizations using R and R based wrapper
packages for your projects and wider research outputs.

## Why R?

``` r
plot(iris[,1:4])
```

### BaseR

### ggplot2

    ggplot(data = <DATA>) + 
      <GEOM_FUNCTION>(mapping = aes(<MAPPINGS>))

``` r
library(ggplot2)
p = ggplot(mpg, aes(displ, hwy, colour = class)) + 
  geom_point()
# p 
# or 
# ggplot(data = mpg) + 
#   geom_point(mapping = aes(x = displ, y = hwy, color = class))
```

We have seen or heard that ggplot2 can also do “smoothing”. We can see a
simple example from the code above

``` r
p = ggplot(data = mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point(mapping = aes(color = class)) + 
  geom_smooth()
# p
```

Notice the message given by the package making it clear what “method”
and what “formulae” has been use dot generate the smoothing? Let’s jump
ahead by exploring the different methods that are used. The results are
commented out on purpose.

``` r
library(gridExtra)
plots = lapply(list("lm", "gam", "glm"), function(x){
  ggplot(mpg, aes(displ, hwy)) + 
  geom_point(mapping = aes(color = class)) + 
  geom_smooth(method = x)
}) 
# plots
# marrangeGrob(plots, nrow=2,ncol=2)
# ggsave("multiplot.pdf", ml)
```

Dataset - US Elections
(<span class="citeproc-not-found" data-reference-id="VN42MVDX_2017">**???**</span>)

``` r
# get your csv from this link as we cannot link directly to the file
# https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/42MVDX
d = read.csv("~/Desktop/data/us-elections/1976-2016-president.csv")
library(dplyr)
# names(d)
# head(d)
# Winner and runner up candidates
# dw = d[(d$candidatevotes/d$totalvotes) > .4, ]
d = d %>% select(state, year, party, candidatevotes, totalvotes) %>%
  mutate(percent = (candidatevotes/totalvotes) * 100)

# country wide percent
dc = d %>% group_by(year, party) %>%
  summarise(percent = sum(percent), n= n()) %>%
  mutate(percent = percent/n, n = NULL) %>% ungroup()
# head(dc)
# length(unique(dc$party))

# winning parties
dw = dc %>% filter(percent > 25)
# length(unique(dw$party))
# head(dw)

# add democratic-farmer-labor to democrat
# filter(dw, stringr::str_detect(party, "democratic-farmer-labor|democrat"))
# filter(dw, party == "democratic-farmer-labor")

# create new column to identify these two
dw$dems = stringr::str_detect(dw$party, "democratic-farmer-labor|democrat")
# dw
dw = dw %>% group_by(year, dems) %>%
  summarise(percent = sum(percent), n= n()) %>%
  mutate(percent = percent/n, n = NULL) %>%
  mutate(party = ifelse(dems, "democrat", "republican")) %>% # add back party
  mutate(dems = NULL)

# length(unique(dw$party))
dw$percent = format(round(dw$percent, 2), nsmall = 2)

library(ggplot2)

p = ggplot(dw, aes(x = factor(year), y = percent,
               fill = party)) +
  # define the colours for the parties
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label=percent),
            position=position_dodge(width=0.9), vjust=-0.25) +
  scale_fill_manual("legend",
                    values = c("democrat" = "blue",
                               "republican" = "red"))
# p
```

Real work here at UoL
<https://github.com/saferactive/saferactive/blob/ca234078eba91a81f4bb79d0e46d7f67ad0460ca/code/la_trends.R>

<https://github.com/saferactive/saferactive/blob/9760a0b4d0ea3b9572432c81cc549cfed689351d/code/dft-aadf-descriptive.R>

### JS (web)

## Reading List

  - R for Data Science by H Wickham & G Grolemund Data Visualization
    [chapter](https://r4ds.had.co.nz/data-visualisation.html)
  - Visualization analysis and design (Book) (Munzner 2014)
  - ggplot2: elegant graphics for data analysis (Book) (Wickham 2016)
  - The eyes have it: a task by data type taxonomy for information
    visualizations (Shneiderman 1996)
  - IEEVIS publications and conference.

## Watching List

![bar vis](README_files/bar.png) - John Stasko: he Value of
Visualization…and Why Interaction Matters, Eurovis Capstone Talk.
<https://vimeo.com/98986594>

## More?

  - See Roger’s workshop materials on
    [github](http://www.roger-beecham.com/GEOG5042-data-visualization/index.html).

## References

<div id="refs" class="references hanging-indent">

<div id="ref-munzner2014visualization">

Munzner, Tamara. 2014. *Visualization Analysis and Design*. CRC press.

</div>

<div id="ref-shneiderman1996eyes">

Shneiderman, Ben. 1996. “The Eyes Have It: A Task by Data Type Taxonomy
for Information Visualizations.” In *Proceedings 1996 Ieee Symposium on
Visual Languages*, 336–43. IEEE.

</div>

<div id="ref-ggplo2">

Wickham, Hadley. 2016. *Ggplot2: Elegant Graphics for Data Analysis*.
springer.

</div>

</div>
