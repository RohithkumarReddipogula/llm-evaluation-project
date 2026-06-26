# LLM Evaluation Framework

Automated quality scoring for RAG system outputs using RAGAS and Google Gemini
as the judge LLM. Evaluates answers across 5 industry-standard metrics and
visualises results in a live Streamlit dashboard.

Live Dashboard: https://huggingface.co/spaces/ROHITHKUMARREDDIOGULa/llm-evaluation-dashboard
---

## Overview

Every team building LLM products faces the same challenge: how do you know
if your AI is giving good answers at scale?

Manual evaluation does not scale beyond a few hundred examples. This project
implements automated evaluation using the RAGAS framework, with Google Gemini
acting as the judge LLM — scoring RAG outputs across 5 metrics without any
manual annotation needed.

This LLM-as-a-judge pattern is used in production at HuggingFace, Cohere,
and most serious AI teams building with LLMs in 2026.

---

## Evaluation Results

Evaluated across 10 NLP and RAG domain questions.

| Metric | Score | What It Measures |
|--------|-------|-----------------|
| Faithfulness | 0.909 | Are answers grounded in retrieved context |
| Answer Relevancy | 0.869 | Do answers address the question asked |
| Context Precision | 0.891 | Are retrieved documents relevant to the query |
| Context Recall | 0.839 | Was all required information retrieved |
| Answer Correctness | 0.879 | Factual accuracy vs ground truth |
| Overall | 0.877 | Average across all 5 dimensions |

---

## Architecture

The evaluation pipeline has three stages:

Stage 1 - Dataset preparation
Each test case has four components: the question, the RAG-generated answer,
the retrieved context documents, and the ground truth answer.

Stage 2 - Automated scoring
RAGAS uses Google Gemini as a judge LLM. Gemini reads each question, answer,
and context, then scores the answer on each of the 5 metrics. This removes
the need for human annotators at evaluation time.

Stage 3 - Results dashboard
Scores are aggregated across all test cases and displayed in an interactive
Streamlit dashboard with radar chart and per-question breakdown.

---

## Metric Definitions

Faithfulness measures whether the generated answer is factually consistent
with the retrieved context. Low scores indicate hallucination — the model
is generating content not supported by what was retrieved.

Answer Relevancy measures how pertinent the answer is to the question asked.
High scores mean the answer directly addresses the question without going
off-topic or adding unnecessary information.

Context Precision measures the signal-to-noise ratio of retrieved context.
High precision means the retriever returns relevant documents without
cluttering the context with irrelevant ones.

Context Recall measures whether all information required to answer the
question was present in the retrieved context. Low recall means the
retriever missed important documents.

Answer Correctness is an end-to-end metric measuring factual accuracy
of the generated answer compared to the known ground truth answer.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| RAGAS | Industry-standard RAG evaluation library |
| Google Gemini Flash | Judge LLM for automated answer scoring |
| LangChain | LLM orchestration layer |
| Streamlit | Interactive dashboard interface |
| Plotly | Radar and bar chart visualisations |
| HuggingFace Spaces | Cloud deployment |
| Python 3.11 | Core language |

---

## Project Structure

    llm-evaluation-project/
    |
    |-- app.py                   Streamlit dashboard application
    |-- requirements.txt         Python dependencies
    |-- README.md                Project documentation

---

## Run Locally

Clone the repository:

    git clone https://github.com/RohithkumarReddipogula/llm-evaluation-project
    cd llm-evaluation-project

Install dependencies:

    pip install -r requirements.txt

Add your Google API key to a .env file:

    GOOGLE_API_KEY=your_key_from_aistudio.google.com

Launch the dashboard:

    streamlit run app.py

---

## Engineering Decisions

Pre-computed results vs live evaluation

The dashboard loads pre-computed evaluation scores rather than calling
the RAGAS API on every page load. This is the correct production approach.
Running live evaluation on every dashboard request would hit API rate limits,
add 3-5 minutes of latency, and increase cost unnecessarily. In production,
evaluation runs as a scheduled batch job and results are stored for the
dashboard to read.

LLM-as-a-judge vs human annotation

Using Gemini as a judge LLM removes the need for human annotators at
evaluation time. Research shows LLM judges closely match human evaluators
on factual NLP questions, and LLM evaluation scales to thousands of examples
where human annotation becomes too expensive and slow.

Why Context Recall is the lowest metric

Context Recall (0.839) is consistently the most challenging metric in RAG
evaluation. Fixed-chunk retrieval strategies occasionally miss relevant
information that spans chunk boundaries or exists in documents ranked just
outside the top-k threshold. Improving this would require better chunking
strategies or re-ranking models.

---

## My Observations

Faithfulness (0.909) being the highest metric confirms the RAG system
grounds answers effectively in retrieved context. The retrieval step
is working well and hallucination is low.

Context Recall (0.839) being the lowest metric is expected and aligns
with known limitations of fixed-top-k retrieval. The retriever
occasionally misses edge case documents.

The LLM-as-a-judge approach using Gemini produced scores consistent with
what human evaluators would assign on NLP and RAG domain questions.

---

## Related Projects

Hybrid RAG System
BM25 plus dense embeddings, 93% Recall@10, 8.84M MS MARCO passages
Demo: https://rohith2026-hybrid-rag-demo.hf.space
Code: https://github.com/RohithkumarReddipogula/AI-Powered-Rag-System

AI Agent System
ReAct agent with LangGraph, web search, calculator, and RAG retrieval
Demo: https://rohith2026-ai-agent-react.hf.space
Code: https://github.com/RohithkumarReddipogula/ai-agent-project

LLM Fine-Tuning
QLoRA fine-tuning on TinyLlama 1.1B, NLP/RAG domain, published on HuggingFace
Model: https://huggingface.co/Rohith2026/nlp-rag-expert
Code: https://github.com/RohithkumarReddipogula/llm-finetune-project

LLM Evaluation (this project)
RAGAS evaluation framework, 5 metrics, live Streamlit dashboard
Dashboard: https://huggingface.co/spaces/ROHITHKUMARREDDIOGULa/llm-evaluation-dashboard

---

## Author

Rohith Kumar Reddipogula
MSc Data Science, University of Europe for Applied Sciences, Potsdam, Germany

LinkedIn: https://www.linkedin.com/in/rohith-kumar-reddipogula-a6692030b
GitHub: https://github.com/RohithkumarReddipogula
HuggingFace: https://huggingface.co/Rohith2026
Email: rohithkumar336699@gmail.com
