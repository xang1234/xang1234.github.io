---
title: "Using Multiprocessing to speed up web scraping "
date: 2018-08-12
tags: [Machine Learning, Multiprocessing, python]
excerpt: ""
---
### Introduction
Webscraping or calling APIs to collect data can take significant amounts of time due to the quantities of data involved. We previously saw how we could build a [simple web scraper]({{site.url }}{{site.baseurl }}/scrapingfinviz) to collect data from [Finviz](https://finviz.com/). Depending on the number of tickers that we want to scan, this can take a significant amount of time.

### Multiprocessing
One way to speed up the web scraping or API calls is to run multiple processes in parallel. The caveat is that the website that we are scraping has to allow us to query it frequently as we will be sending multiple requests from the same IP address. For API calls with authentication, it would be useful to have multiple user IDs. One advantage of python over R is that python allows us to run processes on multiple cores. The [multiprocessing library](https://docs.python.org/3.6/library/multiprocessing.html)
does this by circumventing the [Global Interpreter Lock](https://docs.python.org/3.6/glossary.html#term-global-interpreter-lock) via sub processes.

### Process or Pool
In practice, multiprocessing offers us 2 classes; `Process` and `Pool`.

#### Process
The `Process` class manages an individual process. To spawn a process, we need to create a `Process` object and then call its start method. To run multiple processes, we will have to create multiple `Process` objects, start them and then collect their results. All the processes will be put into memory and then executed FIFO. In general, `Process` class is better if there are a few jobs to assign and if IO operations are long. As everything is manually assigned, it also gives us more control over how jobs are assigned to the various processes. This is useful if we know that certain jobs require more processing time compared to the others as the parallel processing is only as fast as the slowest process.

In our previous example we wrote a function 

#### Pool
The `Pool` class creates a pool of workers, usually equal to the number of processor cores. We can then assign jobs to these workers for them to process in parallel, a bit like MapReduce. There are a few ways to assign jobs to the workers:
* map / imap
* apply
* map_async
* apply_async
* starmap
* starmap_async

`map` iterates a function over an `iterable` (only one argument) while `apply` calls a functions with certain `arguments`. `imap` is similar to `map` but more memory efficient. `starmap` is like `map`, but expects an `iterable` with multiple arguments.


`map/imap` and `apply` will lock the main program until all process are complete.  This helps if we want to keep the results in a certain order. The `async` methods will retrive results as soon as they are finished. In general, `Pool` is better if there are a lot of jobs to process and IO operations are fast.

We previously wrote a


### Main Program





```
mul

Time Taken:  453.840767621994 pool
Time Taken:  1478.1065587255143 single
