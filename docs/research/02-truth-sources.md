# Truth Source Strategy

## Purpose
Define which sources validate the hybrid search and semantic linking, avoiding methodological circularity.

## Core Principle
Embeddings and scores from the retrieval engine must NOT be the sole ground truth. The experiment compares method configurations against independent layers.

## Classification

### Gold — Primary Validation
High independence from the retrieval engine.

**1. Cross-references**
- Source: [neuu-org/bible-crossrefs-dataset](https://github.com/neuu-org) (planned)
- Scale: 31,060 unified files, ~1.2M links from OpenBible + SoulLiberty/TSK
- Strength: structurally independent from embeddings, strong for intertextual adherence
- Limitation: cross-reference does not always equal thematic relationship

**2. Manual curation**
- Source: `experiments/queries/pilot_queries_v1.json` in this repo
- Scale: 10 queries with gold_references and relevance_judgments
- Strength: high control over important cases
- Limitation: small scale, requires discipline to avoid bias

### Silver — Auxiliary Validation
Strong but supplementary.

**1. Definition refs**
- Source: extracted from V3 topic definitions (Easton, Smith, Nave, Torrey)
- Scale: 17,715 refs across 4,438 topics
- Strength: independent from hybrid search, tied to encyclopedic scope
- Use: weak supervision, context for curation

**2. Promoted themes / enriched topics**
- Source: topical pipeline internal output
- Strength: high domain adherence
- Limitation: close to the method itself — should not be sole validation

### Signal — Discovery Only
Help explain, rank, or generate candidates. NOT ground truth.

**1. Embeddings** (small, large, unified)
- Use: retrieval, reranking, scoring, candidate generation
- Risk: circularity if used as the truth they are evaluated against

**2. Gazetteers, entities, symbols**
- Use: query classification, explainability, semantic audit

## Anti-Circularity Rules
1. Never use embeddings as the sole primary truth
2. Do not treat derived topics, definitions, and comments as fully independent of each other
3. Prioritize more external and more manual sources as gold
4. Register, query by query, where each validation reference came from

## Golden Rule
The more a source was derived from the biblical corpus itself and the project's own pipeline, the lower its weight as independent truth.

## What is NOT available yet (as of 2026-03-14)
- **CCEL Precision/Coverage**: Referenced in earlier documentation as gold/silver, but not confirmed as materialized and accessible. Do not depend on it for the first experimental round.
- **Commentary embeddings**: Not generated yet. Commentaries validate via textual agreement, not vector similarity.
