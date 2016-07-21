[![Travis-CI Build Status](https://travis-ci.org/skiptoniam/dhmpr.svg?branch=master)](https://travis-ci.org/skiptoniam/dlmpr)

dlmpr is an r package for simulating dynamic habitat meta-population models.
----------------------------------------------------------------------------

The package aims to run dynamic habitat meta-population models in R using a modular framework.

``` r
devtools::install_github('skiptoniam/dhmpr')
```

Because we are interested in matrix population models (or stage based models) we have develop a matrix population model for a single population. Here I have generated a simple example of matrix population with three stages.

``` r
library(dlmpr)
transition_matrix <- as.transition(matrix(c(0.53,0.00,0.42,
                                      0.10,0.77,0.00,
                                      0.00,0.12,0.90),nrow = 3,ncol = 3,byrow = TRUE))
```

We can now look at the standard matrix population metrics for this population.

``` r
summary(transition_matrix)
```

    ## $lambda
    ## [1] 0.961153
    ## 
    ## $stable.stage.distribution
    ## [1] 0.3922036 0.2051778 0.4026185
    ## 
    ## $reproductive.value
    ## [1] 1.000000 4.311530 6.868017
    ## 
    ## $sensitivity
    ##            [,1]       [,2]       [,3]
    ## [1,] 0.09703147 0.05076115 0.09960813
    ## [2,] 0.41835413 0.21885823 0.42946344
    ## [3,] 0.66641380 0.34862844 0.68411030
    ## 
    ## $elasticity
    ##            stage.1    stage.2    stage.3
    ## stage.1 0.05350520 0.00000000 0.04352628
    ## stage.2 0.04352628 0.17533196 0.00000000
    ## stage.3 0.00000000 0.04352628 0.64058402
    ## 
    ## $name.mat
    ## [1] "structure(c(0.53, 0.1, 0, 0, 0.77, 0.12, 0.42, 0, 0.9), .Dim = c(3L, "          
    ## [2] "3L), .Dimnames = list(c(\"stage.1\", \"stage.2\", \"stage.3\"), c(\"stage.1\", "
    ## [3] "\"stage.2\", \"stage.3\")))"                                                    
    ## 
    ## $m.names
    ## [1] "stage.1" "stage.2" "stage.3"

We can also plot the population dynamics based on our population matrix.

``` r
par(mar=c(1,1,1,1))
plot(transition_matrix)
```

![](readme_files/figure-markdown_github/single_pop_plot-1.png)<!-- -->

Having assessed the matrix population model for this population we can look at how a-spatial projection of this population will shift over time.

``` r
transition_matrix <- as.transition(matrix(c(.53,0,.42,0.1,0.77,0,0,0.12,0.9),nrow = 3,ncol = 3,byrow = TRUE))
pop <-   as.population(c(80,40,10))
dm1 <- demographic(pop=pop,tmat=transition_matrix,time=100,nrep=100)
```

We can now look at how this single population changes through time.

``` r
dev.off()
```

    ## null device 
    ##           1

``` r
plot(dm1)
```

We can all see the different stages in the population structure change through time.

``` r
plot(dm1,mean_pop = FALSE)
```

![](readme_files/figure-markdown_github/plot_all_stages1-1.png)<!-- -->

Have assessed how the population changes over time, we can include stochasticity to our projections of population change through time. Here we include demographic uncertainty to each step in the stage based model by including `matsd` in to the demographic model run, we do this by adding a simple stochastic element by creating a the same sized matrix filled with he `runif` call values.

``` r
transition_matrix <- as.transition(matrix(c(.53,0,.42,0.1,0.77,0,0,0.12,0.9),nrow = 3,ncol = 3,byrow = TRUE))
matsd <- matrix(runif(dim(transition_matrix$stage_matrix)[1]*dim(transition_matrix$stage_matrix)[1]), dim(transition_matrix$stage_matrix)[1],dim(transition_matrix$stage_matrix)[2])
pop <- as.population(c(80,40,10))
dm2 <- demographic(pop=pop,tmat=transition_matrix,matsd = matsd, estdem = TRUE,time=100,nrep=100)
```

We can now look at how this single population changes over time with demographic uncertainty.

``` r
plot(dm2)
```

![](readme_files/figure-markdown_github/plot_all2-1.png)<!-- -->

We can all see the different stages in the population structure change through time with demographic uncertainty.

``` r
plot(dm2,mean_pop = FALSE)
```

![](readme_files/figure-markdown_github/plot_all_stages2-1.png)<!-- -->
