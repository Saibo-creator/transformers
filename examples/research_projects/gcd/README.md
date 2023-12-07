# 🤗 Transformers Grammar Constrained Decoding


## Introduction
The project is an extension for the popular Transformers library, tailored for working with context-free grammars (CFG). 
One notable use case is to use the extension to force a language model to generate a valid json object.


## QuickStart: Force LLM to generate a valid json object

The below example can be found in `transformers/examples/research_projects/gcd/run_grammar_constrained_generation.py`

```python

from transformers import AutoModelForCausalLM, AutoTokenizer
from transformers_cfg.grammar_utils import IncrementalGrammarConstraint
from transformers_cfg.generation.logits_process import GrammarConstrainedLogitsProcessor

if __name__ == "__main__":
    # Load model and tokenizer
    tokenizer = AutoTokenizer.from_pretrained("gpt2")
    tokenizer.pad_token = tokenizer.eos_token
    model = AutoModelForCausalLM.from_pretrained("gpt2")

    # Load json grammar
    with open("grammars/json.ebnf", "r") as file:
        grammar_str = file.read()
    grammar = IncrementalGrammarConstraint(grammar_str, "root", tokenizer)
    grammar_processor = GrammarConstrainedLogitsProcessor(grammar)

    # Generate
    prefix1 = "This is a valid json string for http request:"
    prefix2 = "This is a valid json string for shopping cart:"
    input_ids = tokenizer([prefix1, prefix2], add_special_tokens=False, return_tensors="pt", padding=True)["input_ids"]

    output = model.generate(
        input_ids,
        do_sample=False,
        max_length=50,
        num_beams=2,
        logits_processor=[grammar_processor],
        repetition_penalty=5.0,
        num_return_sequences=1,
    )
    # decode output
    generations = tokenizer.batch_decode(output, skip_special_tokens=True)
    print(generations)

    """
    'This is a valid json string for http request:{ "request": { "method": "GET", "headers": [], "content": "Content","type": "application" }}
    'This is a valid json string for shopping cart:This is a valid json string for shopping cart:{ "name": "MyCart", "price": 0, "value": 1 }
    """

```

## Grammar Collection

We provide a collection of grammars in the `examples/grammars` folder, which are mostly identical to the grammars in llama-cpp project.
We try to keep the grammars up-to-date with the original grammars from llama-cpp project.
But up to now, we can not yet guarantee that all grammars from llama-cpp project can be directly used in transformers-CFG.

The list of grammars contains:
- `json.ebnf`: A grammar for generating valid json objects.
- `c.ebnf`: A grammar for generating valid C programs.
- `chess.ebnf`: A grammar for generating valid chess moves.
- `arithmetic.ebnf`: A grammar for generating valid arithmetic expressions.


## Why should I use Grammar Constrained Decoding?

- We offer the same grammar interface as llama-cpp project, allowing you to drop-in replace llama-cpp with transformers-CFG.
- We allow you to use any of the models in the 🤗 Transformers library, including the ones that are not supported by llama-cpp.

## Installation as a package

We also provide a package for the project, which can be installed by:

```bash
pip install git+https://github.com/epfl-dlab/transformers_cfg.git
```

## License
This project is licensed under the [MIT License](LICENSE).

## Acknowledgement

This project is based on the [torch-grammars](https://github.com/Shopify/torch-grammar) project. 

## References

```bibtex
@misc{geng2023grammarconstrained,
      title={Grammar-Constrained Decoding for Structured NLP Tasks without Finetuning}, 
      author={Saibo Geng and Martin Josifoski and Maxime Peyrard and Robert West},
      year={2023},
      eprint={2305.13971},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```

