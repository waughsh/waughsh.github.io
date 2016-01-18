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
