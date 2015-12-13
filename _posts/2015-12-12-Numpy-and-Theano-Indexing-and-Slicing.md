---
published: true
title: Numpy and Theano Indexing and Slicing
layout: post
---




As some of you might know, I've been working on Numpy and Theano for a while. I can't claim any expertise, but have found many interesting facets and had to synthesize them together, leading to this post.

As always, you'll find a list of references (website links) at the bottom of this page.

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

## Numpy Array Slicing

Numpy in many ways tries to stay consistent with Python's original way of slicing, but with a few tricks: reference pointer and axis. So in numpy, you can combine the slicing (selecting) by comma such as `a[1,2]` for a 2D array. Such comma combination is not possible in normal python.

You can even do cool tricks such as `x[:, 1::2]`, which means let's select everything from the first (1st) axis, but only every other 2 elements starting at position 1 on axis 2 (what a mouthful right?).

When the majority of the tricks stay the same, numpy does have its own qurkiness (which I don't like), which is the value reference:

```python

```




