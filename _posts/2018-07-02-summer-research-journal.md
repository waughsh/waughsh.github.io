---
published: true
layout: post
visible: 0
title: Summer Research Journal
draft: true
---
## Project 1: Treating CSU/PP Corpra as Testbeds for Unsupervised NLP Methods

Rethinking about the problems from the DeepTag Project: comparing on the effectiveness of Unsupervised Learning Methods in NLP, applied to a clinical setting, and improve PP performance.

It is a **perfect test bed for a few unsupervised learning algorithms** in NLP and see which one actually works well in an industry setting.

1. **Unsupervised LM** in medical domain to improve downstream supervised learning tasks. (LM gives both word embedding and a sentence model) (Can fix LM or unfix during domain fitting)
2. **Word embedding** training only (fixed embedding) (on large corpora)
3. **Meta-learning** the LSTM optimizer, apply to see if it improves and make test domain shift smaller. (Also Chelsea Finn's MAML might also work).

## Project 2: Learning/Inducing latent structure from Sentence Embedding Models

Last year many sentence embedding models are proposed (ELMO, GenSent, InferSent, TransformerLM, DisSent, etc.). We can interpret the model's computational process and induce latent structures from them.

We have a sequence of tokens $(x_1, x_2, ..., x_T)$, the task is to find the correct order to combine the tokens, where LSTM/RNN assumes left-to-right processing, CNN assumes independence between time steps (each scan is uninfluenced by other patches). The relaxed version of both inductive biases is Attention-is-all model.

We can first show that [Attention-is-all](https://arxiv.org/abs/1706.03762) model is a continuous relaxation of a harder combinatorial discrete optimization problem.

### Inducing the structure

1. For CNN (can trace how representation gets selected -- receptive field analysis)
2. For LSTM (using interpretation scheme from Contextual decomposition)
3. For Attention-is-all (just look at attention weights)

All of these share a small problem that is the induced structures might not be a tree. Tree requires children to have exclusive parents. We can further put constraints to prune this graph and mold into a tree.

Build recursive neural network using induced structures, learn new weight matrices, and see if it outperforms **constituency tree recursive neural network** and the **SPINN model**.

As qualitative analysis, can also look at induced patterns from InferSent, DisSent, compare them to SPINN model patterns, and see how much they overlap, whether induced patterns are more consistent (vs. SPINN model), and whether InferSent and DisSent (or other models) generate different induced patterns.

Contextual decomposition requires label to be present.

### Learning the structure

Meta-learning that tweaks architecture sometimes involves a `controller`. SPINN model's shift-reduce operation can be regarded as a controller that decides how to assemble a sentence. 

Then the [DART](https://arxiv.org/abs/1806.09055) paper proposed an iterative algorithm for a controller-free optimizing weights and model selection jointly with a relaxed softmax version of connection schemes. 

Or technically Attention-is-all model can be regarded as having implicit controllers? The controller is the one of the $W$ matrices that generate keys. Then we won't have a train/val/test problem. 

Gumbel-softmax [paper](https://arxiv.org/pdf/1707.02786.pdf) uses a query vector $q$ to compute how the tree should be combined (by testing for consecutive pairs). In fact, in printing out the stack trace of LSTM, we can see that the sequence tokens get combined in much more complex ways than recursive neural network can handle. Can propose a model where not only a combination is proposed, but also different/multiple operations are proposed.

Roadmap (TODO list)
1. Run SPINN model on SST
2. Find a good Attention model
3. 

## Project 3: Interpretability

We can extend the [first Murdoch](https://arxiv.org/pdf/1702.02540.pdf) paper that deals the RNN hidden states using the telescoping sum.

The trick is to decompose $h_T$ into: $h_T = h_0 + h_2 - h_1 + ... + h_T - h_{T-1}$, then we still have $T$ difference vectors and we can compute how important they are by multiplying them with the label embedding vector.

For global max pooling, what we can do is that for a vector of dimension $d$, we can find $d$ trajectories that makes up $d$. Assume for a dimension $i$, where it picks from time step $j$, then we can decompose $h_s^i = h_0 + h_2 - h_1 +...+h_j - h_{j-1}$. This gives us $d$ unique trajectories (because dimensions are different), and that can be interpreted as semantic superposition (the trajectory is the same, but dimension is different). Then we have a much more complex way of interpreting the sentence (compared to a simple sum of difference) when max pooling is used, which might explain why global max pooling is better?

For attention, we can conduct a similar analysis, but it wouldn't yield any more insights.

## Project 4: Interactive Natural Language Processing

At least it will allow people to write conditional arguments to shape classifier's behavior, but that's the power of filter (parser)...(regex-lite) (it's irrelevant to underlying system). 

The difference is that this system allow: 
1. Delete (modify) patterns/extracts that are wrong
2. Add new patterns (already doable) 
3. Retraining the model (LSTM or anything) using human-modified patterns as regularization signals (penalize gating function or attention) (Need to extend Murdoch's algorithm to cover attention)
   - Similar to SQuAD, which only asks the model to pick out words (an attention assignments over the paragraph), human-added keywords can serve similar functionality -- predicting the highlight mask can be a semi-supervised task for the training of model.

## Other Ideas

1. NLP for long documents (DeepTag project reveals most EHR documents are long. Most NLP methods are for short sentences) (Long documents can be treated as multi-sentence processing) (Jonathan Berant recently released this paper
2. Coming up with effective regularization schemes for Attention-is-all model (adding entropy regularization to Attention) (eliminate the learning rate warmup schedule -- it's unintuitive, and the reason is probably because of early commit) (Need to find the right dataset)
  - Attention-is-all model is inherently bad for classification (MR, CR, etc.) (LSTM is surprisingly good at those tasks) (Can plot out the hidden sentence representation evolution of both models and see how they change)
  - Propose a more effective Attention-is-all Classification model.
3. Tree-LSTM can be a more generalized form of [Gumble-Tree](https://arxiv.org/pdf/1707.02786.pdf) (instead of same weight matrix, maybe introduce more options)
