---
published: true
title: Easier Understanding of Stochastic Gradient Descent (SGD)
layout: post
---


First of all, I'm not a math person. However, compared to so many "intuitions" and visualizations online, I think rigorious deduction is the best way to build up intuitions, and is much better than some visualizations.

This post is a watered-down and synthesized version of [Andrew Ng's CS229 Lecture notes 1](http://cs229.stanford.edu/notes/cs229-notes1.pdf).

To understand SGD, the easiest way is to understand a single-variable linear regression.

When we define a linear estimator of random variable X predicting Y: $ h(X) =  \hat{y} $. Since we already know the form of such linear estimator:

<div>$$ h_{\theta}(x) = \theta_0 + \theta_1 x_1 + \theta_2 x_2 $$</div>

We can easily get the below vectorial representation of such linear estimator:

<div> $$ h(x) = \displaystyle\sum_{i=0}^{n} \theta_i x_i  $$ </div>

I recommend reading the "probabilistic interpretation" of the $J(\theta)$ cost function, where you actually derive the cost function, instead of "defining" it. To quickly get to the point of SGD, we can see that cost function is:

<div> $$ J(\theta) = 1/2 * \displaystyle\sum_{i=1}^{m} (h_\theta(x^(i)) − y^(i))^2 $$ </div>





