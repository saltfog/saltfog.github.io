---
layout: post
title: Data Science and Wine Quality
description: Determine which factors have the greatest effect on wine quality, the analysis for a client
keywords: data scienc
comments: true
---


**WineQuality.r**

```r
 
 # Descision Tree
 wine <- read.csv('winequality-white.csv', sep=';', header = TRUE)
 wine <- rbind(wine, read.csv('winequality-red.csv', sep=';', header = TRUE))
 hist(wine$quality)
 
 # The wine quality values appear to follow a fairly normal, bell-shaped distribution, centered around a value of six.
 summary(wine)
 
 ```
 **Hisogram**
 ![Histogram](https://saltfog.github.io/assets/images/histogram.png)
 
 ```
 
 ##  fixed.acidity    volatile.acidity  citric.acid     residual.sugar  
 ##  Min.   : 3.800   Min.   :0.0800   Min.   :0.0000   Min.   : 0.600  
 ##  1st Qu.: 6.400   1st Qu.:0.2300   1st Qu.:0.2500   1st Qu.: 1.800  
 ##  Median : 7.000   Median :0.2900   Median :0.3100   Median : 3.000  
 ##  Mean   : 7.215   Mean   :0.3397   Mean   :0.3186   Mean   : 5.443  
 ##  3rd Qu.: 7.700   3rd Qu.:0.4000   3rd Qu.:0.3900   3rd Qu.: 8.100  
 ##  Max.   :15.900   Max.   :1.5800   Max.   :1.6600   Max.   :65.800  
 ##    chlorides       free.sulfur.dioxide total.sulfur.dioxide
 ##  Min.   :0.00900   Min.   :  1.00      Min.   :  6.0       
 ##  1st Qu.:0.03800   1st Qu.: 17.00      1st Qu.: 77.0       
 ##  Median :0.04700   Median : 29.00      Median :118.0       
 ##  Mean   :0.05603   Mean   : 30.53      Mean   :115.7       
 ##  3rd Qu.:0.06500   3rd Qu.: 41.00      3rd Qu.:156.0       
 ##  Max.   :0.61100   Max.   :289.00      Max.   :440.0       
 ##     density             pH          sulphates         alcohol     
 ##  Min.   :0.9871   Min.   :2.720   Min.   :0.2200   Min.   : 8.00  
 ##  1st Qu.:0.9923   1st Qu.:3.110   1st Qu.:0.4300   1st Qu.: 9.50  
 ##  Median :0.9949   Median :3.210   Median :0.5100   Median :10.30  
 ##  Mean   :0.9947   Mean   :3.219   Mean   :0.5313   Mean   :10.49  
 ##  3rd Qu.:0.9970   3rd Qu.:3.320   3rd Qu.:0.6000   3rd Qu.:11.30  
 ##  Max.   :1.0390   Max.   :4.010   Max.   :2.0000   Max.   :14.90  
 ##     quality     
 ##  Min.   :3.000  
 ##  1st Qu.:5.000  
 ##  Median :6.000  
 ##  Mean   :5.818  
 ##  3rd Qu.:6.000  
 ##  Max.   :9.000
 
 #partition the data
 wine_train <- wine[1:3248, ]
 wine_test <- wine[3249:6497, ]
 
 m.rpart <- rpart::rpart(quality ~ ., data = wine_train)
 m.rpart
 
 summary(m.rpart)
 ## Call:
 ## rpart::rpart(formula = quality ~ ., data = wine_train)
 ##   n= 3248 
 ## 
 ##           CP nsplit rel error    xerror       xstd
 ## 1 0.17309330      0 1.0000000 1.0009756 0.02583728
 ## 2 0.04673914      1 0.8269067 0.8284360 0.02410107
 ## 3 0.03098582      2 0.7801676 0.7908229 0.02375595
 ## 4 0.01380180      3 0.7491817 0.7603411 0.02229241
 ## 5 0.01113703      4 0.7353799 0.7515058 0.02204882
 ## 6 0.01000000      5 0.7242429 0.7434067 0.02183699
 ## 
 ## Variable importance
 ##              alcohol              density     volatile.acidity 
 ##                   39                   22                   13 
 ##            chlorides  free.sulfur.dioxide total.sulfur.dioxide 
 ##                   11                    6                    5 
 ##          citric.acid                   pH            sulphates 
 ##                    1                    1                    1 
 ##        fixed.acidity 
 ##                    1 
 ## 

 library(rpart.plot)
 
 ## Loading required package: rpart
 
 rpart.plot(m.rpart, digits = 3)
 
```

 **Decision Tree**
 
 Because alcohol was used first in the tree, it is the single most important predictor of wine quality.

![Decision-Tree](https://saltfog.github.io/assets/images/Decision-Tree.png)