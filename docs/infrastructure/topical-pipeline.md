# 📚 Pipeline de Tópicos Bíblicos - Arquitetura Completa

## Visão Geral

Pipeline completo para processamento de tópicos bíblicos do Nave's Topical Bible,
com descoberta de temas, linking de versículos e integração com busca semântica.

**Fonte:** Migrado e melhorado a partir de `bible-api-old/scripts/topical/`

---

## 🗺️ Diagrama do Pipeline

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              FASE 0: FONTE DE DADOS                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Nave's Topical Bible (XML/CCEL)                                        │    │
│  │  → 5000+ tópicos com referências bíblicas em inglês                     │    │
│  │  → Ex: "SERMON - Of Jesus, on the mount, Mt 5;6;7"                      │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         FASE 1: PARSING E UNIFICAÇÃO                            │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Scripts: reference_parser.py, io_utils.py, loader.py                   │    │
│  │                                                                         │    │
│  │  INPUT:  XML/texto bruto com "Ex 6:16-20;Jos 21:4,10;1Ch 6:2,3"        │    │
│  │  OUTPUT: JSON estruturado com biblical_references[]                     │    │
│  │                                                                         │    │
│  │  {                                                                      │    │
│  │    "book": "Exodus",                                                    │    │
│  │    "book_abbrev": "ex",                                                 │    │
│  │    "chapter": 6,                                                        │    │
│  │    "verses": [16, 17, 18, 19, 20],                                     │    │
│  │    "testament": "OT"                                                    │    │
│  │  }                                                                      │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  RESULTADO: 4058 tópicos em unified/topics/A-Z/*.json                          │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                      FASE 2: ENRIQUECIMENTO IA (GPT-4o-mini)                    │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Script: ai_enrichment.py (TopicEnrichmentService)                      │    │
│  │                                                                         │    │
│  │  INPUT:  Tópico com content e biblical_references                       │    │
│  │  OUTPUT: Enriquecimento estruturado                                     │    │
│  │                                                                         │    │
│  │  {                                                                      │    │
│  │    "summary": "Resumo teológico...",                                    │    │
│  │    "outline": ["Ponto 1", "Ponto 2", ...],                             │    │
│  │    "key_concepts": ["conceito1", "conceito2"],                         │    │
│  │    "theological_significance": "Importância...",                        │    │
│  │    "themes": ["salvação", "graça"],           // → Fase 3              │    │
│  │    "entities": [                               // → Gazetteer          │    │
│  │      {"name": "Moisés", "type": "PERSON", "role": "primary"}           │    │
│  │    ]                                                                    │    │
│  │  }                                                                      │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  CUSTO: ~1000-1500 tokens/tópico | STATUS: 568/4058 (14%)                      │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                      FASE 3: DESCOBERTA DE TEMAS (PHASE 1)                      │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Scripts: theme_service.py, policy_engine.py                            │    │
│  │                                                                         │    │
│  │  INPUT:  Temas extraídos da IA                                          │    │
│  │  OUTPUT: Proposals ou temas canônicos                                   │    │
│  │                                                                         │    │
│  │  Decisões possíveis:                                                    │    │
│  │  • auto_accept → Adiciona ao themes_catalog.json                        │    │
│  │  • propose → Adiciona ao themes_proposals.jsonl                         │    │
│  │  • alias_existing → Mapeia para tema existente                          │    │
│  │                                                                         │    │
│  │  Políticas de governança:                                               │    │
│  │  • Confiança mínima para auto-accept                                    │    │
│  │  • Detecção de duplicados via fuzzy matching                            │    │
│  │  • Clustering semântico de temas similares                              │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  STATUS: 290 temas canônicos | 4871 proposals pendentes                        │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                      FASE 4: PROMOÇÃO DE TEMAS (PHASE 2)                        │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Scripts: phase2_orchestrator.py, semantic_clustering.py                │    │
│  │                                                                         │    │
│  │  INPUT:  Proposals de temas                                             │    │
│  │  OUTPUT: Temas promovidos ao catálogo canônico                          │    │
│  │                                                                         │    │
│  │  Processo:                                                              │    │
│  │  1. Clustering semântico de proposals similares                         │    │
│  │  2. Votação/consolidação de clusters                                    │    │
│  │  3. Promoção para themes_catalog.json                                   │    │
│  │  4. Criação de hierarquias (parent/child)                              │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                    FASE 5: VERSE LINKING (PHASE 3) ⭐ MELHORAR                  │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Scripts: phase3_verse_linking.py, unified_scoring_engine.py            │    │
│  │                                                                         │    │
│  │  INPUT:  Tema canônico + versículos da Bíblia                           │    │
│  │  OUTPUT: Links versículo→tema com scores                                │    │
│  │                                                                         │    │
│  │  ┌─────────────────────────────────────────────────────────────────┐    │    │
│  │  │  SCORING MULTI-DIMENSIONAL (v2) - PONTO DE MELHORIA             │    │    │
│  │  │                                                                 │    │    │
│  │  │  sem: 0.717  ← Similaridade semântica (EMBEDDINGS)              │    │    │
│  │  │  lex: 0.03   ← Sobreposição lexical (palavras comuns)           │    │    │
│  │  │  ref: 1.0    ← Referência direta do tópico                      │    │    │
│  │  │  has_topic: 1 ← Menção literal do tema no verso                 │    │    │
│  │  │  ctx: 0.7    ← Score de contexto                                │    │    │
│  │  │                                                                 │    │    │
│  │  │  final_score = weighted_combination(sem, lex, ref, has_topic)   │    │    │
│  │  │  decision = "accept" if final_score >= threshold else "reject"  │    │    │
│  │  │                                                                 │    │    │
│  │  │  🔥 MELHORIAS PLANEJADAS:                                       │    │    │
│  │  │  • Usar embeddings text-embedding-3-large do bible-api          │    │    │
│  │  │  • Adicionar cross-reference score                              │    │    │
│  │  │  • Scoring por proximidade de versículos                        │    │    │
│  │  │  • NLP entities como boost                                      │    │    │
│  │  └─────────────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  STATUS: ~21 arquivos de verse_links gerados                                   │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                      FASE 6: RESOLUÇÃO DE ENTIDADES                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  Scripts: entity_service.py, entity_gazetteer.py                        │    │
│  │                                                                         │    │
│  │  INPUT:  Entidades extraídas da IA                                      │    │
│  │  OUTPUT: Entidades no Gazetteer com bible_refs                          │    │
│  │                                                                         │    │
│  │  Fluxo:                                                                 │    │
│  │  1. Entidade extraída → verificar se já existe                          │    │
│  │  2. Se existe → atualizar aliases/bible_refs                            │    │
│  │  3. Se não existe → entity_inbox.jsonl (revisão)                        │    │
│  │  4. Auto-accept se alta confiança                                       │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
│                                                                                 │
│  STATUS: 120 entidades no gazetteer | 1034 no inbox                            │
└─────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         OUTPUTS FINAIS / INTEGRAÇÃO                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │  📁 unified/topics/A-Z/*.json     → Tópicos enriquecidos                │    │
│  │  📁 unified/themes/               → Catálogo de temas canônicos         │    │
│  │  📁 unified/verse_links/          → Links versículo→tema                │    │
│  │  📁 nlp_gazetteer/                → Entidades para NLP                  │    │
│  │                                                                         │    │
│  │  🔗 INTEGRAÇÃO COM HYBRID_SEARCH:                                       │    │
│  │  • NLP Tool detecta entidades → boost na busca                          │    │
│  │  • bible_refs → filtragem direta de versículos                         │    │
│  │  • verse_links → expansão semântica de busca por tema                   │    │
│  └─────────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## 📂 Estrutura de Arquivos

### Scripts Principais (a copiar para bible-api)

```
scripts/topical/
├── run_topics_pipeline.py      # 🔥 ORQUESTRADOR PRINCIPAL
│
├── # FASE 1: Parsing
├── reference_parser.py         # Parser de referências bíblicas (REGEX)
├── io_utils.py                 # Escrita atômica, JSONL
├── loader.py                   # Carregamento com schema validation
├── normalize.py                # Normalização de labels/slugs
│
├── # FASE 2: Enriquecimento IA
├── ai_enrichment.py            # TopicEnrichmentService (GPT-4o-mini)
│
├── # FASE 3-4: Temas
├── theme_service.py            # Resolução/criação de temas
├── policy_engine.py            # Regras de auto-accept/reject
├── semantic_clustering.py      # Clustering de temas similares
├── phase1_orchestrator.py      # Orquestrador Fase 1
├── phase2_orchestrator.py      # Orquestrador Fase 2
│
├── # FASE 5: Verse Linking
├── phase3_verse_linking.py     # 🔥 CORE DO VERSE LINKING
├── unified_scoring_engine.py   # Motor de scoring multi-dimensional
├── embedding_service.py        # Serviço de embeddings
│
├── # FASE 6: Entidades
├── entity_service.py           # Processamento de entidades
├── entity_gazetteer.py         # Gestão do gazetteer
│
└── # Utilitários
    ├── constants.py            # Constantes globais
    ├── id_generators.py        # Geração de IDs
    ├── index_builder.py        # Rebuild de índices
    └── translation.py          # Tradução de temas (pt/en)
```

### Estrutura de Dados

```
data/processed/topical_references/unified/
├── topics/                     # 4058 tópicos
│   ├── A/
│   │   ├── AARON.json
│   │   ├── ABADDON.json
│   │   └── ...
│   └── Z/
│       └── ...
│
├── themes/
│   ├── themes_catalog.json     # 290 temas canônicos
│   ├── themes_proposals.jsonl  # 4871 proposals
│   ├── promoted_themes.json    # Histórico de promoções
│   └── verse_themes.jsonl      # Links rápidos
│
├── verse_links/                # Links versículo→tema
│   ├── faith_verse_links.json
│   ├── salvation_verse_links.json
│   └── ...
│
├── canonical/
│   └── verse_links/            # Links canônicos validados
│
├── enrichment/
│   ├── enrichment_runs.jsonl   # Log de 568 execuções IA
│   └── ai_prompt_templates/    # Templates GPT-4o-mini
│
├── metadata/
│   ├── master_index.json       # Índice de 3878 tópicos
│   ├── entity_inbox.jsonl      # 1034 entidades pendentes
│   ├── themes_policies.json    # Regras de governança
│   └── entities_policies.json  # Regras para entidades
│
└── schemas/                    # JSON Schemas v3.0
    └── ...
```

---

## 🔧 Configuração dos Scores (Verse Linking)

### Scoring Atual (v2)

```python
SCORE_WEIGHTS = {
    "semantic": 0.40,      # Similaridade de embeddings
    "lexical": 0.15,       # Palavras em comum
    "reference": 0.25,     # Referência direta do tópico
    "has_topic": 0.10,     # Menção literal
    "context": 0.10        # Score de contexto
}

THRESHOLDS = {
    "accept": 0.35,        # Score mínimo para aceitar
    "reject": 0.20,        # Score máximo para rejeitar
    "review": (0.20, 0.35) # Range para revisão manual
}
```

### Melhorias Planejadas (v3)

```python
SCORE_WEIGHTS_V3 = {
    "semantic": 0.35,      # text-embedding-3-large
    "lexical": 0.10,       # Manter
    "reference": 0.20,     # Referência direta
    "has_topic": 0.10,     # Menção literal
    "cross_ref": 0.10,     # NOVO: cross-references existentes
    "nlp_entity": 0.10,    # NOVO: boost se entidade detectada
    "proximity": 0.05      # NOVO: versículos próximos
}

# Usar embeddings do bible-api (text-embedding-3-large)
# Já temos 926K+ versículos com embeddings de alta qualidade
```

---

## 🚀 Plano de Execução

### Etapa 1: Copiar Scripts
```bash
# Criar estrutura no bible-api
mkdir -p bible/topical_pipeline/scripts
mkdir -p bible/topical_pipeline/data
```

### Etapa 2: Adaptar para Django
- Converter scripts standalone para management commands
- Integrar com modelos existentes (Verse, Book, CrossReference)
- Usar embeddings já gerados

### Etapa 3: Executar Pipeline
```bash
# Fase 1: Parsing (se necessário reprocessar)
python manage.py topical_parse --all

# Fase 2: Enriquecimento IA (incremental)
python manage.py topical_enrich --limit=100

# Fase 3-4: Discovery + Promoção de Temas
python manage.py topical_discover_themes

# Fase 5: Verse Linking (COM NOVOS EMBEDDINGS)
python manage.py topical_verse_link --theme=salvation

# Fase 6: Resolução de Entidades
python manage.py topical_resolve_entities
```

---

## 📊 Métricas de Qualidade

### Indicadores de Sucesso

| Métrica | Atual | Meta |
|---------|-------|------|
| Tópicos enriquecidos | 14% | 100% |
| Temas no catálogo | 290 | 500+ |
| Proposals processadas | 0% | 100% |
| Verse links gerados | ~21 | 290+ |
| Entidades com bible_refs | ~5 | 120+ |

### Qualidade do Verse Linking

```python
# Métricas por tema
{
    "theme": "salvation",
    "total_verses_evaluated": 1000,
    "accepted": 31,
    "rejected": 969,
    "acceptance_rate": 3.1%,  # Esperado: seletivo
    "avg_final_score": 0.45,
    "top_verse": {
        "ref": "Acts 4:12",
        "score": 0.85
    }
}
```

---

## 🔗 Integração com Hybrid Search

### Fluxo de Busca com Pipeline

```
Usuário busca: "Sermão do Monte"
            │
            ▼
┌─────────────────────────────────────┐
│  NLP Tool detecta entidade          │
│  → "Sermao_do_Monte" (LITERARY_WORK)│
│  → bible_refs: [MAT.5, MAT.6, MAT.7]│
└─────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────┐
│  Hybrid Search                      │
│  → Semantic: embedding similarity   │
│  → Lexical: FTS com sinônimos       │
│  → Filter: versículos de MAT 5-7    │
│  → Boost: tema "sermon" detectado   │
└─────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────┐
│  Resultados                         │
│  1. Mateus 5:1-2 (score: 0.95)      │
│  2. Mateus 5:3-12 (score: 0.92)     │
│  3. Mateus 6:9-13 (score: 0.88)     │
│  ...                                │
└─────────────────────────────────────┘
```

---

## 📝 Notas de Implementação

### Dependências
- spaCy (pt_core_news_lg)
- OpenAI API (GPT-4o-mini)
- scikit-learn (clustering)
- Django ORM (modelos)
- pgvector (embeddings)

### Variáveis de Ambiente
```bash
OPENAI_API_KEY=sk-...
```

### Comandos Úteis
```bash
# Ver status do pipeline
python manage.py topical_status

# Processar tópico específico
python manage.py topical_process --topic=AARON

# Processar todos (batch)
python manage.py topical_process --all --batch-size=50
```

---

**Versão:** 1.0
**Última atualização:** 2025-11-30
**Autor:** Pipeline migrado de bible-api-old
