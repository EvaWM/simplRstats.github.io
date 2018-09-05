---
title: "Least Squares model fitting"
author: "Evalyne W Muiruri"
date: "5 September 2018"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Least Squares model fitting

Generate some data, removing anything too close to zero for clarity
```{r}

distX <- seq(-200, 200,5)
distX <- distX[abs(distX)>30] # remove any values close to the  
dist <- rep(distX,each=10)
```


Generate the response variables with some noise
```{r }
genresponse<- function(distances, type){
  if(type=="flat"){
    resp <- ifelse(distances >0, 
                  rnorm(length(distances), (4 + 10*exp(-0.05 * distances)), 0.2), 
                  rnorm(length(distances), 4,0.2))
  }
  if(type=="proximity"){
    #' Could expand model to account for higher spm closer to the windfarm due to current changing direction.
    resp <- ifelse(distances >0, 
                  rnorm(length(distances), (4 + 10*exp(-0.05 * distances)), 0.2), 
                  rnorm(length(distances), (4+2*exp(0.1*distances)),0.2))
  }
  spm
}
resX <- genresponse(distX, type="flat")
res <- genresponse(dist, type="flat")
```

Plot the data 
```{r echo=FALSE}
plot(distX, resX)
plot(dist, res)
```

Write a model for a linear relationship where x is negative and an exponential where y is positive

```{r}
mymodel <- function(par, d){
  # par= three model parameters
  # d =distance to edge 
  model <- ifelse(d<0,
                  par[1],
                  par[1] + par[2]*exp(-par[3]*d))
}
```

Predict from this model to check it works as expected

```{r}
pred <- mymodel(par=c(4,8, 0.03), d=dist)
plot(dist, spm, col="grey40"); lines(dist, pred, col="blue")
```


Fit the model using least squares

```{r }
lsq <- function(par, spmd, d){
  model <- ifelse(d<0, par[1],par[1] + par[2]*exp(-par[3]*d))
  rss <- sum((spmd-model)^2)
  rss
}

params <- optim(c(4, 8, 0.04),lsq, spmd=spm, 
               d=dist)
params$par
```

Predict from the model using the optimised parameters and plot the final results

```{r}
pred2 <- mymodel(par=params$par, d=dist)
plot(dist, spm, col="grey40"); lines(dist, pred2, col="blue", lwd=2)
```



## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r cars}
summary(cars)
```

## Including Plots

You can also embed plots, for example:

```{r pressure, echo=FALSE}
plot(pressure)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
