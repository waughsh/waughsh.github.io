---
published: false
title: A Composite of Theano Tricks
layout: post
---




This post will be continuously updated.

# Trick 1: Always try a list!

It is probably a dynamic programming's convention, a theano built-in function's parameter normally can take a scalar, a list, or an array of any shape.

Just like Numpy's binomial method that takes a list:

```python
>> np.random.binomial(5, p=[(0.5, 0.5, 0.5)]*3, size=(3,3,3))
array([[[3, 3, 4],
        [3, 2, 3],
        [4, 3, 4]],

       [[1, 4, 3],
        [3, 1, 3],
        [4, 1, 2]],

       [[2, 3, 2],
        [3, 4, 5],
        [2, 1, 3]]])
```

Theano can do very similar trick:

```python
>> from theano.sandbox.rng_mrg import MRG_RandomStreams
>> p = shared(np.array(range(11)) / 10.)
>> rng_factory = MRG_RandomStreams(42)
>> rng_factory.binomial(p=p, size=p.shape, n=1)
array([ 0.,  0.,  0.,  1.,  0.,  1.,  0.,  1.,  1.,  0.,  1.])
```
