---
title: "Animated Barplots"
date: 2021-03-01
tags: [barplot, ggplot, gganimate, animation, rstudio, R]
header:
  image: "/images/animated-barplots_files/figure-gfm/unnamed-chunk-2-1.gif"
excerpt: "Make beautiful animated barplots using ggplot & gganimate!"
mathjax: "true"
---


I really like these animated barplots and was wondering if it’s possible
in R to create such beautiful animations. I dug the web and
stackoverflow and R-bloggers had answers for me. It is indeed possible
to create nice animation movies out of plots in R and save them as GIF
or mp4\!

# Goals:

  - create a bar plot as known from “data is beautiful” (youtube
    <https://www.youtube.com/channel/UCkWbqlDAyJh2n8DN5X6NZyg>)
  - use n-COVID-19 data to see the top countries animated
  - smoothen it up\!

Turns out `gganimate` can help us achieve this, by animating the bars created by `ggplot`.

I added as much comments in the code as possible. Have fun recreating /
improving\!

Let’s start by loading the data and do some data wrangling:

``` r
# to get nCov2019 dataset:
#install.packages("remotes")
remotes::install_github("YuLab-SMU/nCov2019")

library(nCov2019) # the dataset
library(plotly) # the animation library
library(tidyverse) # useful for wrangling
library(gganimate) # for the animations
library(gifski)
library(magrittr)
library(magick)

#### LOAD DATA ####
res <- query()
```

    ## last update: 2021-03-14 
    ## Gloabl total  120384528  cases; and  2664362  deaths
    ## Gloabl total affect country or areas: 221
    ## Gloabl total recovered cases: 300658
    ## last update: 1 
    ## Total Candidates Programs : 51 
    ## Total Candidates Programs : 54

``` r
y =res$historical
d <- y["global"]
# get global data:

# FORMAT DATA
formatted <- d %>% 
  group_by(date) %>% # group by the date
  mutate(rank = min_rank(-cases) * 1, # create a rank for every day 
         Value_lbl = paste0(" ", round(cases))) %>%  # create labels for the values
  group_by(country) %>% 
  filter(rank <= 10) %>% # get the top 10 ranks every day
  filter(date > as.Date("2020-01-01")) %>%  # filter custom date
  ungroup()

formatted$date = as.Date(formatted$date)

head(formatted)
```

    ## # A tibble: 6 x 7
    ##   country     date       cases deaths recovered  rank Value_lbl
    ##   <chr>       <date>     <int>  <int>     <int> <dbl> <chr>    
    ## 1 Afghanistan 2020-01-22     0      0         0     7 " 0"     
    ## 2 Afghanistan 2020-01-23     0      0         0     9 " 0"     
    ## 3 Afghanistan 2020-01-24     0      0         0    10 " 0"     
    ## 4 Albania     2020-01-22     0      0         0     7 " 0"     
    ## 5 Albania     2020-01-23     0      0         0     9 " 0"     
    ## 6 Albania     2020-01-24     0      0         0    10 " 0"

The animation:

``` r
# animate
animated <- 
  # create a ggplot with ..
  ggplot(formatted, aes(rank, group = country, 
        fill = as.factor(country), color = as.factor(country))) +
  # the aesthetics for the bars: y position, height, alpha is the opacity
  geom_tile(aes(y = cases/2,
                height = cases,
                width = 0.9), alpha = 0.8, color = NA) +
  # the labels for the countries in front, the paste0
  # needs to be there for the right position
  geom_text(aes(y = 0, label = paste(country, " ")), vjust = 0.1, hjust = 1) +
  # the labels for the numbers. 
  geom_text(aes(y=cases, label = paste0(Value_lbl), hjust = 0)) +
  # flip the coordinate system, clip off for the right display
  coord_flip(clip = "off", expand = FALSE) +
  # reverse the x-scale to have the largest bar on top
  scale_x_reverse() +
  scale_y_continuous(labels = scales::comma) +
  # to make the background grid lines moving, use view_follow
  view_follow() +
  # this is for removing redundant labels etc:
  guides(color = FALSE, fill = FALSE) +
  # change background & theme
  theme(axis.line=element_blank(),
        axis.text.x=element_blank(),
        axis.text.y=element_blank(),
        axis.ticks=element_blank(),
        axis.title.x=element_blank(),
        axis.title.y=element_blank(),
        legend.position="none",
        panel.background=element_blank(),
        panel.border=element_blank(),
        panel.grid.major=element_blank(),
        panel.grid.minor=element_blank(),
        panel.grid.major.x = element_line( size=.1, color="grey" ),
        panel.grid.minor.x = element_line( size=.1, color="grey" ),
        # aesthetics for the title, subtitle etc.
        plot.title=element_text(size=20, hjust=0.5, face="bold", colour="grey", vjust=-1),
        plot.subtitle=element_text(size=15, hjust=0.5, face="italic", color="grey"),
        plot.caption =element_text(size=8, hjust=0.5, face="italic", color="grey"),
        plot.background=element_blank(),
        plot.margin = margin(2, 2, 2, 4, "cm")) +
  # the transition between the states is defined here
  transition_states(date, transition_length = 25, state_length = 5) +
  # to make the countries overtake each other more smoothly:
  # more options to smoothen the transitions
  enter_grow() +
  exit_shrink() +
  ease_aes("cubic-in-out") +
  labs(title = 'Cumulative confirmed cases on {closest_state}',  
       subtitle  =  "Top 10 Countries",
       caption  = "Tianzhi Wu, Erqiang Hu, Xijin Ge*, Guangchuang Yu*. 
         Open-source analytics tools for studying the COVID-19 coronavirus outbreak. medRxiv, 2020.02.25.20027433. 
         doi: https://doi.org/10.1101/2020.02.25.20027433") 

# this is the animation here
animate(animated, duration = 75, fps = 25, renderer = magick_renderer())
```

<img src="{{ site.url }}{{ site.baseurl }}/images/animated-barplots_files/figure-gfm/unnamed-chunk-2-1.gif" alt="here, the animation should show up...">


[Click here for the github repo](https://github.com/glaswasser/animated-running-corona-bar-plot)
