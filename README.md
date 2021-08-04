# transjurisdictional-transformers
Helping lawyers quickly find similar legislation from another jurisdiction.


## Project Overview

### Problem Statement and Background

Lawyers engaged in cross-jurisdictional work might have to quickly familiarise themselves with legal positions in a few jurisdictions. These different jurisdictions may have statutes covering similar points of law, but may not use consistent wording. Note that the actual legal requirements may differ between jurisdictions, the provisions are analogous (think about the piece of legislation on mask wearing in Singapore vs that in UK).

Lawyers may have to go through a tedious process of finding out which are the analogous positions on a point of law in a different jurisdiction either through initial background research on cross-jurisdiction comparison articles, or simply manually comb through the different statutes. 

The hypothesis is that helping lawyers quickly get to the analogous provisions in another jurisdiction can be sped up using natural language processing techniques that encode semantic similarity.

### Project Goals

This is a project aimed at helping lawyers doing cross-jurisdictional research by helping them quickly match a legislation provision of interest to its equivalent in another jurisdiction. The matching will be done on a semantic level, using vector similarity, specifically on cosine similarity.

To achieve this, several methods for embedding each legislative provision will be used and the outcome will be evaluated on some prepared positive match examples. 

The embedding methods tried will be:
- tfidf (term frequency inverse document frequency)
- fastText
- BERT

And a baseline performance will be calculated based on edit distance between the titles of the legislation sections.

### Scope

For the scope of the first version of this project, each dataset will only contain one legislation from each jurisdiction, and the legislations covered are:
- Singapore Copyright Act & UK Copyright, Designs and Patents Act (CDPA)
- Singapore and UK Trade Marks Acts
- Singapore Personal Data Protection Act and European Union (EU) General Data Protection Regulation (GDPR)

The matching will be done on a section level. For the purposes of this project, articles in the GDPR will be treated as sections.

### Data

Unfortunately, the raw legislation data will not be uploaded to the repository to stay clear of permission issues. The scripts to parse the html will also not be uploaded for similar reasons.

The [data](../transjurisdictional-transformers/data) available on this repository are
- the saved [vectors](../transjurisdictional-transformers/data/vectors) to allow users to still use the model to match legislation, and also to run the [evaluation scripts](../transjurisdictional-transformers/notebooks/02-evaluation.ipynb)
- the [answer keys](../transjurisdictional-transformers/data/answer-keys) needed for the evaluation
- the [baseline edit distance results](../transjurisdictional-transformers/data/baselines) needed to get baseline scores
- the BERT [models and tokenizers](../transjurisdictional-transformers/models) from the project are also available

### Flask App

There is [flask app](https://github.com/nysk92/transjurisdictional-transformers-app/blob/main/README.md) that can be run on the browser for the tool to be tried out, preloaded with the vectors prepared in this project.

### Adaptability

Although the scope of this project is limited to the legislations mentioned above, the limitation comes more from the time needed to annotate examples for meaningful evaluation rather than any technical limitation on data volume.

The code in these notebooks is written in a way to allow it to be as reusable as possible for anyone to try their own legislation matching with these embedding methods once they have prepared their legislation data in the required input format.


## Implementation

| Method                         | Copyright  Recall @ 3 20 Examples | Trade Mark Recall @ 3 12 Examples | Data Protection Recall @ 3 12 Examples |
|--------------------------------|-----------------------------------|-----------------------------------|----------------------------------------|
| Title Edit Distance (Baseline) | 0.35                              | 0.83                              | 0.17                                   |
| tfidf (cos sim)                | 0.8                               | 1                                 | 0.33                                   |
| fastText (cos sim)             | 0.4                               | 0.33                              | 0.25                                   |
| BERT (cos sim)                 | 0.55                              | 0.83                              | 0.25                                   |

Different hyerparams were used for fastText on data protection set: (dim=50, lr=0.0001, epoch=50, minn=6, minCount=3, ws=10, model='skipgram', wordNgrams=3)


## Results and Conclusions

| Method                         | Copyright  Recall @ 3 20 Examples | Trade Mark Recall @ 3 12 Examples | Data Protection Recall @ 3 12 Examples |
|--------------------------------|-----------------------------------|-----------------------------------|----------------------------------------|
| Title Edit Distance (Baseline) | 0.35                              | 0.83                              | 0.17                                   |
| tfidf (cos sim)                | 0.8                               | 1                                 | 0.33                                   |
| fastText (cos sim)             | 0.4                               | 0.33                              | 0.25                                   |
| BERT (cos sim)                 | 0.55                              | 0.83                              | 0.25                                   |


On these example legislation and example answer keys, tfidf performs the best.
However, expectedly, tfidf only works well with very similarly worded input and targets.
In 1/12 of the less similarly worded examples (PDPA -> GDPR), BERT got the correct result over tfidf, showing some potential for BERT to catch ‘harder cases’. But more data and tests are needed to see if this is really the case.



### Conclusion so Far

- This tool can benefit lawyers needing to quickly map very similarly worded legislation, and can be rapidly built with tfidf.
- For more challenging situations (less similarly worded legislation), more work needs to be done to explore the potential of more sophisticated embeddings, but they do not work so well “out of the box” and with less than 1000 examples of training data. 


### Next Steps

- Try out more fastText hyperparams. Try pretraining with more external data of related legal topics.
- Dive deeper into error analysis. Look at wrong examples and examine examples that models predicted differently.
- Try on more examples of less similarly worded legislation. Less ‘keyword’ based, thus more challenging for tfidf, see if BERT can outperform in those situations.
- Consider stacking models with some rules based hierarchy. E.g. tfidf -> BERT.


## References

BERT paper: 
[BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2019)
](https://arxiv.org/pdf/1810.04805.pdf)

Pretraining BERT in PyTorch and getting vectors:
- https://towardsdatascience.com/bert-for-measuring-text-similarity-eec91c6bf9e1
- https://github.com/jamescalam/transformers/blob/main/course/training/03_mlm_training.ipynb
- https://www.youtube.com/watch?v=IC9FaVPKlYc

BERT learning rates:
- https://wandb.ai/jack-morris/david-vs-goliath/reports/Does-Model-Size-Matter-A-Comparison-of-BERT-and-DistilBERT--VmlldzoxMDUxNzU

fastText paper:
[Enriching Word Vectors with Subword Information (Bojanowski et al., 2016)](https://arxiv.org/pdf/1607.04606v2.pdf)
