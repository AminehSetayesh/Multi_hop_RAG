# Multi-hop RAG

An educational project exploring Retrieval-Augmented Generation (RAG), semantic search, and question decomposition using Python, FAISS, and a local quantized language model.

## Project Overview

This project aims to build and evaluate a multi-hop RAG pipeline capable of answering questions that require information from multiple pieces of evidence.

The project is developed incrementally, starting with a simple RAG system and progressing toward question decomposition and multi-step retrieval.

## Technologies

- Python
- Jupyter Notebook
- Google Colab
- Hugging Face Transformers
- Sentence Transformers (`all-MiniLM-L6-v2`)
- FAISS
- Qwen2.5-7B-Instruct (8-bit quantization)
- HotpotQA dataset

## Current Status

The initial RAG pipeline and a question-decomposition-based approach have been implemented and tested on a small evaluation subset of HotpotQA.

Preliminary experiments have been conducted to examine answer accuracy and generation behavior. The independent contribution of question decomposition has not yet been established and requires further controlled evaluation.

## Goals

- Build a reproducible RAG pipeline.
- Investigate the effect of question decomposition on retrieval and answer accuracy.
- Analyze retrieval and answer-generation errors.
- Evaluate accuracy, computational cost, and retrieval efficiency.
- Develop a foundation for further experiments with multi-step reasoning and RAG.

## Notes

This is an ongoing learning and research project. Experimental results are preliminary and may change as the implementation and evaluation methodology improve.
