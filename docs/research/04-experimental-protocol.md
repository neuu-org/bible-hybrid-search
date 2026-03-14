# Experimental Protocol

## Pipeline Overview

The topical pipeline transforms raw topics into theme-verse links. The research measures how retrieval configuration affects phases 2 and 3.

```
Phase 0: AI Enrichment      → extract semantic signals from topics
Phase 1: Theme Discovery     → resolve proposed themes against catalog
Phase 2: Theme Promotion     → use retrieval to evaluate theme evidence    ← RESEARCH FOCUS
Phase 3: Verse Linking       → use retrieval for final verse ranking       ← RESEARCH FOCUS
```

Phases 0-1 are infrastructure. Phases 2-3 are the experimental contribution.

## Comparisons

| Group | Control | Variations |
|-------|---------|------------|
| `alpha` (lexical-semantic weight) | 0.5 | 1.0 (pure lexical), 0.0 (pure semantic) |
| `expand` (query expansion) | false | true |
| `rerank` (second-stage) | false | true |
| `mmr_lambda` (relevance vs diversity) | 0.8 | 0.5, 0.2 |
| `embedding_source` | verse | unified |

## Stratification
Results will be analyzed by:
- Query type: entity, phrase, concept, reference, question, metaphor, doctrine
- Book
- Configuration

## Metrics

### Phase 2 (Theme Promotion)
- Coverage by books
- Relevant verses per theme
- Average score strength
- Agreement with crossrefs
- Stability when parameters change

### Phase 3 (Verse Linking)
- Precision@K, Recall@K, MAP, NDCG
- Latency
- Result diversity (unique books, unique references)
- Cross-reference agreement rate
- Plausible new discovery rate

## Query Set
File: `experiments/queries/pilot_queries_v1.json`

10 queries covering:
- Phrases: "paz na terra"
- Concepts: "justica", "amor ao proximo", "perdao"
- Metaphors: "agua viva"
- Doctrine: "ressurreicao dos mortos"
- Entities: "Abraao", "sermao do monte"
- References: "Joao 3:16"
- Questions: "o que e graca"

Each query has: `gold_references`, `relevance_judgments` (1-3 scale), `seed_signals`, `validation_sources`.

## Benchmark Runner
Script: `run_hybrid_benchmark.py` (in bible-api repo)

```bash
# Dry run
python scripts/run_hybrid_benchmark.py --dry-run

# Full run
python scripts/run_hybrid_benchmark.py

# Specific experiments
python scripts/run_hybrid_benchmark.py --experiment baseline_hybrid --experiment alpha_lexical

# Specific queries
python scripts/run_hybrid_benchmark.py --query-id q001 --query-id q006
```

Output: `experiments/runs/<timestamp>/` with plan.json, summary.json, summary.csv, aggregate.csv, raw results.

## Prerequisites (not yet confirmed)
- PostgreSQL database with verse embeddings loaded
- bible-api Django app running
- Hybrid search engine operational (bible/ai/hybrid.py)
