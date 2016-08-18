---
published: true
layout: post
title: A Geometric Interpretation of Lagrangian and Dual Problem
---
## Preface

This is an article providing another perspective on understanding Lagrangian and dual problem. These two topics are essential to convex and non-convex optimization. Since it is a blog post, the proper background to understand this article is kept rather low. If you need to brush up on convex knowledge, or wish to be introduced to the subject, there is a handout from Stanford CS229 website, and it's written in a very concise and clear way: [Convex Optimization (I)](http://cs229.stanford.edu/section/cs229-cvxopt.pdf) and [Convex Optimization (II)](http://cs229.stanford.edu/section/cs229-cvxopt2.pdf). However, you don't need to understand convex sets, functions in order to follow this post. If you are like me, who took Stanford EE364A, then you might find this post more interesting. The post randomly jumps between Chapter 5 and Chapter 9, 10, and 11 ([Convex Optimization book by Stephen Boyd](http://stanford.edu/~boyd/cvxbook/bv_cvxbook.pdf )). This post is also partially inspired by an office hour conversation with [Nicholas Moehle](http://stanford.edu/~moehle/).

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
\tag{1}
\label{original}
$$


We define $f_i(x)$ being convex functions, and $h_i(x)$ being affine functions. If you are  familiar with convex optimization or other optimization problem, this formula should look familar. The constraints listed under `subject to` are considered **hard** constraints, and those constraints do not normally appear in traditonal machine learning cost functions. One way to distinguish them is that, cost functions in Machine Learning does not need to have a realistic meaning (objective can be an abstract measurement of progress), where in convex optimization, objective is something you want to find (like how much fuel the rocket will need).



However, it has been quite difficult to optimize with these hard constraints. Typical methods include solving for a Newton step:


$$
\begin{bmatrix}
    \nabla^2f(x)  & A \\
    A & 0
  \end{bmatrix}
    \begin{bmatrix}
    \Delta x_{nt} \\
    w
  \end{bmatrix} =
  \begin{bmatrix}
    - \nabla f(x) \\
    0
  \end{bmatrix}
$$


Solving for $\Delta x_{nt}$ and we get our central-path (feasible) Newton method of satisfying equality constraint as well as optimizing our overall objective. As to why it has such effect, refer to the book.



As to satisfy the inequality constraint, we can use **barrier method**, which adds the inequality constraints as a logarithmic barrier, so instead of optimizing the original problem, we can have:


$$
\begin{equation*}
\begin{aligned}
& \text{minimize}
& & f_0(x) - (1/t) \sum_{i=1}^m \text{log}(-f_i) \\
& \text{subject to}
& & h_i(x) = 0, \; i = 1, \ldots, p.
\end{aligned}
\end{equation*}
$$


The logarithmic barrier punishes the objective logarithmically when it's approaching 0 from the negative side.



So what do those two "implementation" details have to do with Lagrangian? The reason is simple: Lagrangian is simply a way, not unlike equality Newton step or barrier method, to incoroporate constraints into the overall objective. It turns the **hard** constraints into **soft** constraints, constraints that you (or the optimization) can violate. 



Let's look back to equation $\ref{original}$: we can formulate its Lagrangian as: 


$$
L(x, \lambda, \nu) = f_0(x) + \sum_{i=1}^m \lambda_i f_i(x) + \sum_{i=1}^p \nu_i h_i(x)

\label{lagrangian}
\tag{2}
$$


Now it's as if we are telling the God of optimization that, you can go ahead and violate our constraints as much as you can, and we are going to introduce two new variables $\lambda$ and $\nu$ to help you do that. These two variables are called dual variables. $\lambda$ is referred as inequality constraint dual variable, and not surprisingly $\nu$ is the equality constraint dual variable. 