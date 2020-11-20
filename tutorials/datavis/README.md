Data visualization
================
L Hama
2020-11-20

  - [Intro + Setup](#intro-setup)
  - [Why R?](#why-r)
      - [BaseR](#baser)
      - [ggplot2](#ggplot2)
  - [JS (web)](#js-web)
  - [Real data](#real-data)
  - [Watching List](#watching-list)
  - [More?](#more)
  - [References](#references)

## Intro + Setup

This is a beginner session, if you feel you know the basics, jump to the
reading list for something more useful. And it is focused on the tools
and techniques more than the foundations and theoretical body of work on
scientific/information visualization. Also, as we have only two hours,
we will be spending maybe just minutes pointing to and referring you to
the reading list below. Therefore we will spend the session discussion
choices, libraries and of course examples of generating visualizations
using R and R based wrapper packages for your projects and wider
research work.

I think we all agree that: - it would be hard to do any data analysis
without charts - even more difficult to communicate it without
visualizing them - as bonus, you could even get an audience moving with
changing bars to tree maps, watch [John
Stasko](https://en.wikipedia.org/wiki/John_Stasko)’s EuroVis capstone
[talk](https://vimeo.com/98986594) if you have time to find out why. But
be warned\!

## Why R?

``` r
plot(iris[,1:4])
```

### BaseR

``` r
rownames(installed.packages(priority="base"))
```

    ##  [1] "base"      "compiler"  "datasets"  "graphics"  "grDevices" "grid"     
    ##  [7] "methods"   "parallel"  "splines"   "stats"     "stats4"    "tcltk"    
    ## [13] "tools"     "utils"

``` r
# rownames(installed.packages(priority="recommended"))
# stats, graphics etc
```

This is actually not the most basic R barplot but OK we accept it:

``` r
# create dummy data
d = data.frame(
  name=letters[1:5],
  value=sample(seq(4,15),5)
)
# barplot(height=d$value, names=d$name)
# this is the most basic R barplot
# barplot(height=d$value)
# or
# barplot(iris[1:10,1])
# with names
# barplot(iris[1:10,1], names=iris[1:10,5])
```

### ggplot2

Lets start similarly with the most basic barplot:

``` r
library(ggplot2)
# p = ggplot(data = iris[1:10,], aes(x = Species, y = Sepal.Length)) +
#   geom_bar(stat='identity')
p = ggplot(diamonds, aes(cut)) +
  geom_bar(fill = "#0073C2FF")
# p
```

    ggplot(data = <DATA>) + 
      <GEOM_FUNCTION>(mapping = aes(<MAPPINGS>))

Remember this session should not be about `ggplot2`, otherwise I have
missed my point. Having said that, the above needs some clarification: -
`data` is your data, work with columns, keep your rows for your “lines”
- `GEOM_FUNCTION` is a
[variety](https://ggplot2.tidyverse.org/reference/), examples are
`geom_bar`, `geom_histogram` - crucailly the `mapping` variable is the
crucial function
[`aes`](https://ggplot2.tidyverse.org/reference/aes.html) or the fancy
`Construct aesthetic mappings` expression.

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

#### Boxplots

I have great memories of Box plots, the reason is the type of studies I
did during my PhD and maybe something special about Box plots in general
too. For some reason someone has written an R package using Shiny and
got themselves a space in “Correspondence” section in Nature (Spitzer et
al. 2014), I do think that is en overstatement though.

``` r
p = ggplot(iris, aes(x = Species, y = Sepal.Length)) +
  geom_boxplot(width = 0.4, fill = "white") +
  geom_jitter(aes(color = Species), 
              width = 0.1, size = 1) +
  scale_color_manual(values = c("#00AFBB", "#E7B800", "#E7B8AF")) 
# p
```

## JS (web)

I use React as my JS library (framework if you like) and the package I
borrowed from Uber Eng is called ReactVis and that is what I use
direclty. But in R, prominent and stable ones are “plotly” and I should
mention Python’s mighty Bokeh. The latter has of course an
[interface](https://hafen.github.io/rbokeh/articles/rbokeh.html) in R
called `rbokeh`.

So I will pick `plotly` although I fancy Bokeh more for this section.
Shall we try a Box plot? Just because they too like to start with Box
plot.

``` r
library(plotly)
p = plot_ly(midwest, x = ~percollege, color = ~state, type = "box")
# p
```

## Real data

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
\#\# Reading List - R for Data Science by H Wickham & G Grolemund Data
Visualization [chapter](https://r4ds.had.co.nz/data-visualisation.html)
- Visualization analysis and design (Book) (Munzner 2014) - ggplot2:
elegant graphics for data analysis (Book) (Wickham 2016) - The eyes have
it: a task by data type taxonomy for information visualizations
(Shneiderman 1996) - IEEVIS publications and conference.

## Watching List

![bar vis](README_files/bar.png) - John Stasko: The Value of
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

<div id="ref-spitzer2014boxplotr">

Spitzer, Michaela, Jan Wildenhain, Juri Rappsilber, and Mike Tyers.
2014. “BoxPlotR: A Web Tool for Generation of Box Plots.” *Nature
Methods* 11 (2): 121.

</div>

<div id="ref-ggplo2">

Wickham, Hadley. 2016. *Ggplot2: Elegant Graphics for Data Analysis*.
springer.

</div>

</div>
