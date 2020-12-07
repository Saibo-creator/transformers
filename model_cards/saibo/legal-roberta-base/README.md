# LEGAL-ROBERTA



We introduce LEGAL-ROBERTA, which is a domain-specific language representation model fine-tuned on large-scale legal corpora(4.6 GB). 
We start from a pretrained ROBERTA-BASE model and fine-tune it on legal corpus.

Fine-tuning configuration:
- lr = 5e-5(with lr decay, ends at 4.95e-8)
- num_epoch = 3
- Total steps = 446500
- Total_flos = 2.7365e18

Loss starts at 1.850 and ends at 0.880
The perplexity after fine-tuning on legal corpus = 2.2735

We benchmarked the model on two downstream tasks: Multi-Label Classification for Legal Text and Catchphrase Retrieval with Legal Case Description. 
