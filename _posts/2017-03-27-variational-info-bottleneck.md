---
published: false
layout: post
title: Information Bottleneck and Neural Network Regularization
visible: 1
---

Through information bottleneck method, we know the goal of a learning system is to minimize $I(X;T)$ to encourage maximal compression which can be narrowly interpreted as feature selection, or the gathering of the minimum sufficient statistics: $Y - X - S(X) - T(X)$[^1] so that $I(X; Y) = I(S(X); Y) = I(T(X); Y)$. The other objective of an optimal learning system, according to Tishby, is to encourage the extracted reprerensetation to share its statistical distribution to the label space $Y$ by maximizing $I(Y; T)$. 


$$
\begin{equation*}
\begin{aligned}
& \text{minimize}
& & I(X;T) \\
& \text{maximize}
& & I(Y;T) \\
\end{aligned}
\end{equation*}
\tag{1}
\label{original}
$$

We can reformulate to allow this to have a soft objective as:


$$
\begin{equation*}
\begin{aligned}
\min\limits_{p(t|x), p(y|t), p(t)}
\{I(X;T) - \beta I(T;Y)\}  \\
\end{aligned}
\end{equation*}
\tag{2}
\label{info_bottleneck}
$$
We can apply a factor $\alpha$ to $I(X;T)$ if we want to control the rate of compression. IB principle assumes markov chain to be $Y - X - \hat{X}$. We can extend this to multilayer neural network $Y - X - h_1 - h_2 - … - h_n - \hat{X}$. And formulate our IB training objective as $I(X; h_1, …, h_n) - \beta I(Y; h_1, …, h_n)$. However, with simple deduction we can show that:


$$
\begin{equation*}
\begin{aligned}
I(h_1, h_2 ; X) &= H(X) - H(h_1, h_2)  \\
 &= H(X) - H(h_2|h_1) - H(h_1) \\
 & = H(X) - H(h_1) \text{   with } X - h_1 - h_2 \\
 & =  I(X; h_1)
\end{aligned}
\end{equation*}
\tag{1}
\label{mutual_info}
$$


This means we can only treat the output of a neural network as a random variable and we do not need intermediate stochastic representation if our algorithm is optimal. In fact, Tishby[^2] has shown that under an theooptimal algorithm:  The Blahut–Arimoto algorithm, the problem can be solved exactly. 



However, Blahut-Arimoto algorithm cannot be computed exactly under integration, and we have to approximate both $I(X; T)$ and $I(Y; T)$. Preliminary studies have shown a simple way to approximate an upper bound of $I(X; T) - \beta I(T; Y)$ by using the non-negativity of mutual information for continuous random variables[^3]. Alemi, et al has shown that in order to apply this principle to supervised learning, beyond rate distortion theory or compression, we want to be lenient to the rate of compression but be demanding on the mutual information between hidden representation and label, thus recasting the equation to be $\beta I(X;T) - I(T; Y)$. 



With an understanding that the bound can be arbitrarily loose, and with monte carlo sampling, Alemi et al derived that $I(X; T) \leq \mathrm{KL}[p(T|X), r(T)]$, with $r$ being an arbitrary distribution: spherical gaussian $\mathcal{N} \sim (0, I)$. And $-I(Y;T) \leq \mathbb{E}_{\epsilon \sim p(\epsilon)}[-\log q(Y | f(X, \epsilon))]$ where $q(Y|f(X, \epsilon))$ is our model. 



With demosntration from Shwartz-Ziv, neural networks with deeper layers have a better ability to compress and extract information, thus following their way of measurement, we can assume $Y - X - T_i$ for each layer to define $I(Y; T_i)$  and assume $Y - T_{i-1} - T_{i}$ to define compression terms such as $I(X; T_1)$ and $I(X; T_1, T_2, …, T_n)$. Thus we are able to reformulate the cost for a multi-layer stochastic neural network as:


$$
L_{MIB} = [\sum_{i=1}^n I(Y; T_i)] + I(X;T_1) + \sum_{i=2}^n I(T_{i-1}; T_i)
$$


This is equivalent to create sub-networks and enforce the $L_2$ norm of each hidden representations to be small (due to the KL divergence between layers and a spherical gaussian distribution). 



![](http://anie.me/images/mutual_y_h.png)



This is similar to the style of stochastic depth network, but in a more principled way. We encourage each layer to maximally compress $X$ that will maximize the chance of predicting the correct label. This is quite different form the architecture of DenseNet[^4].



[^1]: Opening the blackbox of Neural Network via Information https://arxiv.org/pdf/1703.00810.pdf
[^2]: The information bottleneck method https://arxiv.org/pdf/physics/0004057.pdf
[^3]: Deep Variational Information Bottleneck https://arxiv.org/pdf/1612.00410.pdf
[^4]: Densely Connected Convolutional Networks https://arxiv.org/abs/1608.06993
