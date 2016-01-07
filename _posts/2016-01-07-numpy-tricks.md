---
published: true
title: Numpy Tricks
layout: post
---



Numpy Array overrides many operations, so deciphering them could be uneasy. Here are a collection of what I would consider tricky/handy moments from Numpy.

# Trick 1: Collection1 == Collection2

The `==` in Numpy, when applied to two collections mean element-wise comparison, and the returned result is an array. This trick can be neatly combined with other operations that take in array result.

```python
X_train, y_train, X_test, y_test = load_CIFAR10(cifar10_dir)
# Training labels (y_train) shape:  (50000,)
# to pick out correct y examples for one class (assuming that class is indexed at 5)
print(y_train == 5)
# array([False, False, False, ..., False, False, False], dtype=bool)
idxs = np.flatnonzero(y_train == 5)
```

`np.flatnonzero()` takes an array as input. Boolean array in Python has the property of `True` mapping to 1 and `False` mapping to 0, so `flatnonzero()` can easily pick out the index of those examples that are of class 5.
