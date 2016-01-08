---
published: true
title: "Understand Numpy Reshape, Transpose, and Theano Dimshuffle"
layout: post
---


When you work with Numpy, you work with multidimensional arrays (or tensors). I have to admit such concept was not too easy for me to grasp in the beginning, but after some delibration, it became relatively easy. This post uses the term  tensor/multidimensional array interchangeably.

for a tensor of shape `(4,3,2,4)` is a 4D tensor. Think of a 4D tensor as a tree, root connects with 4 leaves, and each leaf has 3 children, and each such children has 2 children, and each such children has 4 concrete elements: yes, the last number of the shape indicates the length of a list/array, such as `array([1., 2., 3., 4.])`.

So basically, you have 4 * 3 * 2 = 24 copies of `array([1., 2., 3., 4.])`. The axis in numpy, more intuitively, I think should be called "layer" (or level), corresponding to the tree analogy.

This type of representation does make many operations every simple. No one wants to use 3 layers of for-loop to operate on the base layer. Even though both Numpy and Theano have `broadcast`, operating on two arrays with different shapes would be difficult and sometimes can't act in the same way that we hope it would.

So various ways to effectively change the shape of arrays were developed. Before any further discussion, I want to point out that Theano's `dimshuffle` is functionally more convenient than Numpy, and `transpose` + `reshape` = `dimshuffle`.

Let's look at `reshape` first:

```python
numpy.reshape(a, newshape, order='C')
```

`a` is the array, and `newshape` can be an `int` or a tuple like `(3,2,5)`. When you are reshaping, the total number of elements can't be altered, as explained above. If you are too lazy to calculate the what the remaining of this tuple should look like, you can just put `-1`, and Numpy will calculate for you.

```python
>> rng = np.random.RandomState(234)
>> a = rng.randn(2,3,10)
[[[-0.19527893 -0.55857688  1.81404088 -0.96837131  0.24091401 -0.73500459
    0.2109724  -0.02775907 -0.3963966   0.59031683]
  [-0.50886026  0.4946645  -1.40719802 -1.11904803 -0.57989281  1.32724684
   -0.59643449 -1.23477173 -0.29232262 -0.1781048 ]
  [-0.77729182  0.12059733  0.04879302  0.0432387   0.36680183 -0.49865206
    1.98657646  1.49187182 -0.72115552 -0.13696597]]

 [[ 0.45780119 -0.21050629 -0.51760964 -0.10392791  1.3585057   2.47169207
   -0.2112615  -1.1344434  -1.094633    0.37917997]
  [-1.27183452 -0.45772402  0.28187467 -1.5637492  -0.9033226   0.32506839
    0.17751023 -0.91124188 -0.52497156  1.80199851]
  [ 1.07582354  1.34339255 -1.24913382  0.36885077 -0.42382145 -0.39417196
    1.28506918  1.40770487 -0.66003583 -0.93787805]]]
>> np.reshape(a, (3,5,-1)).shape
(3, 5, 4)
```


