---
published: false
layout: post
title: A Geometric Interpretation of Lagrangian and Dual Problem
---
## Preface

This is an article providing another perspective on understanding Lagrangian and dual problem. These two topics are essential to convex and non-convex optimization. Since it is a blog post, the proper background to understand this article is kept rather low. If you need to brush up on convex knowledge, or wish to be introduced to the subject, there is a handout from Stanford CS229 website, and it's written in a very concise and clear way: [Convex Optimization (I)](http://cs229.stanford.edu/section/cs229-cvxopt.pdf) and [Convex Optimization (II)](http://cs229.stanford.edu/section/cs229-cvxopt2.pdf). However, you don't need to understand convex sets, functions in order to follow this post. If you are like me, who took Stanford EE364A, then you might find this post more interesting. The post randomly jumps between Chapter 5 and Chapter 9, 10, and 11. This post is also partially inspired by an office hour conversation with [Nicholas Moehle](http://stanford.edu/~moehle/).

## Lagrangian

In convex optimization, unlike other fields of machine learning, we can add constraints. Convex problems have a form of the following:
$$
\begin{equation*}
\begin{aligned}
& \text{minimize}
& & f_0(x) \\
& \text{subject to}
& & f_i(x) \leq 0, \; i = 1, \ldots, m. \\
&
& & h_i(x) = 0, \; i = 1, \ldots, p.
\end{aligned}
\end{equation*}
$$
If you are  familiar with convex optimization or other optimization problem, this formula should look familar. The constraints listed under `subject to` are considered **hard** constraints, and those constraints do not normally appear in traditonal machine learning cost functions. One way to distinguish them is that, cost functions in Machine Learning does not need to have a realistic meaning (objective can be an abstract measurement of progress), where in convex optimization, objective is something you want to find (like how much fuel the rocket will need).

However, it has been quite difficult to optimize with these hard constraints. Typical methods include turning 
