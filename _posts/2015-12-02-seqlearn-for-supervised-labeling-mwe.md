---
title: Seqlearn for supervised sequence labeling
--
Now that's something.

First look at [this list](http://scikit-learn.org/stable/related_projects.html). There are lots of things to be discovered and MWE-ed.

[seqlearn](https://github.com/larsmans/seqlearn) is for supervised stuff and I found it looking at [hmmlearn](https://github.com/hmmlearn/hmmlearn) -- a project on unsupervised sequence learning.

Let's get our hands dirty (I assume everything is installed, because it's no problem, seriously). Code is inspired by example in README.

```python

# coding:utf-8

import codecs

from seqlearn.datasets import load_conll
from seqlearn.evaluation import bio_f_score
from seqlearn.perceptron import StructuredPerceptron

def features(sequence, i):
    seq = sequence[i]
    yield "word=" + seq.lower()

    if seq.istitle():
        print 'title', seq
        yield "title"
    if seq.isupper():
        print 'uppercase', seq
        yield "uppercase"
    if not seq.isalnum():
        print 'not_alphanumeric', seq
        yield "not_alphanumeric"
    if i > 0 and not sequence[i - 1].isalnum():
        print 'not_alphanumeric_as_prev', sequence[i - 1]
        yield "prev_not_alphanumeric"
    if len(seq) > 3:
        print 'gt_3', seq
        yield 'gt_3'


X_train, y_train, lengths_train = load_conll(codecs.open("train.txt", "r", "utf-8"), features)
clf = StructuredPerceptron()
clf.fit(X_train, y_train, lengths_train)
X_test, y_test, lengths_test = load_conll(codecs.open("validation.txt", "r", "utf-8"), features)
y_pred = clf.predict(X_test, lengths_test)

print y_test
print y_pred
print(bio_f_score(y_test, y_pred))


```
