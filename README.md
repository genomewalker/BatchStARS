## Installation and Usage ##


```r
library(devtools)
install_github("zdk123/BatchStARS")
library(BatchStARS)
```

Make sure the config file `.BatchJobs.R` and the jobs template file is properly specified in your system and in the working directory. An example `.BatchJobs.R` file and `simpletorque.tmpl` file can be found in the [extdata](https://github.com/zdk123/BatchStARS/tree/master/extdata) subdirectory of this github repo.
The `.BatchJobsSerialTest.R` file can be used to test code serially.

Generate some data using the huge package
```r
library(huge)
L <- huge.generator(n = 100, d = 50, graph = "random", g = 40)
```

Use StARS in batch mode to select the correct lambda from the path
```r
lams <- exp(seq(log(.9), log(.05), length.out=20))
sel <- batch.stars(L$data, fun=huge::huge, fargs=list(method='mb', lambda=lams), rep.num=20, regdir="batchtest-files", regid="batchtest")
```

Estimating a sparse graphical models is slower for values of lambda. Therefore, we should use a binary search to minimize the number of lambdas values we need to try. This may save time for problems where the true lambda value is uncertain (especially for datasets with many variables) although it reintroduces serial computation along the lambda path (On order log(n) in theory).
```r
lams <- exp(seq(log(.9), log(.05), length.out=20))
sel <- bin.lam.search(L$data, lams, fun=huge::huge, fargs=list(method='mb'), rep.num=20, regdir="batchtest-files", regid="batchtest")
```
