---
published: true
title: An Easier Understanding of Stochastic Gradient Descent (SGD)
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

<div> $$ J(\theta) = 1/2 * \displaystyle\sum_{i=1}^{m} (h_\theta(x^{(i)}) − y^{(i)})^2 $$ </div>

As Andrew Ng has already worked out (we skip this part) for the a single training example $ (x, y) $, when we differentiate the actual $J(\theta)$, we can bring the differentiation sign into the summation, and differentiate each term individually, so eventually, from our original update formula:

<div> $$ \theta_j := \theta_j − \alpha \frac{\partial}{\partial \theta_j} J(\theta) $$ </div>

We get:

repeat till converge { 
<div> $$ \theta_j := \theta_j − \alpha \displaystyle\sum_{i=1}^{m} ( y^{(i)} - h_\theta(x^{(i)})) x^{(i)}_j \(for every j\) $$ </div>
}

So yes. According to rigorous math, in order to update one parameter: $\theta_j$, we need to sum up the errors between all pairs of $ (x,y) $. However, this becomes inpractical when dataset gets large, so some genious thought about, why not just update all the parameters for every pair?

This is exactly the core of SGD, instead of updating the parameter's value when we are done, we update as we go through the dataset.







