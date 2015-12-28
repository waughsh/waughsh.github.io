---
published: false
title: "Understand Numpy Reshape, Transpose, and Theano Dimshuffle"
layout: post
---





When you work with Numpy, you work with multidimensional arrays (or tensors). I have to admit such concept was not too easy for me to grasp in the beginning, but after some delibration, it became relatively easy. This post uses the term  tensor/multidimensional array interchangeably.

for a tensor of shape `(4,3,2,4)` is a 4D tensor. Think of a 4D tensor as a tree, root connects with 4 leaves, and each leaf has 3 children, and each such children has 2 children, and each such children has 4 concrete elements: yes, the last number of the shape indicates the length of a list/array, such as `array([1., 2., 3., 4.])`.

So basically, you have 4 * 3 * 2 = 24 copies of `array([1., 2., 3., 4.])`. The axis in numpy, more intuitively, I think should be called "layer" (or level), corresponding to the tree analogy.

This type of representation does make many operations every simple. No one wants to use 3 layers of for-loop to operate on the base layer. Even though both Numpy and Theano have `broadcast`, operating on two arrays with different shapes would be difficult and sometimes can't act in the same way that we hope it would.

So various ways to effectively change the shape of arrays were developed. Before any further discussion, I want to point out that Theano's `dimshuffle` is functionally more convenient than Numpy, and `transpose` + `reshape` = `dimshuffle`.
