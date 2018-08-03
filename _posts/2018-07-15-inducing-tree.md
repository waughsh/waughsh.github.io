---
published: true
layout: post
visible: 0
title: Inducing Tree from LSTM
draft: true
---
## Aug 3. Reading List

1. 

## Inducing Compositional Structures from LSTM

Some observations

1. Later time steps are not processed as much. On 2nd time step, $x_1$ is processed 111 times, yet $x_2$ is processed only 6 times. $x_1$ and $x_2$ are composed together for 17 times. (This kind-of explained why bidirectional LSTM works so well -- because it would even out the amount of processing for all time steps)
2. The hadamard product brings complexity into the computation, forging non-linear paths that are different from Recursive NN. (Also since RNN does not have hadamard product, it would not construct an implicit tree)

Algorithm idea:

Deompose each hidden state and cell state into a linear combination of past sequence of tokens.

```
(x1)
(x1, x2, x1x2)
(x1, x2, x3, x1x2, x1x3, x2x3, x1x2x3) * W_1
```

[x1 x5]

Algorithm:

```python
prev_h = ['x1']
for t in range(2, 15+1):
    prev_h += ['x{}'.format(t)] + [e + 'x{}'.format(t) for e in prev_h]
```

In $Uh_{t-1}$ step, there is no $x_t$ entering into the mix, so previous components remain uninfluenced by the new input.

After mixing with different gates, we get:

```
(x1, x2, x3, x1x2, x1x3, x2x3, x1x2x3)
(x1, x2, x3, x1x2, x1x3, x2x3, x1x2x3)
```

So at time step 15, we have 32,767 combinations of the past sequence.

Steps:

1. Demonstrate that terms like [x1, x5] (non-adjacent composition) has low weights or low importance, so that we can safely ignore them or not (even if they have high importance, it's a finding, it's very interesting)
2. If we only consider adjacent composition, then the list is quite small
3. Decompose into: [relaxed_adjacent_terms] + non_adj_terms (one vector) + irev (biases); we can change the criteria to get non_adj_terms, and play with it [f (f(x1 x3) x4)] 
4.
