## READ ME

With this code you can train and evaluate Context Encoders (ConEc), an extension of word2vec, which can learn word embeddings from large corpora and create out-of-vocabulary embeddings on the spot as well as distinguish between multiple meanings of words based on their local contexts.
For further details on the model and experiments please refer to the [paper](https://arxiv.org/abs/1706.02496) (and of course please consider citing it ;-)).


dependencies: (main code) numpy, scipy; (experiments) sklearn, unidecode

### main code
- `word2vec.py`: code to train a standard word2vec model, adapted from the corresponding [gensim](https://radimrehurek.com/gensim/) implementation.
- `context2vec.py`: code to build a context matrix from a large collection of texts; this context matrix can then be multiplied with the corresponding word2vec embeddings to give the context encoder embeddings:
```python 
# get the text for training
sentences = Text8Corpus('data/text8')
# train the word2vec model
w2v_model = word2vec.Word2Vec(sentences, mtype='cbow', hs=0, neg=13, embed_dim=200, seed=3)
# get the global context matrix for the text
context_model = context2vec.ContextModel(sentences, min_count=w2v_model.min_count, window=w2v_model.window, wordlist=w2v_model.index2word)
context_mat = context_model.get_context_matrix(fill_diag=False, norm='max')
# multiply the context matrix with the (length normalized) word2vec embeddings
# to get the context encoder (ConEc) embeddings
conec_emb = context_mat.dot(w2v_model.syn0norm)
# renormalize so the word embeddings have unit length again
conec_emb = conec_emb / np.array([np.linalg.norm(conec_emb, axis=1)]).T
```

### experiments
`test_analogy.py` and `test_ner.py` contain the code to replicate the analogy and named entity recognition (NER) experiments discussed in the aforementioned paper.

To run the analogy experiment, it is assumed that the [text8 corpus](http://mattmahoney.net/dc/text8.zip) or [1-billion corpus](http://code.google.com/p/1-billion-word-language-modeling-benchmark/) as well as the [analogy questions](https://code.google.com/archive/p/word2vec/) are in a data directory.

To run the named entity recognition experiment, it is assumed that the corresponding [training and test files](http://www.cnts.ua.ac.be/conll2003/ner/) are located in the data/conll2003 directory.


If you have any questions please don't hesitate to send me an [email](mailto:cod3licious@gmail.com) and of course if you should find any bugs or want to contribute other improvements, pull requests are very welcome!
