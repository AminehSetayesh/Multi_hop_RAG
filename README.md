# Multi-Hop RAG: Query Decomposition and Sequential Retrieval

This repository explores and compares different Retrieval-Augmented Generation (RAG) strategies for answering multi-hop questions using the HotpotQA dataset.

The project builds on a separate **Simple RAG** implementation and investigates two approaches designed to handle questions that may require multiple pieces of information: **Query Decomposition** and **Sequential Retrieval**. A final notebook compares all three methods using the same evaluation questions.

The goal is to understand how different retrieval strategies affect answer quality, execution time, and computational cost.

## Project Overview

Standard RAG retrieves relevant context for a question and uses a language model to generate an answer. However, some questions require combining information from multiple sources or reasoning across several steps.

This project implements and evaluates two alternative retrieval strategies:

- **Query Decomposition:** Breaks a complex question into smaller subquestions, retrieves information for those subquestions, and uses the retrieved context to generate an answer.
- **Sequential Retrieval:** Retrieves information iteratively, using intermediate results to guide subsequent retrieval steps before generating the final answer.

Both approaches are compared with the existing Simple RAG baseline to examine whether more structured retrieval improves answer quality and whether any improvements justify the additional computational cost.

### Notebooks

| Notebook | Description |
|---|---|
| `01_build_corpus_and_index.ipynb` | Prepares the corpus and builds the retrieval index used by the experiments. |
| `02_multi_hop_rag_decomposition.ipynb` | Implements the Query Decomposition approach. |
| `03_multi_hop_rag_sequential.ipynb` | Implements the Sequential Retrieval approach. |
| `04_compare_three_rag_methods.ipynb` | Evaluates and compares Simple RAG, Query Decomposition, and Sequential Retrieval on the same set of questions. |

## Dataset

The experiments use **HotpotQA**, a question-answering dataset containing questions that can require reasoning over multiple supporting facts.

A subset of 30 questions is used for the current evaluation. The same evaluation questions are used across the three methods to support a fair, sample-by-sample comparison.

## Embedding Model

The project uses:

`sentence-transformers/all-MiniLM-L6-v2`

The model produces 384-dimensional embeddings. The embeddings are normalized before being indexed.

## Vector Retrieval

FAISS is used for dense similarity search with:

`IndexFlatIP`

Because the embeddings are normalized, inner-product similarity corresponds to cosine similarity.

The default retrieval configuration is:

```text
Top-k = 5
```

## Language Model

The generation component uses Qwen2.5-7B-Instruct with 8-bit quantization.

The model is loaded locally and the model files are not included in this repository.

Model inference requires a compatible environment with the necessary dependencies and sufficient memory. The notebooks are designed for Google Colab, but runtime availability and resource limits may vary.

The corpus and retrieval index are prepared in the corpus-and-index notebook. The Simple RAG implementation is maintained in a separate repository, and its existing outputs are reused in the comparison notebook.

**Simple RAG repository:** `(https://github.com/AminehSetayesh/Basic_RAG)`

## Methods Compared

### 1. Simple RAG — Baseline

Simple RAG retrieves relevant context for each question and generates an answer using a language model. In the current setup, each question requires one retrieval operation and one language-model call.

This method serves as the baseline for evaluating the two more structured retrieval strategies.

### 2. Query Decomposition

Query Decomposition breaks a complex question into smaller subquestions. The system retrieves information for the resulting subquestions and uses the collected context to generate the final answer.

This approach aims to make the information needed to answer multi-hop questions easier to retrieve.

### 3. Sequential Retrieval

Sequential Retrieval performs retrieval iteratively, allowing intermediate results to inform subsequent retrieval steps.

The objective is to gather the information needed to answer a question across multiple retrieval steps rather than relying on a single retrieval operation.

## Evaluation

The three methods are evaluated using the following answer-quality metrics:

- **Exact Match (EM):** Measures whether the normalized predicted answer exactly matches the reference answer.
- **Flexible Match (FM):** A binary, substring-based metric that checks whether one answer is contained in the other.
- **Token F1:** Measures lexical overlap between the predicted answer and the reference answer using token-level precision and recall.

Computational cost is also examined using:

- Mean execution time per question
- Mean number of language-model calls per question
- Mean number of retrieval operations per question

These metrics provide complementary perspectives on answer quality and execution cost. In particular, the number of model calls and retrieval operations helps explain the additional processing required by the more complex methods.

## Experimental Results

The current evaluation uses 30 HotpotQA questions.

| Method | Exact Match (%) | Flexible Match (%) | Mean Token F1 |
|---|---:|---:|---:|
| Simple RAG | 33.33 | 40.00 | 0.367 |
| Query Decomposition | 30.00 | 40.00 | 0.346 |
| Sequential Retrieval | 26.67 | 46.67 | 0.369 |

### Computational Cost

| Method | Mean Time (s/question) | Mean LLM Calls | Mean Retrieval Calls |
|---|---:|---:|---:|
| Simple RAG | 1.40 | 1.00 | 1.00 |
| Query Decomposition | 9.44 | 2.00 | 1.90 |
| Sequential Retrieval | 22.88 | 3.73 | 1.87 |

### Initial Findings

In the current experiment, Simple RAG achieves the highest Exact Match score and has the lowest mean execution time. Query Decomposition does not improve Exact Match over the baseline, while Sequential Retrieval achieves the highest Flexible Match and a slightly higher mean Token F1 than Simple RAG.

However, Sequential Retrieval also has the highest execution time and mean number of language-model calls. These initial results do not demonstrate that the additional complexity improves exact answer correctness on the current evaluation subset.

The results should be interpreted cautiously because the evaluation contains only 30 questions and the lexical metrics do not directly establish semantic correctness.

## Requirements

The notebooks are designed to run in a Python/Jupyter environment, such as Google Colab.

The exact dependencies depend on the implementation and may include packages for:

- Dataset loading and processing
- Text embeddings
- Vector similarity search
- Language-model inference
- Evaluation and result analysis

See the notebook imports and setup cells for the specific packages and model configuration used in each experiment.

## How to Run

For the intended workflow:

1. Open `01_build_corpus_and_index.ipynb` and prepare the corpus and retrieval index.
2. Run `02_multi_hop_rag_decomposition.ipynb` to execute the Query Decomposition experiment.
3. Run `03_multi_hop_rag_sequential.ipynb` to execute the Sequential Retrieval experiment.
4. Ensure the Simple RAG results from the separate repository are available at the paths expected by the comparison notebook.
5. Run `04_compare_three_rag_methods.ipynb` to evaluate and compare all three methods.

Before running the notebooks, check the dataset paths, output paths, model configuration, and any Google Drive mounting or environment-specific settings.

## Limitations and Future Work

This project is an experimental comparison rather than a comprehensive benchmark. The current findings are limited to a subset of 30 HotpotQA questions and the specific model, retrieval configuration, and evaluation procedures used in the notebooks.

Potential next steps include:

- Evaluating the methods on a larger set of questions
- Adding semantic answer evaluation
- Recording input and output token usage
- Repeating execution-time measurements under consistent conditions
- Investigating the trade-off between answer quality and computational cost

## Acknowledgments

This project uses the HotpotQA dataset for multi-hop question answering research. The Simple RAG baseline is maintained separately and is reused for comparative evaluation.

