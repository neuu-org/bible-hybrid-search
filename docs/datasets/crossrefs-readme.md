# Cross-References Dataset

Este diretório contém os datasets e scripts para processamento de referências cruzadas bíblicas.

## Estrutura

```
cross_references/
├── raw/                              # Dados originais (não processar diretamente)
│   ├── cross_references.txt          # Dataset OpenBible (TSV format)
│   └── bible-cross-reference-json/   # Dataset SoulLiberty (JSON shards)
│       ├── 1.json
│       ├── 2.json
│       └── ... (33 arquivos)
├── processed/                        # Dados processados e consolidados
│   ├── combined_crossrefs.json       # Referências cruzadas mescladas
│   └── summary.json                  # Estatísticas do processamento
└── unified/                          # Formato unificado para uso em aplicações
    ├── indexes/
    ├── metadata/
    └── verses/
```

## Datasets

### 1. OpenBible Cross References
- **Arquivo**: `raw/cross_references.txt`
- **Formato**: TSV (Tab-Separated Values)
- **Estrutura**: `From Verse | To Verse | Votes`
- **Exemplo**: `Gen.1.1	Isa.65.17	6`
- **Total**: ~343.611 linhas
- **Fonte**: [OpenBible.info](https://www.openbible.info)
- **Licença**: CC-BY 2016

### 2. SoulLiberty Cross References
- **Arquivo**: `raw/bible-cross-reference-json/`
- **Formato**: JSON (33 shards)
- **Estrutura**: 
  ```json
  {
    "verse_id": {
      "v": "GEN 1 1",
      "r": {
        "1": "JOH 1 1",
        "2": "REV 4 11"
      }
    }
  }
  ```
- **Total**: ~500.000+ referências
- **Fonte**: [github.com/noinkling/bible-cross-reference-json](https://github.com/noinkling/bible-cross-reference-json)

## Uso

### Processar Referências Cruzadas

Execute o script de merge para consolidar os datasets:

```bash
cd Commands/cross_references
python merge_crossrefs.py
```

**Opções disponíveis:**
```bash
python merge_crossrefs.py \
    --openbible ../../Datasets/cross_references/raw/cross_references.txt \
    --souliberty ../../Datasets/cross_references/raw/bible-cross-reference-json \
    --out-json ../../Datasets/cross_references/processed/combined_crossrefs.json \
    --out-summary ../../Datasets/cross_references/processed/summary.json
```

### Formato de Saída

#### combined_crossrefs.json
```json
{
  "GEN.1.1": [
    {
      "to": "JHN.1.1",
      "votes": 45,
      "score": 47,
      "sources": {
        "openbible": {"votes": 45},
        "souliberty": {"present": true}
      }
    }
  ]
}
```

#### summary.json
```json
{
  "total_edges": 1234567,
  "total_sources": 31102,
  "avg_refs_per_source": 39.67,
  "datasets_present": ["openbible", "souliberty"]
}
```

## Normalização

O script realiza as seguintes normalizações:

1. **Formato OSIS**: Todas as referências são convertidas para `BOOK.CHAPTER.VERSE`
   - Exemplo: `Genesis 1:1` → `GEN.1.1`
   
2. **Expansão de Ranges**: Intervalos são expandidos em versículos individuais
   - Exemplo: `Gen.1.1-Gen.1.3` → `GEN.1.1`, `GEN.1.2`, `GEN.1.3`

3. **Aliases de Livros**: Múltiplas variações são normalizadas
   - `Genesis`, `Gen`, `GEN`, `Gn` → `GEN`
   - `1 Corinthians`, `1Cor`, `1CO` → `1CO`

4. **Simetria**: Referências são bidirecionais
   - Se A referencia B, então B também referencia A

5. **Score de Relevância**: Calculado como `votes + número_de_fontes`

## Estatísticas

Baseado no último processamento:

- **Versículos com referências**: ~31.000
- **Total de ligações**: ~1.200.000+
- **Média de referências por versículo**: ~39
- **Datasets consolidados**: 2 (OpenBible + SoulLiberty)

## Integração

### Usando em Python

```python
import json
from pathlib import Path

# Carregar referências cruzadas
crossrefs_path = Path("processed/combined_crossrefs.json")
with crossrefs_path.open("r", encoding="utf-8") as f:
    crossrefs = json.load(f)

# Obter referências de um versículo
verse = "GEN.1.1"
if verse in crossrefs:
    refs = crossrefs[verse]
    for ref in refs[:5]:  # Top 5 referências
        print(f"{ref['to']} (score: {ref['score']}, votes: {ref['votes']})")
```

### Filtrar por Score

```python
# Obter apenas referências com score alto
min_score = 10
high_quality_refs = [
    ref for ref in crossrefs.get("GEN.1.1", [])
    if ref["score"] >= min_score
]
```

## Manutenção

### Atualizar Datasets

1. Baixar novos datasets e colocar em `raw/`
2. Executar `merge_crossrefs.py`
3. Verificar `summary.json` para validar resultados

### Backup

Recomendado fazer backup periódico de:
- `processed/combined_crossrefs.json`
- `processed/summary.json`

## Referências

- [OpenBible Cross-References](https://www.openbible.info/labs/cross-references/)
- [SoulLiberty Bible Cross-Reference JSON](https://github.com/noinkling/bible-cross-reference-json)
- [OSIS Bible Reference Standard](http://crosswire.org/osis/)

## TODO

- [ ] Adicionar dataset Treasury of Scripture Knowledge (TSK)
- [ ] Implementar cache para buscas frequentes
- [ ] Criar índice invertido para busca rápida
- [ ] Adicionar filtros por testamento/livro
- [ ] Gerar visualizações de rede de referências
