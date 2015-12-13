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
a = [1,2,3,4,5,6,7,8,9]
a[::3]
>> [1, 4, 7]
```

When using double colon to slice, the syntax looks like `a[x::y]`, which means you start at xth element, and get every yth element. Using this syntax, you can easily get odd and even positioned element from a Python list:

```python
>> a = [1,2,3,4,5,6,7,8,9]
>> even = a[::2]
>> odd = a[]
```




