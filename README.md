# Bible Hybrid Search

Research on the impact of hybrid retrieval strategies on semantic discovery and linking of biblical content.

Part of the [NEUU](https://github.com/neuu-org) biblical scholarship ecosystem.

## Research Question

How do different hybrid search strategies — combining lexical (BM25) and semantic (embedding) components with query expansion, reranking, and diversification — impact the quality of verse retrieval and theme linking in a corpus enriched by cross-references, commentaries, and topics?

## Documentation

| Doc | Description |
|-----|-------------|
| [01-scope](docs/research/01-scope.md) | Research problem, hypothesis, variables, delimitation |
| [02-truth-sources](docs/research/02-truth-sources.md) | Gold/silver/signal validation framework, anti-circularity rules |
| [03-datasets](docs/research/03-datasets.md) | Dataset inventory with real status (ready, partial, not available) |
| [04-experimental-protocol](docs/research/04-experimental-protocol.md) | Pipeline phases, comparisons, metrics, query set, benchmark runner |
| [05-commentaries](docs/research/05-commentaries.md) | Commentary architecture, layer justification, role in the research |

## Datasets (NEUU Ecosystem)

| Dataset | Repository | Role |
|---------|-----------|------|
| Commentaries | [bible-commentaries-dataset](https://github.com/neuu-org/bible-commentaries-dataset) | Silver/signal validation |
| Cross-references | bible-crossrefs-dataset (planned) | Gold validation |
| Topics V3 | bible-topics-dataset (planned) | Pipeline input |
| Dictionary | bible-dictionary-dataset (planned) | Silver validation |
| Bible text | bible-text-dataset (planned) | Retrieval corpus |

## Current Status

- [x] Research scope and methodology defined
- [x] Validation framework (gold/silver/signal) designed
- [x] 10 pilot queries curated with gold references
- [x] Commentary corpus organized (31,218 files) — [repo](https://github.com/neuu-org/bible-commentaries-dataset)
- [x] Cross-references consolidated (1.2M links)
- [x] Topics V3 unified (7,873 topics)
- [ ] Gold set validated against crossrefs
- [ ] Database with embeddings operational
- [ ] First experimental run
- [ ] Analysis and writing

## Structure

```
bible-hybrid-search/
├── docs/research/           # Research methodology (5 numbered docs)
├── experiments/
│   ├── queries/             # Curated query sets
│   └── runs/                # Benchmark outputs (future)
├── analysis/                # Analysis scripts (future)
└── writing/                 # Paper chapters (future)
```

## License

CC BY 4.0

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
