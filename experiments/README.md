# Hybrid Search Experiments

Este diretório guarda os artefatos reproduzíveis dos benchmarks de busca híbrida.

Estrutura esperada:
- `pilot_queries_v1.json`: conjunto inicial de consultas
- `runs/<timestamp>/plan.json`: plano executado
- `runs/<timestamp>/summary.json`: resultados consolidados
- `runs/<timestamp>/summary.csv`: tabela por query x experimento
- `runs/<timestamp>/aggregate.csv`: métricas agregadas por experimento
- `runs/<timestamp>/raw/<experimento>/<query>.json`: resposta bruta da API

Os arquivos deste diretório devem ser tratados como evidência experimental, não como documentação narrativa.
