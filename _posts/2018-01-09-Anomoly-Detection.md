---
layout: post
title: Anomaly Detection with R
description: Anomaly detection is used for many different applications. It is a commonly used technique for fraud detection. It is also used in manufacturing to detect anomalous systems such as aircraft engines. It can also be used to identify anomalous medical devices and machines in a data center.
keywords: data
tags: [R, Data Science, Machine Learning]
comments: false
---

I will implement an anomaly detection algorithm and apply it to detect failing servers on a network.

First, we will start with 2D data and detect anomalous servers based on two features. We will plot the 2D data and see the algorithm's performance on a 2D plot. The features measure the through-put (mb/s) and latency (ms) of response of each server.
I will use a gaussian model to detect anomalous examples in our dataset. We use multivariate normal distribution to detect servers with very low probabilities and hence can be considered anomalous (outliers).


MathJax

f(x)=(2\pi)^\frac{-k}{2} |\Sigma|^\frac{-1}{2}e^{-1/2(x-\mu)^{'} \Sigma^{-1}(x-\mu)}

````
library(magrittr)  
library(ggplot2)  
library(MASS)      
library(caret) 
library(reshape2) 
````

````
load("data1.RData")
````

````
x=data1$X
x=data1$Xval   # This is cross-validation data
y =data1$yval  # This shows which rows in Xval are anomalous
````
