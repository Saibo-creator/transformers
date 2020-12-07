---
language:
- en
tags:
- legal
license: apache-2.0
metrics:
- precision
- recall
---



# LEGAL-ROBERTA
We introduce LEGAL-ROBERTA, which is a domain-specific language representation model fine-tuned on large-scale legal corpora(4.6 GB). 

## Training data

The tranining data consists of 3 origins:

1. Patent Litigations (https://www.kaggle.com/uspto/patent-litigations): This dataset covers over 74k cases across 52 years and over 5 million relevant documents. 5 different files detail the litigating parties, their attorneys, results, locations, and dates.
    1. *1.57GB*
    2. abbrev:PL
    3. *clean 1.1GB*


2. Caselaw Access Project (CAP) (https://case.law/): Following 360 years of United States caselaw, Caselaw Access Project (CAP) API and bulk data services includes 40 million pages of U.S. court decisions and almost 6.5 million individual cases.
    1. *raw 5.6*
    2. abbrev:CAP
    3. *clean 2.8GB*
3. Google Patents Public Data (https://www.kaggle.com/bigquery/patents): The Google Patents Public Data contains a collection of publicly accessible, connected database tables for empirical analysis of the international patent system.
    1. *BigQuery (https://www.kaggle.com/sohier/beyond-queries-exploring-the-bigquery-api)*
    2. abbrev:GPPD(1.1GB,patents-public-data.uspto_oce_litigation.documents)
    3. *clean 1GB*

## Training procedure
We start from a pretrained ROBERTA-BASE model and fine-tune it on legal corpus.

Fine-tuning configuration:
- lr = 5e-5(with lr decay, ends at 4.95e-8)
- num_epoch = 3
- Total steps = 446500
- Total_flos = 2.7365e18

Loss starts at 1.850 and ends at 0.880
The perplexity after fine-tuning on legal corpus = 2.2735

## Eval results
We benchmarked the model on two downstream tasks: Multi-Label Classification for Legal Text and Catchphrase Retrieval with Legal Case Description. 

TODO

## Limitations:
In the Masked Language Model showroom, the tokens have a prefix **Ġ**. This seems to be wired but I haven't yet been able to fix it.
I know in case of BPE tokenizer(ROBERTA's tokenizer), the symbol Ġ means the end of a new token and the majority of tokens in vocabs of pre-trained tokenizers start with Ġ.

For example
```
import transformers
tokenizer = transformers.RobertaTokenizer.from_pretrained('roberta-base')
print(tokenizer.tokenize('I love salad'))
```
Outputs:

```
['I', 'Ġlove', 'Ġsalad']
```

So I think this is not fundamentally linked to the model itself.

## BibTeX entry and citation info





