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
 ## Node number 1: 3248 observations,    complexity param=0.1730933
 ##   mean=5.848522, MSE=0.8305027 
 ##   left son=2 (2247 obs) right son=3 (1001 obs)
 ##   Primary splits:
 ##       alcohol              < 10.85    to the left,  improve=0.17309330, (0 missing)
 ##       density              < 0.992265 to the right, improve=0.11069620, (0 missing)
 ##       chlorides            < 0.0395   to the right, improve=0.07529120, (0 missing)
 ##       free.sulfur.dioxide  < 11.75    to the left,  improve=0.03771371, (0 missing)
 ##       total.sulfur.dioxide < 153.5    to the right, improve=0.03310689, (0 missing)
 ##   Surrogate splits:
 ##       density              < 0.992065 to the right, agree=0.870, adj=0.579, (0 split)
 ##       chlorides            < 0.0355   to the right, agree=0.782, adj=0.292, (0 split)
 ##       total.sulfur.dioxide < 102.5    to the right, agree=0.716, adj=0.077, (0 split)
 ##       sulphates            < 0.335    to the right, agree=0.698, adj=0.020, (0 split)
 ##       fixed.acidity        < 5.25     to the right, agree=0.695, adj=0.010, (0 split)
 ## 
 ## Node number 2: 2247 observations,    complexity param=0.04673914
 ##   mean=5.595461, MSE=0.6173893 
 ##   left son=4 (1172 obs) right son=5 (1075 obs)
 ##   Primary splits:
 ##       volatile.acidity    < 0.2525   to the right, improve=0.09088154, (0 missing)
 ##       free.sulfur.dioxide < 13.5     to the left,  improve=0.04071408, (0 missing)
 ##       alcohol             < 10.15    to the left,  improve=0.03109034, (0 missing)
 ##       citric.acid         < 0.205    to the left,  improve=0.02987939, (0 missing)
 ##       pH                  < 3.325    to the left,  improve=0.02087396, (0 missing)
 ##   Surrogate splits:
 ##       total.sulfur.dioxide < 153.5    to the right, agree=0.587, adj=0.137, (0 split)
 ##       citric.acid          < 0.265    to the left,  agree=0.587, adj=0.136, (0 split)
 ##       pH                   < 3.275    to the left,  agree=0.580, adj=0.123, (0 split)
 ##       alcohol              < 10.05    to the left,  agree=0.575, adj=0.111, (0 split)
 ##       density              < 0.994235 to the right, agree=0.550, adj=0.060, (0 split)
 ## 
 ## Node number 3: 1001 observations,    complexity param=0.03098582
 ##   mean=6.416583, MSE=0.8424423 
 ##   left son=6 (77 obs) right son=7 (924 obs)
 ##   Primary splits:
 ##       free.sulfur.dioxide  < 11.5     to the left,  improve=0.09911648, (0 missing)
 ##       alcohol              < 11.85    to the left,  improve=0.05615603, (0 missing)
 ##       fixed.acidity        < 8.05     to the right, improve=0.04155984, (0 missing)
 ##       pH                   < 3.245    to the left,  improve=0.03745208, (0 missing)
 ##       total.sulfur.dioxide < 78.5     to the left,  improve=0.03096356, (0 missing)
 ##   Surrogate splits:
 ##       total.sulfur.dioxide < 48.5     to the left,  agree=0.932, adj=0.117, (0 split)
 ## 
 ## Node number 4: 1172 observations,    complexity param=0.01113703
 ##   mean=5.368601, MSE=0.5160107 
 ##   left son=8 (172 obs) right son=9 (1000 obs)
 ##   Primary splits:
 ##       volatile.acidity     < 0.4225   to the right, improve=0.04967525, (0 missing)
 ##       free.sulfur.dioxide  < 22.5     to the left,  improve=0.04669360, (0 missing)
 ##       alcohol              < 10.25    to the left,  improve=0.02522470, (0 missing)
 ##       chlorides            < 0.0495   to the right, improve=0.02450074, (0 missing)
 ##       total.sulfur.dioxide < 86.5     to the left,  improve=0.02248320, (0 missing)
 ##   Surrogate splits:
 ##       density       < 0.9912   to the left,  agree=0.858, adj=0.029, (0 split)
 ##       fixed.acidity < 9.85     to the right, agree=0.857, adj=0.023, (0 split)
 ##       citric.acid   < 0.11     to the left,  agree=0.856, adj=0.017, (0 split)
 ##       chlorides     < 0.206    to the right, agree=0.854, adj=0.006, (0 split)
 ##       alcohol       < 8.55     to the left,  agree=0.854, adj=0.006, (0 split)
 ## 
 ## Node number 5: 1075 observations
 ##   mean=5.842791, MSE=0.6106341 
 ## 
 ## Node number 6: 77 observations
 ##   mean=5.415584, MSE=1.048069 
 ## 
 ## Node number 7: 924 observations,    complexity param=0.0138018
 ##   mean=6.5, MSE=0.7348485 
 ##   left son=14 (495 obs) right son=15 (429 obs)
 ##   Primary splits:
 ##       alcohol        < 11.85    to the left,  improve=0.05483062, (0 missing)
 ##       fixed.acidity  < 8.05     to the right, improve=0.04720628, (0 missing)
 ##       pH             < 3.245    to the left,  improve=0.04398018, (0 missing)
 ##       residual.sugar < 1.175    to the left,  improve=0.02526233, (0 missing)
 ##       density        < 0.990005 to the right, improve=0.02499349, (0 missing)
 ##   Surrogate splits:
 ##       density          < 0.99111  to the right, agree=0.713, adj=0.382, (0 split)
 ##       volatile.acidity < 0.305    to the left,  agree=0.661, adj=0.270, (0 split)
 ##       chlorides        < 0.0355   to the right, agree=0.623, adj=0.189, (0 split)
 ##       residual.sugar   < 1.85     to the left,  agree=0.562, adj=0.056, (0 split)
 ##       fixed.acidity    < 6.175    to the right, agree=0.558, adj=0.049, (0 split)
 ## 
 ## Node number 8: 172 observations
 ##   mean=4.982558, MSE=0.4938818 
 ## 
 ## Node number 9: 1000 observations
 ##   mean=5.435, MSE=0.489775 
 ## 
 ## Node number 14: 495 observations
 ##   mean=6.313131, MSE=0.7322518 
 ## 
 ## Node number 15: 429 observations
 ##   mean=6.715618, MSE=0.6510614
 
 library(rpart.plot)
 
 ## Loading required package: rpart
 
 rpart.plot(m.rpart, digits = 3)
 
```

 ### Decision Tree

![Decision-Tree](https://saltfog.github.io/assets/images/Decision-Tree.png)