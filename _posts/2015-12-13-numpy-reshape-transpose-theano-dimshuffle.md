---
published: true
title: "Understand Numpy Reshape, Transpose, and Theano Dimshuffle"
layout: post
---


When you work with Numpy, you work with multidimensional arrays (or tensors). I have to admit such concept was not too easy for me to grasp in the beginning, but after some thought, it became relatively easy. This post uses tensor/multidimensional array interchangeably.

for a tensor of shape `(4,3,2,2)` is a 4D tensor. Think of a 4D tensor as a tree, root connects with 4 leaves, and each leaf has 3 children, and each such children has 2 children, and each such children has 2 concrete elements: yes, the last number of the shape indicates the length of a list/array, such as `array([1., 2.])`