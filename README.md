# Bible Hybrid Search

Research on the impact of hybrid retrieval strategies on semantic discovery and linking of biblical content.

Part of the [NEUU](https://github.com/neuu-org) biblical scholarship ecosystem.

## Research Question

How do different hybrid search strategies — combining lexical (BM25) and semantic (embedding) components with query expansion, reranking, and diversification — impact the quality of verse retrieval and theme linking in a corpus enriched by cross-references, commentaries, and topics?

## Hypothesis

A hybrid strategy with controlled combination of BM25, semantic embeddings, query expansion, reranking, and diversification produces a better balance between precision, coverage, and interpretive utility than purely lexical or purely semantic approaches.

## Experimental Variables

### Independent (search configuration)

| Variable | Control | Variations |
|----------|---------|------------|
| `alpha` (lexical-semantic fusion) | 0.5 | 1.0 (lexical only), 0.0 (semantic only) |
| `expand` (query expansion) | false | true |
| `rerank` (second-stage reranking) | false | true |
| `mmr_lambda` (relevance vs diversity) | 0.8 | 0.5, 0.2 |
| `embedding_source` | verse | unified |

### Dependent (metrics)

- Precision@K, Recall@K, MAP, NDCG
- Latency
- Result diversity (unique books, references)
- Cross-reference agreement rate
- Commentary agreement rate
- Plausible new discovery rate

## Validation Framework

The research uses a layered truth source strategy to avoid methodological circularity:

| Layer | Sources | Independence |
|-------|---------|:------------:|
| **Gold** | Cross-references, CCEL Precision, manual curation | High |
| **Silver** | CCEL Coverage, definition refs, promoted themes | Medium |
| **Signal** | Embeddings, gazetteers, entities | Low |

**Key principle:** Embeddings are used for discovery, not as ground truth. Independent sources validate.

> Full methodology: [docs/research/truth-source-strategy.md](docs/research/truth-source-strategy.md)

## Repository Structure

```
bible-hybrid-search/
├── docs/
│   ├── research/                      # Core research methodology
│   │   ├── scope.md                   # Research scope and delimitation
│   │   ├── evidence-master.md         # Evidence map and experimental protocol
│   │   ├── truth-source-strategy.md   # Gold/silver/signal validation framework
│   │   ├── pipeline-phases-and-embeddings.md  # Pipeline phases + embedding strategy
│   │   ├── dataset-inventory.md       # Dataset inventory and gaps
│   │   └── dataset-integration-strategy.md
│   ├── datasets/                      # Dataset documentation
│   │   ├── topics-evolution.md        # V1→V3 topic dataset evolution
│   │   ├── crossrefs-readme.md        # Cross-references dataset
│   │   ├── crossrefs-methodology.md   # Cross-references merge methodology
│   │   └── commentaries-analysis.md   # Commentary corpus analysis
│   └── infrastructure/                # System architecture docs
│       ├── api-architecture.md
│       ├── topical-pipeline.md
│       └── dev-flow.md
├── experiments/
│   ├── queries/
│   │   └── pilot_queries_v1.json      # 10 curated queries with gold references
│   └── runs/                          # Benchmark run outputs (future)
├── analysis/                          # Analysis scripts and notebooks (future)
└── writing/                           # Research paper chapters (future)
```

## Datasets (NEUU Ecosystem)

This research depends on the following datasets, managed as separate repositories:

| Dataset | Repository | Scale | Role |
|---------|-----------|-------|------|
| Commentaries | [bible-commentaries-dataset](https://github.com/neuu-org/bible-commentaries-dataset) | 31,218 verse files | Silver/signal validation |
| Cross-references | *coming soon* | 31,060 unified files, 1.2M links | Gold validation |
| Topics V3 | *coming soon* | 7,873 topics | Pipeline input |
| Dictionary | *coming soon* | 5,998 entries | Silver validation |
| Bible text | *coming soon* | 20 translations (EN+PT) | Retrieval corpus |

## Experimental Pipeline

```
Phase 0: AI Enrichment     → Extract semantic signals from topics
Phase 1: Theme Discovery    → Resolve themes against catalog
Phase 2: Theme Promotion    → Use hybrid search to evaluate theme evidence  ← RESEARCH FOCUS
Phase 3: Verse Linking      → Use hybrid search for final ranking           ← RESEARCH FOCUS
```

Phases 0-1 are infrastructure. Phases 2-3 are the core experimental contribution.

## Current Status

- [x] Research scope defined
- [x] Validation framework designed (gold/silver/signal)
- [x] 10 pilot queries curated with gold references
- [x] Commentary corpus complete (31,218 files)
- [x] Cross-references consolidated (1.2M links)
- [x] Topics V3 unified (7,873 topics)
- [ ] Gold set validated against crossrefs + CCEL
- [ ] Benchmark runner operational
- [ ] First experimental run
- [ ] Analysis and writing

## License

Research documentation: CC BY 4.0

## Citation

```bibtex
@misc{neuu_bible_hybrid_search_2026,
  title={Impact of Hybrid Retrieval Strategies on Semantic Discovery and Linking of Biblical Content},
  author={Coelho, Iury},
  year={2026},
  publisher={GitHub},
  url={https://github.com/neuu-org/bible-hybrid-search}
}
```
