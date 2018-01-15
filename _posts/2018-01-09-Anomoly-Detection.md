---
layout: post
title: The Importance of Data Visualization.
description: Before we perform any analysis and come up with any assumptions about the distributions of and relationships between variables in our datasets, it is always a good idea to visualize our data in order to understand their properties and identify appropriate analytics techniques. In this post, let's see the dramatic differences in conclutions that we can make based on (1) simple statistics only, and (2) data visualization.
keywords: data
tags: [R, Data Science, Machine Learning]
comments: false
---

The Anscombe dataset, which is found in the base R datasets packege, is handy for showing the importance of data visualization in data analysis. It consists of four datasets and each dataset consists of eleven (x,y) points.

**The Four Data Sets**

```{r echo=TRUE}
data(anscombe)
```

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

