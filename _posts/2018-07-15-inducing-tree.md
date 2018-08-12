---
published: true
layout: post
visible: 0
title: Inducing Trees from LSTM
draft: true
---
## August 4

Todo:

1. Try to run ACD code on Jupyter Notebook

## August 3

Task: Trying to find a high-level reasoning on why you think this is important.

The goal is to capture high-level idea, why is this task is interesting and important, and how to solve it in a satisfactory way to linguists.

Reading list:

1. Chris Potts' new commentary [link](http://web.stanford.edu/~cgpotts/temp/pater-commentary-by-potts.pdf)
2. Jacob Andreas' blog post  [link](http://blog.jacobandreas.net/meaning-belief.html)
3. Sam Bowman's paper [link](https://arxiv.org/pdf/1709.01121.pdf)

Todo:

1. You need to port SPINN to evaluate on SentEval dataset (look at how Bowman's other paper uses SPINN to generate trees, replicate that!!)

#### Chris Potts' commentary: A case for deep learning in semantics

He proposes a sketching of what a DL-based semantics would be like, and advocates that semanticists need to engage and help shape DL people's agenda.

He believes that the tree structures we are assuming are not correct that they get in the way. Data-driven techniques can help us discover the right trees.

He gave a case in Socher et al. (2013) where they analyzed "A but B condeds that A and argues that B", and in a learned DL model, the model consistently predicts that the sentiment of "A but B" is largely determined by the sentiment of B. However, looking at the paper, first, they trained on phrase label to begin with, thus it's a guided result, not a natural discovery. Second, in 131 cases, RNTN only obtained 41%  of the cases correct. 

Potts wrote "a learned DL model to support a nuanced generalization about meaning".

Logics ("formal systems") are defined independently of particular users or instances of use.  It seperates semantics from all aspects of learning and cognitive representation, and it discourages work on items that are tied to interactive language use: disfluencies, swears, honorifics, interjections, etc.

A question on whether any utterance has a unique semantic interpretation. The question of resolution of multiple possibilities. Potts believes that many recent DL theories involve simultaneously representing multiple possible architectures and use attention mechanism.

(Can consider a sampling-based attention mechanism? Is it Gumbel-Tree?)

Potts mentions that compositionality is a methodology on how to proceed, while generalization is something we can measure. 

DL theories discourage firm boudnaries between semantics and pragmatics.

A problem in DL theories of semantics is that it fails to capture functional vocabulary. Every meaning, whether lexical or phrasal, is a vector, it's monotyped. However, **quantificational determiner** meanings are more complex than noun meanings. So any theory that puts them in the same meaning space is unlikely to do justice to determiners. This has a deep consequence the success of a model.

#### Sam Bowman's paper 

High level ideas: investigate whether RL-based SPINN model learns syntax or not (whether it systematically parses or not)

He regards SPINN or Gumbel-ST as "Latent Tree Learning" model. We can argue that LSTM is approximating latent tree learning, and use interpretation methods to extract such tree.

Four points: 

1). Only one of these models outperform conventional tree-structured models on sentence classification

 - The LSTM result in Table 1 is very effective. Contradicts the original SPINN paper result. "Suggests there is little value in using the correct syntactic structure".
 - RL-SPINN matches SPINN model (with trained correct parses)

2). Its parsing strategies are not especially consistent across random restarts (obviously, due to RL constraints) (DL interpretation approah will be much better)

- Table 2 provides this result

3). The parse is shallower than PTB parses.

4). The parse do not resemble those of PTB

- Section 6. Table 3. SPINN/SPINN-NC are trained with PTB parses so the F1 is high with PTB. Howeer, ST-Gumbel and RL-SPINN are not similar to PTB, and below random trees.

Observations:

1. They use Leaf LSTM/GRU, which provides local "context" information. Meaning the parsing is done not on words, but on hidden states / outputs of a RNN already. :rage: :rage:

Code Paper link:

1. TreeLSTM = SPINN-PI-NT (uses gold parse for both train and test); SPINN-NC is equivalent to TreeLSTM but with an additional prediction function, so not exactly the same
2. SPINN-NC: `use_tracking_in_composition=False` (No connection from tracking to composition)
3. SPINN-PI-NT: `tracking_lstm_hidden_dim=None` (set to None to avoid using tracker)
4. Use this [script](https://github.com/nyu-mll/spinn/blob/master/python/spinn/models/base.py) to check various CLI flags



## Project outline

Overall goal: whether LSTM or Bidirectional LSTM have **latent tree representations**, and whether these representations are **more computationally efficient** than trees produced by PCFG parsing.

Strategy: Use CD on LSTM to build trees. Have a few strategies for tree building. 

We have various tree generation algorithms:

SPINN, SPINN-NC, RL-SPINN, Gumbel-ST (Bowman 2018)

We have vanilla algorithms:

LSTM, BLSTM. (Optional: Transformer, CNN)

We also have various sentence embedding models:

InferSent, DisSent, [GenSen](https://github.com/Maluuba/gensen) (3 major sentence-based, LSTM/GRU based encoding algorithm)

ELMo, TransformerLM (language-model based)

Tree inducing algorithm:

ACD (agglomerative contextual decomposition) (greedy)

(other etc.)

Experiments:

1. Do LSTM/BLSTM learn consistent trees? (RL-SPINN, SPINN do not) (Table 2) (Gumbel-ST does agree with self a lot) (Measure by Self-F1)
2. Sentence classification performance
   - When inducing from sentence embedding models, do recursive neural network (SPINN-PI-NT) outperform vanilla trained LSTM? 
   - Is sentence embedding model the ceiling of recursive neural network?
   - Curcial: Does recursive NN trained from BiLSTM grammar outperform consistuency parsing grammar (Stanford CoreNLP) (Bowman's result showing training on CoreNLP grammar = training on learned grammar) (we can show improvements)
3. Does the grammar match SST, SNLI, MultiNLI, and then maybe PTB? (Table 4)
4. Qualitative studies on these grammars

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
