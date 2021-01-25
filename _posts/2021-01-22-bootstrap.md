---
layout: article
title: The Bootstrap Estimate of Standard  Error 
key: 20210123
tags: Bootstrap Python Statistics InProgress
pageview: false
modify_date: 2021-01-23
aside:
  toc: true
---

Introduction to the bootstrap strategy and some python implementations.

<!--more-->

## Introduction

Bootstrap is a computer-based method for assigning measures of accuracy(bias, variance, confidence intervals, prediction error, etc.) to statistical estimates. The idea is to use the observed sample to estimate the population distribution. Then samples can be drawn from the estimated population and the sampling distribution of any type of estimator can itself be estimated. 

## The general process of bootstrap

**To fix notation:**
- statistic of interest: $s(x)$
- a random observed sample:  $$x=(x_1, x_2,\dots,x_n)$$
- a bootstrap sample: $$x^*=(x^*_1, x^*_2,\dots,x^*_n)$$
- bootstrap replicates: $$s(x^{*1}), s(x^{*2}), \dots, s(x^{*B})$$
- bootstrap estimate of standard error: $$\hat{se}_{boot}$$


![1_bootstrap_process](https://github.com/Yuleii/Yuleii.github.io/raw/master/pictures/plog_pics/20210123/1_bootstrap_process.jpeg)


Figure 1 is the schematics of the bootstrap process for estimating the standard error of a statistic $s(x)$:
1. Generate $B$ bootstrap samples $$x^{*1}, x^{*2},\dots,x^{*B}$$. Each bootstrap sample has $n$ elements, generated by sampling with replacement from the original data set $x$.
2.Bootstrap replicates $$s(x^{*1}), s(x^{*2}), \dots, s(x^{*B})$$ are obtained by calculating the value of the statistic $s(x)$ on each bootstrap sample. If  $s(x)$  is  the sample  median/mean,  for instance,  then  $$s(x^*)$$  is  the  median/mean  of  the  bootstrap  sample.

3. Finally, the bootstrap estimate of standard error of $s(x)$ is the standard deviation  of the bootstrap replications $$s(x^{*1}), s(x^{*2}), \dots, s(x^{*B})$$. 


## Parameter estimate

Suppose we face a common data-analytic situation: a random sample $x=(x_1, x_2, \dots, x_n)$ from an unknown probability distribution $F$ has been observed and we wish to estimate a parameter of interest $\theta=t(F)$ on the basis of $x$. For this purpose, we calculate an estimate $\hat{\theta}=s(x)$ from $x$. How  accurate is  $\hat{\theta}$? Bootstrap sampling can be carried out both non-parametrically and parametrically to estimate the the standard error of a statistic $\hat{\theta}$.

### Non-parametric bootstrap

**To fix notations:**
- a random sample:  $x=(x_1, x_2,\dots,x_n)$
- unknown probability distribution of population: $F$
- empirical distribution of a random sample $x$: $\hat{F}$
- bootstrap sample: $$\hat{F} \rightarrow (x^*_1, x^*_2,\dots,x^*_n)$$
- parameter of interest: $\theta=t(F)$
- estimate of $\theta$: $\hat{\theta}=s(x)$
- bootstrap replication of $$\hat{\theta}:\hat{\theta}^*=s(x^*)$$
- the bootstrap estimate of $se_F(\hat{\theta})$, the standard error of a statistic $\hat{\theta}$ is defined by $se_{\hat{F}}(\hat{\theta}^*)$

In the nonparametric bootstrap a sample of the same size as the data is take from the data with replacement. It means that if you measure 10 samples, you create a new sample of size 10 by replicating some of the samples that you've already seen and omitting others.

![bootstrap algorithm](https://github.com/Yuleii/Yuleii.github.io/raw/master/pictures/20210123/2_bootstrap_algorithm.jpg)

Everything in this case can follow the above bootstrap algorithm without modification when the functional of interest is the parameter, except that we set $$\hat{\theta}^*=s(x^*)$$
:
1. Select  $B$  independent  bootstrap  samples $$x^{*1}, x^{*2},\dots,x^{*B}$$,  each  consisting  of n  data  values  drawn with replacement from $x$

2. Evaluate the bootstrap replication corresponding to each bootstrap sample.    
$$\hat{\theta}(b)=s(x^{*b}), b=1,2,\dots,B$$

3. Estimate the standard  error  $se_F(\hat{\theta})$ by  the sample  standard deviation of the $B$ replications
$$\hat{se}_B=\{\sum_{b=1}^B[\hat{\theta}(b)-\hat{\theta}^*(\cdot)]^2/(B-1)\}^{1/2}$$, where$$\hat{\theta}^*=\sum_{b=1}^B \hat{\theta}^*(b)/B$$


**Remarks:**
- The bootstrap data set  $$x^*=(x^*_1, x^*_2,\dots,x^*_n)$$ consists of members of the original data set  $$x=(x_1, x_2,\dots,x_n)$$, some appearing zero  times, some appearing once,  some  appearing twice,  etc. Thus, we might  have  $$x^*_1  =  x_7,  x^*_2  =  x3,  x^*_3  =  x_3,  x^*_4  =  x_{22},  \dots , x_n^*  =  x_7$$. 

- If  $s(x)$  is  the sample  median/mean,  for instance,  then  $s(x^*)$  is  the  median/mean  of  the  bootstrap  sample.
- The  number  of  bootstrap replications  $B$  for estimating  a  standard  error  is  usually  between  25  and  200. 

### Parametric bootstrap

**To fix notations:**
- parametric bootstrap estimate of standard error: $se_{\hat{F}_{par}}(\theta^*)$
- an estimate of $F$ derived from a parametric model for the data: $\hat{F}_{par}$

Nonparametric  bootstrap makes no assumption about the distribution of the coefficients in the model, while parametric bootstrapping assumes that the data comes from a known distribution with unknown parameters(for example the data may come from a Poisson, negative binomial for counts, or normal for continuous distribution.). The only difference  from  the nonparametric bootstrap is  that  the  samples  are  drawn  from  a  parametric  estimate of the population rather than the non-parametric estimate $\hat{F}$.

Instead of sampling with replacement from  the data,  we  draw  $B$  samples  of size  $n$  from  the  parametric estimate of the population $F_{par}$:
$$\hat{F}_{par} \rightarrow(x^*_1, x^*_2,\dots,x^*_n)$$.
After generating the bootstrap samples,  we  proceed  exactly as  in steps  2  and 3  of the non-parametric bootstrap algorithm:  evaluate statistic on each  bootstrap sample,  and then compute the standard deviation of the $B$  bootstrap replications.



## Bootstrap in Python

```python
import numpy as np
import matplotlib.pyplot as plt
```

### Nonparametric bootstrap

To illustrate, we take 30 bootsrap samples from a population of size 100.

population


```python
# construct a population of size 100
np.random.seed(42)
population = np.random.randint(0,100 , size=100)
population
```




    array([51, 92, 14, 71, 60, 20, 82, 86, 74, 74, 87, 99, 23,  2, 21, 52,  1,
           87, 29, 37,  1, 63, 59, 20, 32, 75, 57, 21, 88, 48, 90, 58, 41, 91,
           59, 79, 14, 61, 61, 46, 61, 50, 54, 63,  2, 50,  6, 20, 72, 38, 17,
            3, 88, 59, 13,  8, 89, 52,  1, 83, 91, 59, 70, 43,  7, 46, 34, 77,
           80, 35, 49,  3,  1,  5, 53,  3, 53, 92, 62, 17, 89, 43, 33, 73, 61,
           99, 13, 94, 47, 14, 71, 77, 86, 61, 39, 84, 79, 81, 52, 23])




```python
# population mean
population.mean()
```




    50.54




```python
# population standard deviation
population.std()
```




    29.278463074417004



orignial sample


```python
# draw a sample of size 30 from population
sample = np.random.choice(population, size=30)
sample
```




    array([75, 47, 83, 61, 88, 21,  2,  7, 47, 49, 74, 94, 51, 86, 94, 70, 87,
           89, 86, 59, 59, 41, 60, 61, 21, 82,  1,  3, 99, 91])




```python
# our first sample mean
sample_mean = sample.mean()
sample_mean
```




    59.6




```python
# standard deiveation for this sample
sample_std = np.std(sample, ddof=1)
sample_std
```




    30.444522750689348




```python
# estimated standard error for the sapmle mann
sample_std/(30 ** 0.5)
```




    5.558383954343928




```python
# theorical standard error for sapmle mann
population.std()/(30 ** 0.5)
```




    5.345491558313417



bootstrap sampling


```python
# bootstrap resampling from empirical CDF. Since each step of our empirical CDF is identical (1/n), 
# sampling from the empirical CDF is the same as re-sampling (with replacement and equal probabilities) 
# from the sample.
boot_means = []
for _ in range(10000):
    bootsample = np.random.choice(sample,size=30, replace=True)
    boot_means.append(bootsample.mean())
```


```python
# simulated mean of mean
bootmean = np.mean(boot_means)
```


```python
# simulated standard deviation of mean
bootmean_std = np.std(boot_means)
```


```python
# simulated mean VS true mean
(population.mean(), bootmean)
```




    (50.54, 59.66076333333333)




```python
# the theorical standard error and simulated standard error
(population.std()/(30 ** 0.5), bootmean_std)
```




    (5.345491558313417, 5.49753810916911)




```python
plt.hist(boot_means)
```




    (array([  16.,   63.,  323., 1235., 2363., 2943., 2111.,  779.,  147.,
              20.]),
     array([37.53333333, 41.70333333, 45.87333333, 50.04333333, 54.21333333,
            58.38333333, 62.55333333, 66.72333333, 70.89333333, 75.06333333,
            79.23333333]),
     <a list of 10 Patch objects>)




    
![png](https://github.com/Yuleii/Yuleii.github.io/raw/master/pictures/20210123/output_18_1.png)
    


### Parametric bootstrap

Suppose the data $x_1, . . . , x_{300}$ is drawn from an $\exp (\lambda)$ distribution. Assume
also that the data mean $x = 2$. Estimate $\lambda$ and give a 95% parametric bootstrap confidence
interval for $\lambda$.


```python
# Given 300 data points with mean 2.
# Assume the data is exp(lambda)
# PROBLEM: Compute a 95% parametric bootstrap confidence interval for lambda

# the number of data points and mean
n = 300
xbar = 2
# the number of bootstrap samples
nboot = 1000
```


```python
# We draw the bootstrap sample from Exponential(lambdahat)
# Each column is one bootstrap sample (of 300 resampled values)
bootstrapsample = np.random.exponential(scale=xbar, size=(n, nboot))
```


```python
sample_means=np.mean(bootstrapsample, axis=0)
```


```python
# simulated mean of mean
bootmean = np.mean(sample_means)
bootmean
```




    1.9992854213203346




```python
# simulated standard deviation of mean
bootmean_std = np.std(sample_means)
bootmean_std
```




    0.11256670761306142




```python
plt.hist(boot_means)
```




    (array([  16.,   63.,  323., 1235., 2363., 2943., 2111.,  779.,  147.,
              20.]),
     array([37.53333333, 41.70333333, 45.87333333, 50.04333333, 54.21333333,
            58.38333333, 62.55333333, 66.72333333, 70.89333333, 75.06333333,
            79.23333333]),
     <a list of 10 Patch objects>)




    
![png](https://github.com/Yuleii/Yuleii.github.io/raw/master/pictures/20210123/output_25_1.png)


confidence interval


```python
lambdahat = 1.0/xbar

# Compute the bootstrap lambdastar
lambdastar = 1.0/sample_means

# Compute the differences
deltastar = lambdastar - lambdahat
 
# Construct confidence interval
upper = lambdahat-np.quantile(deltastar, 0.05)
lower = lambdahat-np.quantile(deltastar, 0.95)

ci = (lower, upper)
ci
```




    (0.45078080625259803, 0.5439570029409425)



## Reference
Most helpful book by Efron, with a comprehensive discussion of the Bootstrap for statistical inference:
- Efron, B., & Tibshirani, R.J. (1994). [An Introduction to the Bootstrap (1st ed.).](https://doi.org/10.1201/9780429246593) Chapman and Hall/CRC.

Other materials:
- [Advanced Data Analysis -The Bootstrap](https://www.stat.cmu.edu/~cshalizi/402/lectures/08-bootstrap/lecture-08.pdf)
- [An Introduction to the Bootstrap Method](https://towardsdatascience.com/an-introduction-to-the-bootstrap-method-58bcb51b4d60)
- [Bootstrap confidence intervals](https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading24.pdf)
- [Statistical Analysis of Genomics Data: 15.3-Bootstrapping](https://online.stat.psu.edu/stat555/node/119/)