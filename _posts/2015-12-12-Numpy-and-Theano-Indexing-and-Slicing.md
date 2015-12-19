---
published: true
title: Numpy and Theano Indexing and Slicing
layout: post
---








As some of you might know, I've been working on Numpy and Theano for a while. I can't claim any expertise, but have found many interesting facets and had to synthesize them together, leading to this post.

## Python Double Colon Slicing

The normal kind of slicing can be found here: [Numpy Tutorial from Stanford CS231N](http://cs231n.github.io/python-numpy-tutorial/). Another more detailed slicing can be found in this [StackOverflow answer](http://stackoverflow.com/questions/509211/explain-pythons-slice-notation).

In fact, except for the "single" colon slicing, you also have double colon slicing:

```python
>> a = [0,1,2,3,4,5,6,7,8,9]
>> a[::3]
[1, 4, 7]
```

When using double colon to slice, the syntax looks like `a[x::y]`, which means you start at xth element, and get every yth element. Using this syntax, you can easily get odd and even positioned element from a Python list:

```python
>> a = [0,1,2,3,4,5,6,7,8,9]
>> odd = a[1::2]
>> even = a[::2]  # because index starts at 0
```

It's also noting, when you are doing `a[::-1]`, this is slightly different from slicing. `[::-1]` reverses the array from the last element to first, and creates a copy of the original array, which is different from `a.reverse()`, since `.reverse()` does not have a return value.

## Numpy Array Slicing

Numpy in many ways tries to stay consistent with Python's original way of slicing, but with a few differences: reference pointer and axis. For the first difference, when slicing, in original Python, you always get a new array independent of the old one, but in Numpy, you always get a reference pointer that every element in your new array is a pointer to the old array's cell.

For the second difference, in numpy, you can combine the slicing (selecting) by comma such as `a[1,2]` for a 2D array. Such comma combination is not possible in normal python.

You can even do cool tricks such as `x[:, 1::2]`, which means let's select everything from the first (1st) axis, but only every other 2 elements starting at position 1 on axis 2 (what a mouthful right?).

When the majority of the tricks stay the same, numpy does have its own qurkiness (which I don't like). That is the reference pointer:

```python
import numpy as np
A = np.array([1,1,2,3,4], dtype = 'float')
B = A[::2]
>> A: array([ 1.,  1.,  2.,  3.,  4.])
>> B: array([ 1.,  2.,  4.])
```

If you do this, you are only passing in a reference pointer to B. If you modify array B in any way, elements in A will be changed:

```python
B += 1
>> A: array([ 2.,  1.,  3.,  3.,  5.])
>> B: array([ 2.,  3.,  5.])
```

This does give flexibility of easy assignment and alteration for A's elements, but this can give rise to bugs, and especially to people who don't realize this.

A safer way to operate is to copy A's value:

```python
B = A.copy
B[::2] += 1
```

Yes. Numpy is so convenient, that slicing and assignment can happen within the same line.

## Theano Tensor Slicing/Assigning

Theano is trying to be similar to Numpy, but has its major difference on the reference pointer and assignment. For a shared variable, we know we have `.get_value()` and `.set_value()`, so what about tensors?

But let's talk about slicing first, before diving into assignment:

```python
>> from theano import tensor as T
>> x = T.vector()
>> y = x[1::2]
>> y.eval({x: np.asarray([1,2,3,4])})
array([ 2.,  4.])
```

I have to say `.eval()` is an amazing function, and actually gives insight to a lot of the symbolic magic inside theano. What's worth notice is that `x` is not pointing to `y`'s elements in any way (different from Numpy), and any kind of modification on `x` will not affect `y` at all.

To modify `x`, we can use either `T.inc_subtensor()` or `T.set_subtensor()`.

```python
>> yc = T.inc_subtensor(x[1::2], 1)
>> yc.eval({x: np.asarray([1,2,3,4])})
array([ 1.,  3.,  3.,  5.])
```
Also it's worth noting that since Theano is a pure expression evaluation engine, what it does is to evaluate expressions, and every variable stores an expression (probably except the "shared variables"). This leads to the immutability of the values. Even though we used `T.inc_subtensor()` on `x`, `x` does not really exists. `x` is just a symbol, and the real calculation happens on the expression. So `T.inc_subtensor()`'s effect is only captured by `yc`, which stores this expression (action). If we evaluate `y` afterwards, you would not see the values increased.

```python
>> y.eval({x: np.asarray([1,2,3,4])})
array([ 2.,  4.])
```
## Advanced Multidimensional Indexing

Theano, like Numpy, supports a tuple-based indexing schema. For a Numpy array of shape (3,3,3), selecting the one element could be written as: `a[1,1,1]`, this way you get one element `1.0` returned to you. However, if you want to keep the structure (and dimensions), you can call `a[1:2,1:2,1:2]`, then you get `array([[[ 1.]]])` of shape `(1, 1, 1)` returned.

```python
>> a[1,1,1]
1.0
>> b = a[1:2,1:2,1:2]
>> b
array([[[ 1.]]])
>> b.shape
>> (1, 1, 1)
```

Sometimes, your array's shape is unknown, but you want to apply the same operation to all the dimensions, here is some Python magic kicking in. 

### \_\_getitem\_\_ Tuple
In order to use syntax like `a[...]`, a `__getitem__(self, key)` method is defined on the class. The `key` would correspond to a tuple value, which is created 


