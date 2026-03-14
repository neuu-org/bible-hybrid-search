# Commentary Architecture and Role

## Authoritative Source
The authoritative source for raw commentaries is [neuu-org/bible-commentaries-dataset](https://github.com/neuu-org/bible-commentaries-dataset).

Scale: 31,218 unique verse-level JSON files from CatenaBible.com, covering 66 biblical books, 100+ Church Fathers (AD 100-1700).

## Layer Architecture

| Layer | Purpose | Files | Status |
|-------|---------|-------|--------|
| `00_raw_archive` | Immutable backup of original scraping | 31,218 | Complete (local only, not in git) |
| `01_original` | Canonical normalized verse files | 31,218 | Complete |
| `02_translated_enriched` | PT-BR translation + AI analysis | 879 (Acts 865, John 14) | 2.8% coverage |
| `03_structured` | Flattened JSONL (1 line per commentary) | — | Future |
| `04_embeddings` | Vector representations by language/model | — | Future |

## What is raw vs translated vs enriched

| Aspect | Raw/Original (01) | Translated + Enriched (02) |
|--------|-------------------|---------------------------|
| Language | English only | English + Portuguese (BR) |
| AI summary | No | one_sentence, abstract, key_points |
| Argumentative structure | No | thesis, arguments, objections, conclusion |
| Theological analysis | No | doctrines, traditions, method, controversies |
| Traceability | extraction_date, source_url | + translation_metadata, enrichment_metadata |

## Why this separation matters
1. **Source preservation**: CatenaBible scraping is non-reproducible (site may change). Raw data must be untouched.
2. **Auditability**: Each transformation (translation, enrichment) is logged with model, timestamp, and cost.
3. **Scientific reproducibility**: The research must document exactly which layer was used in each analysis.
4. **Avoiding contamination**: AI enrichment adds interpretation. If GPT-generated summaries enter the gold set, there is circularity risk. Separation allows using raw for validation and enriched for discovery.

## Where commentaries enter the research

**Gold set construction**: Commentaries are NOT gold. Cross-references and manual curation are gold. Commentaries can enter as silver when they corroborate crossrefs.

**Theological/semantic validation**: The theological_analysis field (doctrines, traditions, method) can validate whether hybrid search retrieves verses that the patristic tradition associated with the same theme.

**Qualitative analysis**: ai_summary and argumentative_structure enable case studies — e.g., showing that the system retrieved John 1:1 for the theme "creation" and that 3 Church Fathers confirm this association.

**Benchmark metric**: "Commentary agreement rate" compares retrieval results with passages that were historically commented. This is a dependent variable.

## What commentaries are NOT
- Not the sole source of truth — crossrefs have higher independence
- Not a substitute for crossrefs — crossrefs measure intertextual adherence; commentaries measure exegetical adherence
- Not the retrieval engine — they are validation corpus, not a search component
- CCEL != Catena — the CCEL corpus (if available) is a different dataset from the Catena commentaries
