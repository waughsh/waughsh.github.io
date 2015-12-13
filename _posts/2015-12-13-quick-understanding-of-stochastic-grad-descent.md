---
published: true
title: Easier Understanding of Stochastic Gradient Descent (SGD)
layout: post
---




First of all, I'm not a math person. However, compared to so many "intuitions" and visualizations online, I think rigorious deduction is the best way to build up intuitions, and is much better than some visualizations.

This post is a watered-down and synthesized version of [Andrew Ng's CS229 Lecture notes 1](http://cs229.stanford.edu/notes/cs229-notes1.pdf).

To understand SGD, the easiest way is to understand a single-variable linear regression.

When we define a linear estimator of random variable X predicting Y: h(X) = $ \hat{y} $.
