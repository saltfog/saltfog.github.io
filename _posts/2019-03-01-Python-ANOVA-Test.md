---
title: "Simple demo of a ANOVA test using Python"
description: Analysis of Variance (ANOVA) is a statistical method used to test differences between two or more data set means. 
keywords: data
layout: post
tags: Python, Data Science, MachineLearning
comments: no
output:
  html_document:
    keep_md: yes
---

{% highlight python %}

# Anova One Way
from numpy.random import seed
from numpy.random import randn
from scipy.stats import f_oneway
from matplotlib import pyplot

# Seed the random number generator
seed(129)

# Generate three independent samples
d1 = 5 * randn(100) + 50
d2 = 5 * randn(100) + 50
d3 = 5 * randn(100) + 50

# Compare the above samples 
stat, p = f_oneway(d1, d2, d3)
print('Statistics=%.3f, p=%.3f' % (stat, p))

# Set the alpha
alpha = 0.05
if p > alpha:
	print('Same distributions (fail to reject H0)')
else:
	print('Different distributions (reject H0)')
    
# create histogram plot
    
pyplot.xlabel('X')
pyplot.ylabel('Frequency')
pyplot.title('Histogram of the three data sets')
pyplot.hist(d1, alpha=0.75) 
pyplot.hist(d2, alpha=0.75) 
pyplot.hist(d3, alpha=0.75)
pyplot.grid(True)

# show line plot
pyplot.show()

{% endraw %}
{% endhighlight %}

**Histogram**
 ![Histogram](https://saltfog.github.io/assets/images/ANOVA-Histogram.png)