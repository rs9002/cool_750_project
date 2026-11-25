# cool_750_project

ECE 750: An empirical evaluation of RAG architectures (Traditional RAG vs. LightRAG),
vector database backends (Qdrant vs. PostgreSQL + pgvector), and LLMs, on HotpotQA.

## Branches

| Branch | Contents |
|---|---|
| `main` | Shared code only: data prep, common utilities, evaluation and benchmarking |
| `feature/shared` | Dataset prep + common code (chunking, embeddings, prompts, LLM clients, token/cost logging). Merges into `main` |
| `feature/evaluation` | Metrics + benchmarking. Merges into `main` |
| `traditional-qdrant` | Traditional RAG + Qdrant |
| `traditional-pgvector` | Traditional RAG + pgvector |
| `lightrag-qdrant` | LightRAG + Qdrant |
| `lightrag-pgvector` | LightRAG + pgvector |

Phase 1 runs all four combination branches with one fixed model.
Phase 2 runs the three models on the two branches that use the database chosen after Phase 1.

### Rule: shared code is only edited on `main`

Data prep, prompts, embeddings, metrics and benchmarks change only through
`feature/shared` or `feature/evaluation` -> `main`. Each combination branch then
runs `git merge main` to pick up the change. Do not edit shared code on a
combination branch, or the four configurations stop being comparable.

## Folder structure

```
configs/
  base.yaml            # held constant: embedding model, chunk size, prompt,
                       # temperature, top-k, similarity metric
  models/              # openai, anthropic, google
  run.yaml             # architecture + backend for this branch
data/
  raw/                 # downloaded HotpotQA (gitignored)
  processed/           # chunks, question subsets, gold labels
  scale/               # 1k / 5k / 10k chunk corpora
src/
  shared/              # data prep, chunking, embeddings, prompts, LLM clients,
                       # token and cost tracking
  evaluation/
    metrics/           # Recall@k, MRR, nDCG, supporting facts, EM, F1, faithfulness
    benchmarks/        # retrieval-only vs end-to-end latency, QPS, concurrency,
                       # CPU/RAM/disk
  pipeline/            # differs per combination branch
scripts/               # prepare data, build index, run, aggregate
results/<branch>/<date>/  # each branch writes only to its own folder
analysis/              # notebooks and plots (on main)
reports/               # final paper, slides
```

## Setup

Copy `.env.example` to `.env` and add your API keys.
