---
published: true
layout: post
title: A Geometric Interpretation of Lagrangian, Dual Problem, and KKT
---
## Preface

This is an article providing another perspective on understanding Lagrangian and dual problem. These two topics are essential to convex and non-convex optimization. Since it is a blog post, the proper background to understand this article is kept rather low. If you need to brush up on convex knowledge, or wish to be introduced to the subject, there is a handout from Stanford CS229 website, and it's written in a very concise and clear way: [Convex Optimization (I)](http://cs229.stanford.edu/section/cs229-cvxopt.pdf) and [Convex Optimization (II)](http://cs229.stanford.edu/section/cs229-cvxopt2.pdf). However, you don't need to understand convex sets, functions in order to follow this post. If you are like me, who took Stanford EE364A, then you might find this post more interesting. The post randomly jumps between Chapter 5 and Chapter 9, 10, and 11 ([Convex Optimization book by Stephen Boyd](http://stanford.edu/~boyd/cvxbook/bv_cvxbook.pdf )). This post is also inspired by an office hour conversation with [Nicholas Moehle](http://stanford.edu/~moehle/).



The post also diverges from the geometric interpretation from the book, which focuses on strong and weak duality of the dual problem. However, it's more important to explore the geometric property of the Lagrangian, since it gives rise to complementary slackness and KKT conditions. 

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


We define $f_i(x)​$ being convex functions, and $h_i(x)​$ being affine functions. If you are  familiar with convex optimization or other optimization problem, this formula should look familar. The constraints listed under `subject to` are considered **hard** constraints, and those constraints do not normally appear in traditonal machine learning cost functions. One way to distinguish them is that, cost functions in Machine Learning does not need to have a realistic meaning (objective can be an abstract measurement of progress), where in convex optimization, objective is something you want to find (like how much fuel the rocket will need).



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



So what do those two "implementation" details have to do with Lagrangian? The reason is simple: Lagrangian is simply a way, not unlike equality Newton step or barrier method, to incoroporate constraints into the overall objective. It turns the **hard** constraints into **soft** constraints. **Soft** constraints that you (or the optimization) can violate. 



Let's look back to equation $\ref{original}$: we can formulate its Lagrangian as: 


$$
L(x, \lambda, \nu) = f_0(x) + \sum_{i=1}^m \lambda_i f_i(x) + \sum_{i=1}^p \nu_i h_i(x)

\tag{2}
$$


Now it's as if we are telling the God of optimization that, you can go ahead and violate our constraints as much as you can, and we are going to introduce two new variables $\lambda$ and $\nu$ to help you do that. These two variables are called dual variables. $\lambda$ is referred as inequality constraint dual variable, and not surprisingly $\nu$ is the equality constraint dual variable. 



##  Dual Problem

After all these long and tedious definition of things (hopefully you aren't too bored with them), we get to one last bit of information: dual problem. We can form a dual problem from the Lagrangian. What is a dual problem? Dual problem, simply put, is like you looking into the mirror. It's you in the mirror, but the image is flipped. A deeper interpretation of dual problem is that it allows us to explore the effects of constraints on our original objective. Keep in mind that by forming Lagrangian, we allow the constraints to be soft, to add artibitrary large or small "penalty" to the original objective, but how would these constraints add penalization to the objective? dual variables provide the answer.



We first must get rid of the effect of $x$. Since we want to minimize the new objective (the Lagrangian), we can use the operator **inf**, it can give us the minimum value of the function over a chosen variable, and in here we choose $x$:


$$
g(\lambda, \nu) = \inf_{x \in D} L(x, \lambda, \nu) = \inf_{x \in D} \Big(f_0(x) + \sum_{i=1}^m \lambda_i f_i(x) + \sum_{i=1}^p \nu_i h_i(x) \Big)
$$


By doing this, we reduce the first term $f_0(x)$, $f_i(x)$, and $h_i(x)$ to constants, and the infimum over a family of affine functions is a concave function (go through chapter 2 of the convex book for more details on this). Thus our dual objective is concave. Then we can quite awesomely form the following dual problem:


$$
\begin{equation*}
\begin{aligned}
& \text{maximize}
& & g(\lambda, \nu) \\
& \text{subject to}
& & \lambda \succeq 0
\end{aligned}
\end{equation*}
$$

A nicer understanding of this is that: by fixing $x$ (we shall now call it **primal** variable out of no reason…it's actually to draw contrast to dual variables), we are driving the Lagrangian's value down (as well as $f_0(x)$'s value), however, we must be violating constraints! So now, maybe it's the time for the constraints to punish our objective, and we want the penalty to be as **high** as possible. Thus, by maximizing $g(\lambda, \nu)$, we can figure out a set of values for $\lambda$ and $\nu$ that will make the penalty as **large** as possible for the Lagrangian objective, hence the maximizing over $\lambda$ and $\nu$ on function $g$. 



## Geometric Analysis of Lagrangian

Now we can start our geometric interpretation of these dual variables. The geometric intuition is simple, yet intuitive. We should begin with the simplest example (as many explaination would start). You can easily extend them (mathematically, and mentally) to more complex problems.



We first assume the simplest convex optimization problem, and we start with one equality constraint. 



$$
\begin{equation*}
\begin{aligned}
& \text{minimize}
& & f(x) \\
& \text{subject to}
& & h(x) = 0
\end{aligned}
\end{equation*}
$$


We form the following Lagrangian: $L(x, \nu) = f(x) + \nu h(x)$. Notice the condition of optimality for the original problem is when $\nabla f(x) = 0$, and the optimality condition for the new Lagrangian is changed to: $\nabla f(x) + \nu \nabla h(x) = 0$, if we take derivative of x. 



The rise of KKT conditions and complementary slackness (two concepts that are often hard to grasp as first-timers) comes from the seemingly discordance between the original and Lagrangian optimality conditions. 