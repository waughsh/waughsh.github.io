---
published: true
title: A Collection of Numpy Tricks
layout: post
---













Numpy Array overrides many operations, so deciphering them could be uneasy. Here are a collection of what I would consider tricky/handy moments from Numpy.

# Trick 1: Collection1 == Collection2

The `==` in Numpy, when applied to two collections mean element-wise comparison, and the returned result is an array. This trick can be neatly combined with other operations that take in array result.

```python
>> X_train, y_train, X_test, y_test = load_CIFAR10(cifar10_dir)
# Training labels (y_train) shape:  (50000,)
# to pick out correct y examples for one class (assuming that class is indexed at 5)
>> print(y_train == 5)
# array([False, False, False, ..., False, False, False], dtype=bool)
>> idxs = np.flatnonzero(y_train == 5)
```

`np.flatnonzero()` takes an array as input. Boolean array in Python has the property of `True` mapping to 1 and `False` mapping to 0, so `flatnonzero()` can easily pick out the index of those examples that are of class 5.

# Trick 2: Vectorization/Loop-elimination with `np.tile()`

Sometimes when you want to add two matrices together, and have to manually align/broadcast them, you can use np.tile(). This copy the current matrix and broadcast into the shape you want. Keep the lowest dimension as 1 so your matrix/vector stay the same.

Any for-loop can be written in `np.tile()` and enjoy the advantage of vectorized operation:

```python
# for a matrix shape of (10,5), assume this is our training example
>> train = np.random.randn(10,5)
# for another matrix shape of (5,5), assume this is our training example
# 5 examples, each example has 5 data points
>> test = np.random.randn(5,5)
# We want to form a matrix of (5, 10), each data point on every row is the 
# difference between sum of test-data[j] and train-data[i].
>> train = np.sum(train, axis=1)
>> test = np.sum(test, axis=1)

# duplicate train data of shape (10,) 5 times
>> expanded_train = np.tile(train, [test.shape[0],1])
# (5,10)
>> expanded_test = np.tile(test, [train.shape[0],1])
# (10, 5)
>> expanded_test = expanded_test.T
# (5,10) -> transpose it so we can do subtraction
>> result = expanded_train - expanded_test
# (5,10)
```

`np.tile()` can also be used to expand arrays and allow element-wise multiplication/division/addision, however such functionality can be easily (or better) replaced by `np.expand_dims()` because `expand_dims()` will automatically figure out which dimension to expand on, and does not require a final transpose (which is often the case for `np.tile`).

# Trick 3: Using Array for Pair-wise Slicing

Numpy array's slicing often offers many pleasant surprises. This suprise comes from the context of SVM's max() hinge-loss vectorization. SVM's multi-class loss function requires wrong class scores to subtract correct class scores. When you dot product weight matrix and training matrix, you get a matrix shape of (num_train, num_classes). However, how do you get the score of correct classes out without looping (given y_labels of shape (num_train,))? At this situation, pair-wise selection could be helpful:

```python
>> X = np.random.randn(500, 3073)
>> num_train = X.shape[0]
>> y_label = np.random.randint(10, (500,))
>> W = np.random.randn(3073, 10)
>> scores = X.dot(W)
>> correct_scores = scores[range(num_train),y_label]
```

If a numpy array's `[]` operation has two arguments which both are arrays, then Numpy will run this pair-wise operations, like the example below:

```python
>> a = np.asarray([1,2,3])
>> b = np.asarray([1,2,3])
>> correct_scores = scores[a, b]
```

It will select the [1,1], [2,2], [3,3] value from scores matrix. Another way to reversely use this is to assign value. So if we want to clean out the correct_label's score for the loss function, we can:

```python
>> scores[range(X.shape[0]), y_label] = 0
```

With this, all the correct label's score are set to be 0, then when we sum them up, the correct_label will not affect the overall cost at all.

# Trick 4: Smart use of ':' to extract the right shape

Sometimes you encounter a 3-dim array that is of shape (N, T, D), while your function requires a shape of (N, D). At a time like this, `reshape()` will do more harm than good, so you are left with one simple solution:

```python
for t in xrange(T):
  x[:, t, :] = # ...
```

You can use it to extract values or assign values!

# Trick 5: Use Array as Slicing index

In previous posts, we already explored how Numpy array takes slicing of pairs (such as `x[range(x.shape[0]), y]`), however, Numpy can also take another array as slicing. Assume x is an index array of shape (N, T), each element index
of x is in the range 0 <= idx < V, and we want to convert such index array into array with real weights, from a weight matrix w of shape (V, D), we can simply do:

```python
N, T, V, D = 2, 4, 5, 3

x = np.asarray([[0, 3, 1, 2], [2, 1, 0, 3]])  # (N, T)
W = np.linspace(0, 1, num=V*D).reshape(V, D)  # (V, D)

# this is the only required line
out = W[x]  # (N, T, D)
```
Numpy uses the underlying value of x as index to extract values from W.

# Trick 6: Unfunc `at`

Numpy has a special group of "functions" called [unfunc](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.ufunc.at.html). Turns out numpy.add, numpy.subtract and so on belong to this special group of functions.

Numpy has predefined methods for those functions such as `at`! This function performs the operation (let it be `add` or `subtract`) on a specific location (or locations).

`unfunc.at()` takes 3 arguments, first one is the matrix you intend to modify, second argument is the indices of the matrix you want to modify, third argument is the value you want to change (depending on what the `unfunc` function is).

A simple example is:

```python
>>> a = np.array([1, 2, 3, 4])
>>> np.add.at(a, [0, 1, 2, 2], 1)
>>> print(a)
array([2, 3, 5, 4])
```

However, this example is too simple and almost useless. This kind of useless examples permeate the whole Numpy documentation. Let's look at a more advanced example: use this trick to update a word embedding matrix!

```python
# dout: Upstream gradients of shape (N, T, D)
# x: from example in Trick 5 (N, T), indices
# V: the length of total vocabulary
# D: weight matrix dimension
# task here is to build a dW to modify
# the original weight matrix

dW = np.zeros((V, D), dtype=dout.dtype)
np.add.at(dW, x, dout)
```
Notice that Numpy converts `x`, a matrix into individual indicies, and use it to assign values from dout to dW. `np.add.at()` flattened dout so the dimension becomes (N*T, D). Iterating through such flattened array is the same order as iterating through `x`, which is also of shape (N, T).


