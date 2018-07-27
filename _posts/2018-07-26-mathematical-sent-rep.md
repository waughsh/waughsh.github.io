---
published: true
layout: post
visible: 0
title: Mathematical Sentence Representation
draft: true
---
## Mathematical Sentence Representation

First let's flatten out sentences into concatenation of word vectors.

$s = [x_1, x_2, x_3, ...x_m]$ where $x_1 \in {\rm I\!R}^d$. We assume each sentence has a fixed length. Then we get $s \in {\rm I\!R}^{dm}$. 

The goal of a universal sentence encoder is to compute a weight matrix $W^*$, suppose $h_s = sW^*$, assume that $s_1, s_2, ..., s_n \sim P_s$, then, without concerning label $y_1, ..., y_n$, are we able to know how easily it is to seperate $h_s^1, h_s^2, ...,h_s^n$. 

Previous research has shown that it is obviously easier when $h \in {\rm I\!R}^d$ where $d$ is high (this dimension can be different from $x$). Does this also mean that $h$ is a compressed version of $s$, thus cannot achieve a better performance than $s$ itself?

So maybe an upper bound to the seperability of these points lie within the original space $s_1, s_2, ..., s_n$, which is obvious. We can measure dimension variability, and just pick **word vector dimensions that have the largest variance** across a large corpus.

But since obviously RNN/LSTM (non-linear dynamical system) is not concatenating word vectors and word vectors are not necessarily the best representation of a sentence, then what is the optimal condition we can hope to set for a good mapping? 