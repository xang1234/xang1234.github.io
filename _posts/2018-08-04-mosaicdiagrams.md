---
title: "Mosaic Diagrams"
date: 2018-08-04
tags: [Visualization, Mosaic Diagrams, MariMekko, ggplot2]
excerpt: "Visualization, Mosaic Diagrams, MariMekko"
---

### Introduction

Mosaic diagrams or MariMekko diagrams are an alternative to bar plots . Traditional bar plots have categories on one axis and quantities on the other. If we have 2 categories we would normally use multiple bar plots to display the data. With mosaic diagrams, the dimensions on both the x and y axis vary in order to reflect the different proportions. Consider a hypothetical company with sales across the USA. Sales can be categorized by state and by product category. We would typically use multiple bar charts to represent the data.

### Data



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

<img src="{site.url}{site.baseurl} /images/mosaic_diagrams/barplot.jpeg" alt="">
