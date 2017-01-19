---
published: true
layout: post
title: On Statistical Learning
---
## Preface

In this post, we will build up from a very probabilistic driven foundation, introduce MLE and MAP which many other similar posts have already done, but ours will be better formed. Provide an intuition on the beautiful symmetry between Bayesian learning (sampling-based learning) and traditional machine learning (log-likelihood based optimization), where we will compare a optimization-based multi-regression and a Bayesian based multi-regression. At last, we will delve into the slightly irritating topic of understanding neural network, where we formulate a neural network and compare it to a Bayesian-based neural network. The later framework gave the recent rise of  theoretical neural network and how we can form a deeper understanding.



This post is also made of the course content of EE178/278 at Stanford University, and some recent theoretical papers (that I will link at the end).

## Born Out of Probability...

At the very beginning, let's consider a probabilistic system formulated as follows: (it can be called a "Noisy Channel Model" in Electrical Engineering). We can consider most Machine Learning models to be of this fashion. We assume there is some property or attribute out there (if a person is **sick or not**, which would be our X, and Y would be our observation such as whether this person **coughs or not**). We want to have a model where we put in observation, and it magically spins out X, so we can diagnose this poor patient.



![Noisy Channel](http://anie.me/images/noisy_channel.jpg)



The random variable X (that we model, the original signal) is sent through a noisy channel, where some noise is injected (in some way). Pretend the noise is a random variable Z, and we use a function $$h(X, Z)$$ to describe how X is affected by noise Z. When $$g$$ is addition, you get a simple Additive Gaussian Noise Channel: Y = X + Z. Keep in mind that this is a linear assumption on how noise and signal are mixed. Signal Processing community likes this simple model because we can study it completely and know where the MSE (mean square error) lower bound is (since we can have a nonlinear MSE estimate called MMSE).



We must know that an additive model might not be what noise actually works, so an MSE estimation, which is perfect, cannot be obtained. We use an approximation instead called Linear Estimation, where we propose an Estimator as follows: $$\hat{X} = aY + b$$. We can formulate this slightly more generally: $$\hat{X} = g(Y)$$, as you can see, now we can fit in any function we want.



The reason why this is such a good abstraction is that, we can think about the entire problem as modeling the conditional probability of $$P(X|Y)$$,  and we can easily expand this into: 


$$
f_{X|Y}(x|y) = \frac{f_{Y|X}(y|x)f_X(x)}{f_Y(y)}
$$


As you can easily see, $$f_{Y|X}(y|x)$$ describes the probability density function of our noisy channel, $$f_X(x)$$ corresponds to the distribution of our original signal, so this is supposed to be the perfect estimator. So we can easily know the best this estomator can do, is $$\hat{X}(Y) = E(X|Y)$$. If you are unfamiliar with the continuous probability densitify function notation ($$f$$), you can treat them like $$p$$, or read this [wikipedia article](https://en.wikipedia.org/wiki/Probability_density_function). 



However, we hardly have knowledge of what noisy channel would do to distort the data. Yes, in Electrical engineering we do know! The additive gaussian channel is very applicable in so many situations! But remember machine learning is applied to all aspects of life, for example, given a group of symptoms, can you determine if a person is sick or not. Can you reasonbly ponder what possibly could be the noise and how the noise is mixed with the group of symptoms?



So as we said before, we can use a function to approximate our estimator, the function can be linear, nonlinear, neural network, or anything we hope it to be. Thus we can write the following equation: $$\hat{X} = aY + b​$$. The beauty for this simple case with linear relationship is that we can work out **exactly** what **a** and **b** should be. For those of you who are interested, $$a = \frac{Cov(X, Y)}{\sigma^2_Y}​$$, and $$b = E(X) - aE(Y)​$$. This guarantees to have the smallest mean square error (MSE). However, working out an exact solution for higher-dimensional Y (a sick person can show many symptoms) turns out to be very difficult, and linear estimation isn't always the best solution as well.



We can assume a relationship, a non-linear function that takes in observations $Y$ and parameter vector $$\theta$$, $f(Y, \theta)$. We assume the parameters $\theta$ can be induced from 