---
title: "Chord Diagrams in R with chorddiag"
date: 2018-08-07
tags: [Visualization, Chord Diagrams, Migration Flows, chorddiag, R]
excerpt: ""
comments: true
---
### Introduction
[Chord Diagrams](https://en.wikipedia.org/wiki/Chord_diagram) are a beautiful way of visualizing flows between various entities, for example trade and migration flows between nations. Sander and Abel used them effectively in their representation of [global migration flows](http://www.global-migration.info/) which was published in the [Science Magazine](http://science.sciencemag.org/content/343/6178/1520?ijkey=ypit4%2Fxi7wo4M&keytype=ref&siteid=sci). While their implementation uses javascript, it is possible to use R to produce interactive chord diagrams via the wrapper [chorddiag](https://github.com/mattflor/chorddiag) by Matt Flor. 
{: style="text-align: justify;"}


### Data


http://www.global-migration.info/


### Chord Diagrams

{% include cord-diag.html %}
