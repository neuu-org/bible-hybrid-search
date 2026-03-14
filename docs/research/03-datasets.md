# Dataset Inventory

Datasets are managed as independent repositories under [neuu-org](https://github.com/neuu-org).

## Ready

| Dataset | Repo | Scale | Role | Status |
|---------|------|-------|------|--------|
| **Commentaries** | [bible-commentaries-dataset](https://github.com/neuu-org/bible-commentaries-dataset) | 31,218 verse files (100+ authors, AD 100-1700) | Silver/Signal — commentary agreement, theological validation | Ready (originals), Partial (879 enriched = 2.8%) |
| **Cross-references** | planned: bible-crossrefs-dataset | 31,060 unified files, ~1.2M links | Gold — primary structural validation | Ready |
| **Topics V3** | planned: bible-topics-dataset | 7,873 topics, 112,835 connections | Pipeline input (phases 0-3) | Ready |
| **Dictionary** | planned: bible-dictionary-dataset | 5,998 entries (Easton + Smith) | Silver — definitions, definition_refs | Ready |
| **Pilot queries** | this repo: experiments/queries/ | 10 curated queries | Experimental evaluation base | Ready (seed) |

## Partial

| Dataset | Scale | Status |
|---------|-------|--------|
| **Topical pipeline pilot** | 336 topics (letter A), 326 with ai_enrichment, 231 with phase1_discovery | Sufficient for first experimental round |
| **Bible text** | 20 translations (7 EN + 13 PT) as JSON | Exists but not loaded as retrieval corpus in current engine |

## Not Available

| Dataset | Referenced as | Reality |
|---------|-------------|---------|
| CCEL Precision | Gold auxiliary | Not confirmed as accessible |
| CCEL Coverage | Silver | Not confirmed as accessible |
| CCEL embeddings | Signal | Not confirmed; different corpus from Catena commentaries |
| Bible embeddings in DB | Retrieval corpus | Requires PostgreSQL restoration — not operational now |

## Gaps Blocking the Experiment

1. **Database with embeddings not operational** — the benchmark runner needs the API with verse embeddings loaded
2. **Gold set not cross-validated** — pilot queries have gold_references but they haven't been validated against crossrefs systematically
