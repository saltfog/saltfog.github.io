---
layout: post
title: Machine Learning with the Random Forest Algorithm
description: Using a mushroom data set can we determine if a mushroom is editable or poisonous with Machine Learning using R.
keywords: data
tags: [R, Data Science, Machine Learning]
comments: true
---


**Analysis-Random-Forest.r**

Data Source
[archive.ics.uci.edu](https://archive.ics.uci.edu/ml/datasets/mushroom)

```r
 #Load needed librarys
 source('prepare_functions.R')
 library(randomForest)
 library(e1071)
 library(caret)
 library(ggplot2)
 ```
 **Histogram**
 ![Histogram](https://saltfog.github.io/assets/images/histogram.png)
 
 ```
 #Set a consistent seed
  set.seed(123) 
  
  #Major clean and preparation see helper_function.r
  data = prepareAndCleanData()
  
  #Check the first couple of rows of data
  head(data)
 
```
```
Edible CapShape CapSurface CapColor Bruises    Odor GillAttachment
1 Poisonous   Convex     Smooth    Brown    True Pungent           Free
2    Edible   Convex     Smooth   Yellow    True  Almond           Free
3    Edible     Bell     Smooth    White    True   Anise           Free
4 Poisonous   Convex      Scaly    White    True Pungent           Free
5    Edible   Convex     Smooth     Gray   False    None           Free
6    Edible   Convex      Scaly   Yellow    True  Almond           Free

#Create data for training, set confidence level

sample.ind = sample(2, 
                     nrow(data),
                     replace = T,
                     prob = c(0.05,0.95))
data.dev = data[sample.ind==1,]
data.val = data[sample.ind==2,]

#See how data sets look as edible vs poisonous proportion ratio
#To see if the traning and test data are some what equal

table(data$Edible)/nrow(data)
## 
##    Edible Poisonous 
## 0.5179714 0.4820286
table(data.dev$Edible)/nrow(data.dev)
## 
##    Edible Poisonous 
##    0.5025    0.4975
table(data.val$Edible)/nrow(data.val)
## 
##    Edible Poisonous 
## 0.5187727 0.4812273

#Run the random forest algorithm with number of trees being 100
#The more the better, but we don't want to over do it.

rf = randomForest(Edible ~ ., 
                   ntree = 100,
                   data = data.dev)

plot(rf)
```


 **rf plot**
 
 This plot will tell us if the number of trees we pick will give good results.
 As you can see 10-30 trees causes a minor error span.
![unnamed-chuck-13-1](https://saltfog.github.io/assets/images/unnamed-chuck-13-1.png)