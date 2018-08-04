---
title: "Mosaic Plots"
date: 2018-08-04
tags: [Visualization, Mosaic Diagrams, MariMekko, ggplot2]
excerpt: "Visualization, Mosaic Diagrams, MariMekko"
---

### Introduction

Mosaic diagrams or MariMekko diagrams are an alternative to bar plots . Traditional bar plots have categories on one axis and quantities on the other. If we have 2 categories we would normally use multiple bar plots to display the data. With mosaic diagrams, the dimensions on both the x and y axis vary in order to reflect the different proportions. Consider a hypothetical company with sales across the USA. Sales can be categorized by state and by product category. We would typically use multiple bar charts to represent the data.
{: style="text-align: center;"}
### Data

The sales data can be split along 2 categories: State and Product Category. State has 7 levels - CT, DE, MA, ME, NJ, NY, PA while Product Category has 4 - General Merchandise, Health Care, Home Health Care and Personal Care.

<img src="{{site.url }}{{site.baseurl }}/images/mosaic_diagrams/Data.jpeg" alt="">


### Bar Plots

We display multiple barplots by category the *facet_wrap* function from ggplot2

```r
library(tidyr)
library(ggplot2)
data<-read.csv('Pharmacy_sales.csv')

ggplot(data,aes(x=STATE.CODE,y=SALES,fill=STATE.CODE))+
  geom_bar(stat="identity")+
  facet_wrap(~MAJOR.CATEGORY,nrow=2,ncol=2)
```

<img src="{{site.url }}{{site.baseurl }}/images/mosaic_diagrams/barplot.jpeg" alt="">


Alternatively we can stack the bars. ggpplot2 does this by default, but we need to remember to set the *stat* argument to *"identity"* within *geom_bar*  

```r
ggplot(data,aes(x=STATE.CODE,y=SALES,fill=MAJOR.CATEGORY))+
  geom_bar(stat="identity")
```

<img src="{ {site.url }}{{ site.baseurl }}/images/mosaic_diagrams/stackedbarplot.jpeg" alt="">

The

### Mosaic plots

Mosaic plots allow us to visualize the both State and Product Category with one plot. However we do lose information about the absolute number of Sales. Instead we are able to compare across categories. To create a mosaic plot using base ggplot2, we would need to compute the respective x and y values for each rectangle. We represent states along the y-axis and Product Categories along the x-axis. We calculate the respective xmin, xmax, ymin and ymax of the by grouping by Product Categories and States.

```r
data_mosaic <-data %>% group_by(STATE.CODE) %>%
  mutate(
    share = SALES / sum(SALES),
    tot_group = sum(SALES)
  ) %>% ungroup()

data_mosaic <-data_mosaic2 %>%
  group_by(MAJOR.CATEGORY) %>%
  arrange(desc(STATE.CODE)) %>%
  mutate(
    ymax = cumsum(tot_group) / sum(tot_group),
    ymin = (ymax - (tot_group/sum(tot_group)))
  ) %>% ungroup() %>%
  group_by(STATE.CODE) %>%
  arrange(desc(MAJOR.CATEGORY)) %>%
  mutate(xmax = cumsum(share), xmin = xmax - share) %>%
  ungroup() %>%
  arrange(MAJOR.CATEGORY)

```
After that we create the mosaic plot using the *geom_rect* function

```r
ggplot(data_mosaic2) +
  geom_rect(aes(ymin = ymin, ymax = ymax, xmin = xmin, xmax = xmax, fill = MAJOR.CATEGORY), colour = "white", size = 0.2)+
  scale_fill_manual("legend", values = c("GENERAL MERCHANDISE" = "grey30", "HEALTH CARE" = "royalblue1", "HOME HEALTH CARE" = "gold","PERSONAL CARE"="tomato"))+theme_light()
```
