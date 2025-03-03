# Model Evaluation Framework

A simple asynchronous framework for evaluating language models on reasoning tasks using the OpenRouter API.

## Evaluation Results Repository

In order to keep the main repo clean and not clutter it with evaluation traces from different models, we store all evaluation results in a separate repository: [reasoning-gym-eval](https://github.com/open-thought/reasoning-gym-eval).

If you run evaluations and want to contribute your results, please create a pull request in the [reasoning-gym-eval](https://github.com/open-thought/reasoning-gym-eval) repository, not in the main reasoning-gym repo.

## Overview

This framework provides tools to evaluate language models on the reasoning_gym datasets. It supports:
- Concurrent evaluation of multiple questions and datasets
- Customizable dataset configurations
- Automatic result aggregation and summary generation
- Rate limiting for API calls

## Setup

1. Install reasoning-gym in development mode:
```bash
pip install -e ..
```

2. Install the additional dependencies required for evaluation:
```bash
pip install -r requirements-eval.txt
```

3. Set your OpenRouter API key as an environment variable:
```bash
export OPENROUTER_API_KEY=your-api-key
```


4. Prepare your evaluation configuration in YAML or JSON format (see example in `example_config.yaml`):

```yaml
# Example configuration
model: "meta-llama/llama-3.3-70b-instruct"
provider: "Hyperbolic"  # Optional, can be omitted
output_dir: "results"
max_concurrent: 10
default_size: 20  # Default size for all datasets
default_seed: 42  # Default seed for all datasets

categories:
  - category: "algebra"
    datasets:
      - dataset: "complex_arithmetic"
        params:
          min_real: -10
          max_real: 10
          min_imag: -10
          max_imag: 10

  - category: "arithmetic"
    datasets:
      - dataset: "chain_sum"
        size: 12
        seed: 43
        params:
          min_digits: 2
          allow_negation: true

      - dataset: "products"
        size: 10
        seed: 43
        params:
          min_digits: 2
          allow_negation: true
```

For example, to evaluate Claude 3.5 Sonnet on algorithmic datasets:

```yaml
model: "anthropic/claude-3.5-sonnet"
provider: "Anthropic"
output_dir: "results"
max_concurrent: 5
default_size: 50
default_seed: 45

categories:
  - category: "algorithmic"
    datasets:
      - dataset: "count_primes"
      - dataset: "game_of_life"
      - dataset: "graph_color"
      - dataset: "isomorphic_strings"
      - dataset: "letter_jumble"
      - dataset: "rotate_matrix"
      - dataset: "sentence_reordering"
      - dataset: "string_manipulation"
      - dataset: "word_ladder"
      - dataset: "word_sorting"
```

### Running Evaluations

To run evaluations:

```bash
python eval.py --config configs/your_config.yaml
```

For example:

```bash
python eval.py --config example_config.yaml --full-results
```


The results will be stored in a directory named after the model and timestamp, containing:
- `summary.json` - Summary of all results
- `results.json` - Full results (if `--full-results` is specified)
- Individual dataset results in category subdirectories

For example:
```
results/
└── meta-llama_llama-3.3-70b-instruct_20250227_162030/
    ├── summary.json
    ├── results.json
    ├── algebra/
    │   └── complex_arithmetic.json
    └── arithmetic/
        ├── chain_sum.json
        └── products.json
```

Please upload your results to [reasoning-gym-eval](https://github.com/open-thought/reasoning-gym-eval).
