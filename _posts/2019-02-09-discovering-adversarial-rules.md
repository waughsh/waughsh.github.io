---
published: true
layout: post
draft: true
visible: 0
title: Reading Group Blog -- Semantically Equivalent Adversarial Rules for Debugging NLP Models (ACL 2018)
---
Allen Nie 02/12/2019

In the second post, we will focus on this paper:

> Ribeiro, Marco Tulio, Sameer Singh, and Carlos Guestrin. "[Semantically equivalent adversarial rules for debugging nlp models](http://aclweb.org/anthology/P18-1079)." *Proceedings of the 56th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)*. Vol. 1. 2018.

Robustness is a central concern in engineering. We want our suspension bridges to stand against strong wind and not repeat the collapse of Tacoma Narrows Bridge [[video](https://commons.wikimedia.org/w/index.php?title=File%3ATacoma_Narrows_Bridge_destruction.ogv)]. We want our nuclear reactors to always be fault tolerant so that Fukushima Daiichi incident will never happen again [[link](https://en.wikipedia.org/wiki/Fukushima_Daiichi_nuclear_disaster)].

When we increasingly become reliant on a type of technology -- suspension bridges, nuclear power, or in this case: NLP models, we must raise the level of trust we can have in this technology. Robustness is precisely the requirement we need to place on such systems.

<p style="text-align: center"><img src="https://upload.wikimedia.org/wikipedia/en/2/2e/Image-Tacoma_Narrows_Bridge1.gif" style="width:50%"></p>

Early work from Jia & Liang (2017)[^1] shows that NLP models are not immune to small negligible-by-human perturbation in text -- a simple addition or deletion can break the model and force it to produce nonsensical answers. Other work such as Belinkov & Bisk [^2], Ebrahimi et al.[^3] showed a systematic perturbation that is to simply drop or replace a character is sufficient to break a model. Introducing noise to sequence data is not always bad: ealier work done by Xie et al.[^4] shows that training machine translation or language model with word/character-level perturbation (noising) actually improves performance.

However, it is hard to call these perturbed examples "adversarial examples" in the original conception of Ian Goodfellow[^5].  This paper proposed two definitions of adversarial examples in text:

**Semantic equivalence** of two sentences: $\text{SemEq}(x, x')​$

**Perturbed label prediction**:  $f(x) \not= f(x')$

In our discussion, John Hewitt [[blog](https://nlp.stanford.edu/~johnhew/)] points out that from a linguistic point of view, it is very difficult to define "semantic equivalence" because we don't have a precise and absolute definition of "meaning". This is to say that two sentences might generate the same effect for a particular task, but does not need to be synonymous. A more nuanced discussion of paraphrases in English can be found in *What Is a Paraphrase?* [[link](https://www.mitpressjournals.org/doi/pdf/10.1162/COLI_a_00166)] by Bhagat & Hovy (2012). In this paper, semantic equivalence is operationalized as what humans (MTurkers)  judged to be equivalent. 

Ribeiro et al. argues that only a sequence that satisfies both conditions is a true adversarial example in text.  They translate this criteria into a conjunctive form using an indicator function:

$$
\text{SEA}(x, x') = \unicode{x1D7D9}[\text{SemEq}(x, x') \wedge f(x) \not= f(x')]
$$

In this paper, semantic equivalence is measured by paraphrasing likelihood, defined in multilingual multipivot paraphrasing paper from Lapata et al. (2017)[^6]. Pivoting is a technique in statistical machine translation proposed by Bannard and Callison-Burch (2005)[^7]: if two English strings $e_1$ and $e_2$ can be translated into the same French string $f$, then they can be assumed to have the same meaning.

<p style="text-align: center"> <img src="https://github.com/windweller/windweller.github.io/blob/master/images/pivot-gen.png?raw=true" style="width: 25%"> <img src="https://github.com/windweller/windweller.github.io/blob/master/images/multipivot-gen.png?raw=true" style="width: 25%"> </p>

The pivot scheme is depicted by the generative model on the left, which assumes conditional independence between $e_1$ and $e_2$ given $f$: $p(e_2 \vert e_1, f) = p(e_2 \vert f)$ . Multipivot is depicted by the model on the right: it translates one English sentence into multiple French sentences.



[^1]: Jia, Robin, and Percy Liang. "Adversarial examples for evaluating reading comprehension systems." *arXiv preprint arXiv:1707.07328* (2017). 
[^2]:  Belinkov, Yonatan, and Yonatan Bisk. "Synthetic and natural noise both break neural machine translation." *arXiv preprint arXiv:1711.02173*(2017). 
[^3]:Ebrahimi, Javid, et al. "HotFlip: White-Box Adversarial Examples for Text Classification." *arXiv preprint arXiv:1712.06751* (2017). 
[^4]: Xie, Ziang, et al. "Data noising as smoothing in neural network language models." *arXiv preprint arXiv:1703.02573* (2017). 
[^5]:Goodfellow, Ian J., Jonathon Shlens, and Christian Szegedy. "Explaining and harnessing adversarial examples (2014)." *arXiv preprint arXiv:1412.6572*.
[^6]:Mallinson, Jonathan, Rico Sennrich, and Mirella Lapata. "Paraphrasing revisited with neural machine translation." *Proceedings of the 15th Conference of the European Chapter of the Association for Computational Linguistics: Volume 1, Long Papers*. Vol. 1. 2017.
[^7]:Colin Bannard and Chris Callison-Burch. 2005. Paraphrasing with bilingual parallel corpora. In Proceedings of the 43rd Annual Meeting of the Association for Computational Linguistics, pages 597–604, Ann Arbor, Michigan.







