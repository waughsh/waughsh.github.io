---
published: true
title: A Tutorial on Torchtext
layout: post
---
About 2-3 months ago, I encountered this library: [Torchtext](https://github.com/pytorch/text). I nonchalantly scanned through the README file and realize I have no idea how to use it or what kind of problem is it solving. I moved on.



Last week, there was a paper deadline, and I was tasked to build a multiclass text classifier at the same time. I was slightly overwhelmed. I have started using PyTorch on and off during the summer. So I decided to give **Torchtext** another chance. 



Turns out, it is possible to use this library within 3 hours if you are willing to dig into the codebase and not afraid to use code analysis tools like PyCharm. Besides a slightly outdated and unfinished "tutorial" I can find on Google, there's no other tutorial or explanatory documentation for this library.



This means it's a perfect opportunity to write a blog post :) that will save many people the trouble of going through the source code!


## Basics


**Torchtext** is a very powerful library that solves the preprocessing of text very well, but we need to know what it can and can't do, and understand how each API is mapped to our inherent understanding of what should be done. An additional perk is that **Torchtext** is designed in a way that it does not just work with PyTorch, but with any deep learning library (for example: Tensorflow). 



Let's compile a list of tasks that text preprocessing must be able to handle. All checked boxes are functionalities provided by **Torchtext**.



* [ ] **Train/Val/Test Split**: seperate your data into a fixed train/val/test set (not used for k-fold validation)
* [x] **File Loading**: load in the corpus from various formats 
* [x] **Tokenization**: break sentences into list of words
* [x] **Vocab**: generate a vocabulary list
* [x] **Numericalize/Indexify**: Map words into integer numbers for the entire corpus
* [x] **Word Vector**: either initialize vocabulary randomly or load in from a pretrained embedding, this embedding must be "trimmed", meaning we only store words in our vocabulary into memory.
* [x] **Batching**: generate batches of training sample (padding is normally happening here)
* [ ] **Embedding Lookup**: map each sentence (which contains word indices) to fixed dimension word vectors





From a more visual explanation, what we are doing is this process:



```
"The quick fox jumped over a lazy dog."
-> (tokenization)
["The", "quick", "fox", "jumped", "over", "a", "lazy", "dog", "."]

-> (vocab)
{"The" -> 0, 
"quick"-> 1, 
"fox" -> 2,
...}

-> (numericalize)
[0, 1, 2, ...]

-> (embedding lookup)
[
  [0.3, 0.2, 0.5],
  [0.6, 0., 0.1],
  ...
]
```

 

This process is actually quite easy to mess up, especially for tokenization. Researchers would spend a lot time writing custom code for this, and in Tensorflow (not Keras), this process is excruiating because you would create some **preprocessing** script that handles everything before the **Batching** step, which was the recommended way in Tensorflow.



Torchtext standardizes this procedure and makes it very easy for people to use. Let's assume we have split our text corpus into three tsv files (I tried to use the JSON loader but I was not able to figure out the format specifications): `train.tsv`, `val.tsv`, `test.tsv`.



We first define a `Field`, this is a class that contains information on how you want the data preprocessed. It acts like an instruction manual that `data.TabularDataset` will use. We define two fields:



```python
import spacy
spacy_en = spacy.load('en')

def tokenizer(text): # create a tokenizer function
    return [tok.text for tok in spacy_en.tokenizer(text)]

TEXT = data.Field(sequential=True, tokenize=tokenizer, lower=True, fix_length=150)
LABEL = data.Field(sequential=False, use_vocab=False)
```



I have preprocessed the label to be numerical integers, thus I used `use_vocab=False`. Torchtext probably allows you use to text as labels, but I have not used it yet. As you can see, we can fix the length of input as well as map all words to lowercase.



Then we load in all our corpuses at once using this great class method `splits`:



```python
train, val, test = data.TabularDataset.splits(
        path='./data/', train='train.tsv',
        validation='val.tsv', test='test.tsv', format='tsv',
        fields=[('Text', TEXT), ('Label', LABEL)])
```

 

This is quite straightforward, in `fields`, the amusing part is that `tsv` file parsing is order-based, in my raw `tsv` file, I do not have any header, and this script seems to run just fine, but I would imagine if there is a header, then the first element `Text` should match the column header.



Then we load in pretrained word embeddings:



```python
TEXT.build_vocab(train, vectors="glove.6B.100d")
```



Note you can directly pass in a string and it will download pre-trained word vectors and load them for you. You can also use your own vectors by using this class `vocab.Vectors`. The downloaded word embeddings will stay at `./.vector_cache` folder. I have not yet discovered a way to specify a custom location to store the downloaded vectors (there should be a way right?).



Next we can start the **batching** process, where we use Torchtext's function to build an iterator for our training, validation and testing data split. This is also made into an easy step:



```python
train_iter, val_iter, test_iter = data.Iterator.splits(
        (train, val, test), sort_key=lambda x: len(x.Text),
        batch_sizes=(32, 256, 256), device=-1)
```



Note that if you are runing on CPU, you must set `device` to be `-1`, otherwise you can leave it to `0` for GPU. Note that you can easily examine the result, and realize that Torchtext already uses dynamic padding, meaning that the padded length of your batch is dependent on the longest sequence in your batch. This is a no-brainer must-do, but I've seen quite a few github repos/tutorials fail to cover.



TorchText `Iterator` is different from a normal Python iterator. They accept several keywords which we will walk through in the later advanced section. Keep in mind that each batch is of type ``torch.LongTensor``, they are the **numericalized** batch, but there is no loading of the pretrained vectors.



Don't worry. This step is still very easy to handle. We can obtain the `Vocab` object easily from the `Field` (there is a reason why each Field has its own `Vocab` class, because of some pecularities of Seq2Seq model like Machine Translation, but I won't get into it right now.). We use PyTorch's nice Embedding Layer to solve our embedding lookup problem:



```python
vocab = TEXT.vocab
self.embed = nn.Embedding(len(vocab), emb_dim)
self.embed.weight.data.copy_(vocab.vectors)
```



When we call `len(vocab)`, we are getting the total vocabulary size, and then we just copy over pretrained word vectors by calling `vocab.vectors`! It would normally take me half a day to write a preprocessing script that handles all of these, but with **Torchtext**, I was able to finish the whole classifier in 1 hour. 

## Reversible Tokenization

In quite many situations, you would want to examine your output, and try to interpret your model's actions. Then you need to map a batch of your serialized tokens like `[[0, 5, 15, ...], [152, 0, 50, ...] ]` back to strings! Typically this is difficult to do, because standard tokenization tools like Spacy or NLTK only tokenize but do not map back for you. Even though mapping these batches would not be too difficult, Torchtext has an easy solution for it: RevTok.

RevTok is a simple Python tokenizer, which (may) run relatively slower compared to industry-ready implementations like Spacy, but it's integrated into TorchText. You can download the source code from: https://github.com/jekbradbury/revtok and install as follows:

```
cd revtok/
python setup.py install
```

You cannot install revtok from `pip install revtok` because it is not the latest version of revtok (at the time this article is written), and it will break.

Once you installed revtok, you can easily use it in TorchText using:

```python
TEXT = data.ReversibleField(sequential=True, lower=True, include_lengths=True)
```

When you need to revert back to your string tokens from numericalized tokens, you can:

```python
    for data in valid_iter:
        (x, x_lengths), y = data.Text, data.Description
        orig_text = TEXT.reverse(x.data)
```

Here's a bit of juicy / advanced material for you, `ReversibleField` can only be used jointly with revtok. This is perhaps a bug in Torchtext. If you believe your discrete data only needs simple tokenization or normal splitting, then you can build your own customized Reversible Field like the following:

```python
from torchtext.data import Field
class SplitReversibleField(Field):

    def __init__(self, **kwargs):
        if kwargs.get('tokenize') is list:
            self.use_revtok = False
        else:
            self.use_revtok = True
        if kwargs.get('tokenize') not in ('revtok', 'subword', list):
            kwargs['tokenize'] = 'revtok'
        if 'unk_token' not in kwargs:
            kwargs['unk_token'] = ' UNK '
        super(SplitReversibleField, self).__init__(**kwargs)

    def reverse(self, batch):
        if self.use_revtok:
            try:
                import revtok
            except ImportError:
                print("Please install revtok.")
                raise
        if not self.batch_first:
            batch = batch.t()
        with torch.cuda.device_of(batch):
            batch = batch.tolist()
        batch = [[self.vocab.itos[ind] for ind in ex] for ex in batch]  # denumericalize

        def trim(s, t):
            sentence = []
            for w in s:
                if w == t:
                    break
                sentence.append(w)
            return sentence

        batch = [trim(ex, self.eos_token) for ex in batch]  # trim past frst eos

        def filter_special(tok):
            return tok not in (self.init_token, self.pad_token)

        batch = [filter(filter_special, ex) for ex in batch]
        if self.use_revtok:
            return [revtok.detokenize(ex) for ex in batch]
        return [' '.join(ex) for ex in batch]
```

## TorchText Iterators for masked BPTT

In the basic part of the tutorial, we have already used Torchtext Iterators, but the customizable parts of the Torchtext Iterator that are truly helpful, compared to their competitors like Keras' Preprocessing library.

We talk about three main keywords: `sort`, `sort_within_batch` and `repeat`. 

First, PyTorch's current solution for masked BPTT is slightly bizzare, it requires you to pack the PyTorch variables into a padded sequences. Note that not all PyTorch RNN libraries support padded sequence, for example, [SRU](https://github.com/taolei87/sru) does not, and even though I haven't seen issues being raised, but possibly current implementation of [QRNN](https://github.com/salesforce/pytorch-qrnn) doesn't support padded sequence class either.

The code to incorporate padded sequence for RNN is simple:

```python
TEXT = data.ReversibleField(sequential=True, lower=True, include_lengths=True)
```

We first add `include_lengths` argument for the Field. Then, we build our iterator like:

```python
train, val, test = data.TabularDataset.splits(
        path='./data/', train='train.tsv',
        validation='val.tsv', test='test.tsv', format='tsv',
        fields=[('Text', TEXT), ('Label', LABEL)])
        
train_iter, val_iter, test_iter = data.Iterator.splits(
        (train, val, test), sort_key=lambda x: len(x.Text), 
        batch_sizes=(32, 256, 256), device=args.gpu, 
        sort_within_batch=True, repeat=False)
```

So in here, we look at a couple of arguments: `sort_key` is the sorting function Torchtext will call when it attempts to sort your dataset. If you leave this blank, no sorting will happen (I could be wrong, but on my simple "experiment", it seems to be the case). 

`sort` argument sorts through your entire dataset. You might want to avoid this for training set because the order of your traning examples will have an impact on your network, and my experiments have shown negative impact.

`sort_within_batch` must be flagged True if you want to use PyTorch's padded sequence class.

So in your model's `forward` method, you can write:

```python

 def forward(self, input, lengths=None):
        embed_input = self.embed(input)

        packed_emb = embed_input
        if lengths is not None:
            lengths = lengths.view(-1).tolist()
            packed_emb = nn.utils.rnn.pack_padded_sequence(embed_input, lengths)

        output, hidden = self.encoder(packed_emb)  # embed_input

        if lengths is not None:
            output = unpack(output)[0]
```


And in your main training loop you can pass your variable in like this:

```python
(x, x_lengths), y = data.Text, data.Description
output = model(x, x_lengths)
```

I hope people would find this post useful, and here are a list of references I've used for basic tutorial:


An (old) Torchtext Tutorial: [https://github.com/mjc92/TorchTextTutorial/blob/master/01.%20Getting%20started.ipynb](https://github.com/mjc92/TorchTextTutorial/blob/master/01.%20Getting%20started.ipynb)


Randomly Initializing Word Embeddings: [https://github.com/pytorch/text/issues/32](https://github.com/pytorch/text/issues/32)

