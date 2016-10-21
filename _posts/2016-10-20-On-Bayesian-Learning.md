---
published: true
layout: post
title: On Statistical Learning
---
## Preface

In this post, we will build up from a very probabilistic driven foundation, introduce MLE and MAP which many other similar posts have already done, but ours will be better formed. Provide an intuition on the beautiful symmetry between Bayesian learning (sampling-based learning) and traditional machine learning (log-likelihood based optimization), where we will compare a optimization-based multi-regression and a Bayesian based multi-regression. At last, we will delve into the slightly irritating topic of understanding, where we formulate a neural network and compare it to a Bayesian-based neural network. The later framework gave the recent rise of  theoretical neural network and how we can form a deeper understanding.



This post is also made of the course content of EE178/278 at Stanford University, and some recent theoretical papers (that I will link at the end).

## Born Out of Probability...

At the very beginning, let's consider a probabilistic system formulated as follows: (it can be called a "Noisy Channel Model" in Electrical Engineering). We can consider most Machine Learning models to be of this fashion. We assume there is some property or attribute out there (if a person is **sick or not**, which would be our X, and Y would be our observation such as whether this person **coughs or not**). We want to have a model where we put in observation, and it magically spins out X, so we can diagnose this poor patient.



![Noisy Channel](http://anie.me/images/noisy_channel.jpg)



The random variable X (that we model, the original signal) is sent through a noisy channel, where some noise is injected (in some way). Pretend the noise is a random variable Z, and we use a function $$h(X, Z)$$ to describe how X is affected by noise Z. When $$g$$ is addition, you get a simple Additive Gaussian Noise Channel: Y = X + Z. Keep in mind that this is a linear assumption on how noise and signal are mixed. Signal Processing community likes this simple model because we can study it completely and know where the MSE (mean square error) lower bound is (since we can have a nonlinear MSE estimate called MMSE).



We must know that an additive model might not be what noise actually works, so an MSE estimation, which is perfect, cannot be obtained. We use an approximation instead called Linear Estimation, where we propose an Estimator as follows: $$\hat{X} = aY + b$$. We can formulate this slightly more generally: $$\hat{X} = g(Y)$$, as you can see, now we can fit in any function we want.



But what if, you say, the estimate is not a continuous number, what if it's a discrete variable like we described before **sick or not**, then it's called a decoding problem in signal processing. We only need to slightly adapt the above diagram to look like:



![Noisy Channel Decoder](http://anie.me/images/noisy_channel_decoder.jpg)



