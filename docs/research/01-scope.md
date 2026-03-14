# Research Scope

## Title
Impact of hybrid retrieval strategies on semantic discovery and linking of biblical content.

## Problem
Lexical-only biblical search retrieves exact terms well but fails on synonymy, thematization, and indirect semantic connections. Semantic-only search improves conceptual coverage but loses textual precision and interpretability. The central problem is measuring how different hybrid retrieval configurations alter the quality of biblical discovery and semantic linking.

## Research Question
How do different hybrid search strategies — combining lexical and semantic components — impact the quality of verse retrieval and theme linking in a corpus enriched by cross-references, commentaries, and topics?

## Hypothesis
A hybrid strategy with controlled combination of BM25, semantic embeddings, query expansion, reranking, and diversification produces better balance between precision, coverage, and interpretive utility than purely lexical or purely semantic approaches.

## General Objective
Compare hybrid retrieval configurations to identify the setup that offers the best relationship between ranking quality, diversity, latency, and agreement with auxiliary biblical sources.

## Specific Objectives
- Measure the effect of `alpha` on lexical-semantic fusion
- Measure the effect of query expansion
- Measure the effect of second-stage reranking
- Measure the effect of `mmr_lambda` on relevance vs diversity
- Compare `embedding_source=verse` vs `embedding_source=unified`
- Verify agreement of results with cross-references, commentaries, and promoted themes

## Independent Variable
Search configuration defined by: `alpha`, `expand`, `rerank`, `mmr_lambda`, `embedding_source`

## Dependent Variables
- Precision@K, Recall@K, MAP, NDCG
- Latency
- Result diversity
- Cross-reference agreement rate
- Commentary agreement rate
- Plausible new discovery rate

## Delimitation
The primary experimental focus is hybrid search, semantic search, and theme linking. Gazetteers, symbols, and entities remain as auxiliary interpretation axes, not as parallel fronts.
