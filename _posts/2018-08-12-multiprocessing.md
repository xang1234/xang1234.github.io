---
title: "Using Multiprocessing to speed up web scraping "
date: 2018-08-12
tags: [Machine Learning, Multiprocessing, python]
excerpt: "Webscraping or calling APIs to collect data can take significant amounts of time due to the quantities of data involved. We previously saw how we could build a simple web scraper to collect data from Finviz. Depending on the number of tickers that we want to scan, this can take a significant amount of time."
---
### Introduction
Webscraping or calling APIs to collect data can take significant amounts of time due to the quantities of data involved. We previously saw how we could build a [simple web scraper]({{site.url }}{{site.baseurl }}/scrapingfinviz) to collect data from [Finviz](https://finviz.com/). Depending on the number of tickers that we want to scan, this can take a significant amount of time.

### Multiprocessing
One way to speed up the web scraping or API calls is to run multiple processes in parallel. The caveat is that the website that we are scraping has to allow us to query it frequently as we will be sending multiple requests from the same IP address. For API calls with authentication, it would be useful to have multiple user IDs. One advantage of python over R is that python allows us to run processes on multiple cores. The [multiprocessing library](https://docs.python.org/3.6/library/multiprocessing.html)
does this by circumventing the [Global Interpreter Lock](https://docs.python.org/3.6/glossary.html#term-global-interpreter-lock) via sub processes.

### Process or Pool
In practice, multiprocessing offers us 2 classes; `Process` and `Pool`.

#### Process
The `Process` class manages an individual process. To spawn a process, we need to create a `Process` object and then call its start method. To run multiple processes, we will have to create multiple `Process` objects, start them and then collect their results. All the processes will be put into memory and then executed FIFO. In general, `Process` class is better if there are a few jobs to assign and if IO operations are long. As everything is manually assigned, it also gives us more control over how jobs are assigned to the various processes. This is useful if we know that certain jobs require more processing time compared to the others as the parallel processing is only as fast as the slowest process. Function arguments need to be [pickable](https://docs.python.org/2/library/pickle.html)

In our previous example we wrote a function `scrape_finviz` that scraped data from a list of tickers. We can parallelize the scraping using the `Process` class. In our case we'll use the tickers in the S&P500 from [https://datahub.io/](https://datahub.io/)

```python
from multiprocessing import Pool
from multiprocessing import Process
from multiprocessing import Queue


if __name__ == '__main__':
    ### Load file
    # Define an output queue
    output = Queue()
    spy=pd.read_csv('https://datahub.io/core/s-and-p-500-companies/r/constituents.csv')
    spy_list=spy['Symbol'].tolist()


    ### Split into 4 symbols
    num=4
    div=[spy_list[i::num] for i in range(num)]
    start=time.time()

    ### Multiprocessing with Process
    processes=[Process(target=scrape_finviz,args=(div[i],output)) for i in range(num)]

    # Run processes
    for p in processes:p.start()

    # Get process results from the output queue
    results = [output.get() for p in processes]
    results_df = pd.concat(results)
    print("Time Taken: ",str(time.time()-start))
    results_df.to_csv('D:/multi-process.csv')
    # Exit the completed processes
    for p in processes:p.join()

```

As expected multiprocessing reduces the amount of time needed to scrape the S&P500 tickers:

```python
Time Taken single: 1478.106  
Time Taken multiprocessing : 461.58
```

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

To use pool, we will have to modify the `scrape_finviz` function as it accepts a list of tickers. When we use the `map` function on our list of tickers, `Pool` will assign one ticker at a time to the function `scrape_finviz`. As such is should be rewritten to accept one ticker at a time.

```python
if __name__ == '__main__':
    ### Load file

    spy=pd.read_csv('https://datahub.io/core/s-and-p-500-companies/r/constituents.csv')
    spy_list=spy['Symbol'].tolist()

    ### Multiprocessing with Pool
    p = Pool(4)  
    start=time.time()
    results=p.imap(scrape_finviz2, spy_list)
    p.terminate()
    p.join()
    results_df = pd.concat(results)
    print("Time Taken: ",str(time.time()-start))
    results_df.to_csv('D:/multi.csv')
```
We get similar results to `Pool`:

```python
Time Taken multiprocessing : 453.84
```

### Main Program
When we spawn the new processes via `Process` or `Pool`. This is because Windows does not have `fork`. To distinguish between the parent process and the child process by using `if __name__ == '__main__':`. This protects the 'entry point' of the program. For additional information, refer to the [multiprocessing guidelines](https://docs.python.org/3/library/multiprocessing.html#multiprocessing-programming).
