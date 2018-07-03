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
3. **Meta-learning** the LSTM optimizer, apply to see if it improves and make test domain shift smaller.

## Project 2: Learning/Inducing latent structure from Sentence Embedding Models

Last year many sentence embedding models are proposed (ELMO, GenSent, InferSent, TransformerLM, DisSent, etc.). We can interpret the model's computational process and induce latent structures from them.

We have a sequence of tokens $(x_1, x_2, ..., x_T)$, the task is to find the correct order to combine the tokens, where LSTM/RNN assumes left-to-right processing, CNN assumes independence between time steps (each scan is uninfluenced by other patches). The relaxed version of both inductive biases is Attention-is-all model.


## Project 3: Interactive Natural Language Processing

At least it will allow people to write conditional arguments to shape classifier's behavior, but that's the power of filter (parser)...(regex-lite) (it's irrelevant to underlying system). 

The difference is that this system allow: 
1. Delete (modify) patterns/extracts that are wrong
2. Add new patterns (already doable) 
3. Retraining the model (LSTM or anything) using human-modified patterns as regularization signals (penalize gating function or attention) (Need to extend Murdoch's algorithm to cover attention)

