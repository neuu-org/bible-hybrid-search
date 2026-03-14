# Fases do Pipeline e Estrategia de Embeddings

## Objetivo
Este documento consolida:
- o papel das fases `0`, `1`, `2` e `3` do pipeline atual;
- onde a pesquisa de busca hibrida entra;
- como os embeddings `small`, `large`, `unified/merge` e embeddings de livros podem fortalecer o TCC.

## Estado real atual
O pipeline existe, mas ainda nao foi executado de ponta a ponta sobre todo o corpus.

Estado observado no workspace:
- corpus bruto completo: `data/dataset/topics_v3/`
- escala documental do V3: `7.873` topicos
- piloto enriquecido atual: `scripts/topical_pipeline/data/topics_v3/`
- escala observada do piloto: `336` topicos
- distribuicao atual do piloto: `A=334`, `C=1`, `D=1`
- cobertura observada no piloto:
  - `326/336` com `ai_enrichment`
  - `333/336` com `ai_themes_normalized`
  - `231/336` com `phase1_discovery`

Consequencia metodologica:
- o TCC nao deve tratar o sistema atual como corpus completo processado
- o recorte experimental inicial deve ser assumido como `piloto parcial`
- a letra `A` e o melhor slice principal para reporte nesta sprint

## Recorte oficial de pesquisa
O objeto principal do TCC nao e `todo o pipeline ao mesmo tempo`.
O recorte mais forte e:
- `busca hibrida`
- `busca semantica`
- `vinculacao tema-versiculo`

Leitura correta:
- `Phase 0` e `Phase 1` sustentam o pipeline
- `Phase 2` e `Phase 3` sustentam o experimento principal

## O que esta fora do nucleo
Estes dominios seguem sendo valiosos, mas entram como apoio:
- gazetteers
- simbolos
- entidades
- definicoes de dicionario e `definition_refs`
- comentarios como corpus de validacao
- embeddings de livros como extensao metodologica

Eles fortalecem a pesquisa, mas nao devem virar frentes paralelas descontroladas na mesma sprint.

## Visao geral do pipeline
O pipeline atual transforma `topicos brutos` em `temas ligados a versiculos`.

Fluxo logico:
1. `Phase 0`: enriquecer o topico e extrair sinais semanticos.
2. `Phase 1`: resolver os temas extraidos contra o catalogo existente.
3. `Phase 2`: decidir se propostas de tema merecem promocao.
4. `Phase 3`: vincular temas promovidos aos versiculos mais relevantes.

Fluxo de dados:
- Fonte bruta principal: `data/dataset/topics_v3/`
- Subconjunto enriquecido do piloto: `scripts/topical_pipeline/data/topics_v3/`
- Catalogo de temas: `scripts/topical_pipeline/data/themes/themes_catalog.json`
- Temas promovidos: `scripts/topical_pipeline/data/themes/promoted_themes.json`

## Phase 0 - AI Enrichment
### Entrada
- topicos brutos do dataset V3
- definicoes, referencias biblicas, grupos de referencias e metadados do topico

### O que faz
- gera `ai_enrichment`
- extrai `ai_themes_normalized`
- extrai `ai_entities`
- extrai `ai_discovered_aspects`
- extrai `ai_relationships`

### Saida
- topico enriquecido salvo em `scripts/topical_pipeline/data/topics_v3/`

### Funcao no TCC
Esta fase nao e o foco principal da pesquisa de busca hibrida.
Ela existe para preparar a materia-prima semantica que alimenta as fases seguintes.

Observacao importante:
- as `definitions` e `definition_refs` do V3 nao sao geradas pela IA desta fase
- elas ja chegam do dataset consolidado e coexistem com o enrichment
- portanto, funcionam como camada enciclopedica auxiliar dentro do topico enriquecido

### Pergunta que ela responde
- `o que existe semanticamente dentro deste topico?`

## Phase 1 - Theme Discovery / Resolution
### Entrada
- `ai_themes_normalized` produzidos na Phase 0
- catalogo canonico de temas
- embeddings do rotulo consultado

### O que faz
- compara o rotulo proposto com temas existentes
- decide entre:
  - `alias_existing`
  - `proposed`

### Saida
- `phase1_discovery` no topico
- `themes_proposals.jsonl`
- `alias_suggestions.jsonl`

### Funcao no TCC
Tambem nao e o foco principal da avaliacao de busca hibrida.
Ela funciona como filtro semantico inicial entre `tema conhecido` e `tema candidato`.

### Pergunta que ela responde
- `isso ja e um tema conhecido ou e um tema novo?`

## Phase 2 - Theme Promotion
### Entrada
- temas `proposed` da Phase 1
- catalogo de temas
- evidencias de cobertura biblica recuperadas por busca

### O que faz
- usa busca hibrida para buscar versiculos relevantes para o tema
- mede cobertura, forca semantica e qualidade
- decide se o tema deve ser promovido a tema canonico

### Saida
- atualizacao do `themes_catalog.json`
- atualizacao de `promoted_themes.json`

### Funcao no TCC
Aqui a pesquisa entra de forma direta.
Nesta fase, a busca hibrida influencia a decisao de promocao.

### Pergunta que ela responde
- `este tema encontra evidencia biblica suficiente e consistente para virar tema canonico?`

### Como validar nesta fase
- cobertura por livros
- quantidade de versiculos relevantes por tema
- forca media dos scores
- concordancia com crossrefs
- concordancia com comentarios
- estabilidade da promocao quando mudam os parametros da busca

### Resultado que interessa para o TCC
Nesta fase, a pergunta nao e apenas `o sistema encontrou versiculos?`.
A pergunta correta e:
- `a configuracao de retrieval gera evidencia suficientemente forte para justificar a promocao do tema?`

## Phase 3 - Verse Linking
### Entrada
- temas promovidos pela Phase 2

### O que faz
- executa busca para recuperar versiculos relevantes
- aplica filtros de qualidade
- pode usar deduplicacao e diversificacao
- persiste os links finais tema-versiculo

### Saida
- assignments finais entre tema e versiculos
- base para navegacao tematica, analytics e validacao

### Funcao no TCC
Aqui a pesquisa tambem entra de forma direta.
Nesta fase, a busca hibrida determina a qualidade do ranking final.

### Pergunta que ela responde
- `quais versiculos representam melhor este tema?`

### Como validar nesta fase
- Precision@K
- Recall@K
- MAP
- NDCG
- latencia
- diversidade dos resultados
- taxa de duplicacao entre versoes
- concordancia com crossrefs e comentarios

### Resultado que interessa para o TCC
Nesta fase, a pergunta correta e:
- `qual configuracao entrega o melhor ranking final para representar um tema?`

## Onde a pesquisa de busca hibrida se encaixa
O objeto principal da pesquisa esta entre `Phase 2` e `Phase 3`.

Leitura correta:
- `Phase 0` e `Phase 1` = preparacao semantica
- `Phase 2` = avaliacao de retrieval para promocao
- `Phase 3` = avaliacao de retrieval para linking final

Em termos de TCC:
- fases `0` e `1` sustentam o pipeline
- fases `2` e `3` sustentam o experimento principal

## Ativos de embeddings que o projeto possui
## Ativo auxiliar: definicoes de dicionario e referencias extraidas
O V3 tambem carrega um bloco enciclopedico importante:
- `definitions`
- `definition_refs`
- `sources`

Fontes observadas no dataset e no modelo:
- `EAS`: Easton's Bible Dictionary
- `SMI`: Smith's Bible Dictionary
- `NAV`: Nave's Topical Bible
- `TOR`: Torrey's Topical Textbook

Trilha no sistema:
- aparecem no JSON V3
- sao preservadas no subset enriquecido
- sao importadas pelo modelo `TopicDefinition`
- podem ser expostas por endpoint de definicoes

Valor metodologico:
- adicionam contexto textual mais estavel que a camada gerada por IA
- ajudam a explicar por que um topico existe e como ele foi definido
- podem virar sinal auxiliar de validacao semantica
- podem ajudar a detectar referencias biblicas relevantes associadas a um topico

Uso recomendado nesta sprint:
- nao usar como eixo principal do benchmark
- usar como apoio para curadoria de queries, interpretacao de temas e estudos de caso

Uso recomendado em etapas seguintes:
- usar `definition_refs` como weak supervision
- verificar se versiculos recuperados pela busca coincidem com referencias extraidas das definicoes
- comparar se resultados semanticamente recuperados estao alinhados com o escopo enciclopedico do topico

## 1. Verse embeddings por versao
Base principal:
- `VerseEmbedding`
- embeddings `small`
- embeddings `large`
- cobertura historica documentada de 17 versoes e 1M+ embeddings

Valor metodologico:
- permite comparar recuperacao por versao
- permite separar `recall` rapido de `reranking` mais preciso
- permite testar diferencas entre portugues e ingles

Evidencia historica ja documentada externamente:
- milestone de `1.057.990` embeddings
- `528.995` versiculos embedados
- `17` versoes biblicas
- dual strategy com `text-embedding-3-small` e `text-embedding-3-large`

Perguntas de pesquisa possiveis:
- `small` sozinho recupera melhor custo-beneficio?
- `large` melhora o ranking final de forma mensuravel?
- o ganho de qualidade compensa a latencia adicional?

## 2. Unified / merged verse embeddings
Base principal:
- `UnifiedVerseEmbedding`
- fusao canonica de multiplas versoes
- `fusion_strategy = weighted_average`
- versoes-fonte em portugues agregadas no embedding canonico

Valor metodologico:
- reduz variacao causada por traducao especifica
- permite comparar `por versao` versus `representacao canonica fundida`
- permite estudar robustez semantica entre traducoes
- oferece rota mais rapida de retrieval

Ponto forte para o TCC:
- o merge transforma um problema de `traducao especifica` em um problema de `representacao canonica`
- isso permite testar se a busca melhora quando a unidade de comparacao deixa de ser a traducao isolada e passa a ser o verso consolidado

Perguntas de pesquisa possiveis:
- a fusao de versoes melhora robustez conceitual?
- embeddings unificados preservam qualidade com menor custo?
- o merge perde detalhes lexicais importantes de uma versao especifica?

## 3. Small vs large em dois estagios
O sistema atual ja sugere esta estrategia:
- `small` para recuperar candidatos
- `large` para reranquear

Valor metodologico:
- permite desenhar uma pesquisa de `two-stage retrieval`
- aproxima o TCC de problemas reais de IR moderno

Hipotese natural:
- `small` e melhor para cobertura e custo
- `large` e melhor para precisao no topo do ranking
- a combinacao dos dois pode ser superior a usar apenas um deles

## 4. Unified theme embeddings
Base principal:
- `UnifiedThemeEmbedding`
- embeddings unificados de temas derivados de multiplos contextos

Valor metodologico:
- melhora comparacao semantica entre temas e versiculos
- pode fortalecer a avaliacao da Phase 2
- permite medir proximidade tema-tema e tema-versiculo com menos ruido local

Perguntas de pesquisa possiveis:
- temas unificados geram promocao mais estavel?
- embeddings de tema melhoram a explicabilidade da promocao?

## 5. Embeddings de livros
Observacao:
- este ativo foi reportado pelo projeto como existente fora do recorte principal do repositorio
- ele deve ser tratado como artefato experimental complementar

Valor metodologico:
- permite retrieval hierarquico
- permite classificar primeiro o `livro/corpus` mais provavel e depois o versiculo
- permite medir coerencia macrotematica por livro
- ajuda a explicar resultados em nivel mais alto que o versiculo

Valor adicional para a escrita:
- ajuda a mostrar que a pesquisa pode operar em camadas
- `livro -> tema -> versiculo` e uma narrativa metodologica mais rica do que apenas `query -> versiculo`

Risco:
- se essa trilha nao estiver reproduzivel hoje, ela deve ficar como extensao controlada e nao como centro da primeira rodada experimental

Perguntas de pesquisa possiveis:
- embeddings de livros podem servir como etapa de roteamento antes da busca de versiculos?
- um prior por livro melhora precisao para consultas doutrinarias ou narrativas?
- o ranking de livros ajuda a explicar melhor por que certos versiculos aparecem?

## Como isso fortalece a pesquisa
Esses ativos permitem transformar a pesquisa em comparacao metodologica real, nao apenas em descricao de dataset.

Linhas fortes de validacao:
1. `small` vs `large`
2. `verse` vs `unified`
3. `single-stage` vs `two-stage reranking`
4. `versiculo` vs `livro -> versiculo`
5. `versao especifica` vs `representacao canonica fundida`

## Papel dos comentarios e do CCEL na validacao
Os comentarios nao sao apenas conteudo adicional.
Eles podem funcionar como fonte de validacao externa.

Camadas disponiveis:
- `high coverage`: conjunto amplo de comentarios extraidos
- `high precision`: subconjunto mais rigoroso, util como referencia forte

Como isso entra na pesquisa:
- validar se os resultados da busca concordam com passagens comentadas
- medir se links tema-versiculo recuperados aparecem em comentarios relevantes
- usar o conjunto `precision` como gold set mais confiavel
- usar o conjunto `coverage` como sinal complementar de abrangencia

Perguntas de pesquisa que isso abre:
- configuracoes mais semanticas recuperam melhor passagens comentadas?
- configuracoes mais lexicais recuperam melhor passagens explicitamente citadas?
- o reranking com `large` aproxima mais os resultados da tradicao exegtica?

## Papel das referencias cruzadas
As referencias cruzadas servem como outra camada de verdade de referencia.

Uso no TCC:
- medir concordancia com crossrefs manuais
- medir taxa de descoberta de links plausiveis que nao estavam nas crossrefs originais
- comparar `precision conservadora` versus `novidade plausivel`

Interpretacao:
- concordancia alta indica aderencia a estruturas tradicionais
- novidade plausivel indica potencial de descoberta semantica real

## Papel das definicoes e `definition_refs`
As definicoes de dicionario nao substituem crossrefs nem comentarios, mas podem contribuir em tres frentes:

1. Curadoria de consulta
- ajudam a formular queries melhores para topicos e entidades
- ajudam a entender o escopo semantico de um topico antes do benchmark

2. Interpretabilidade
- ajudam a explicar por que um tema ou topico foi considerado relevante
- ajudam a sustentar estudos de caso qualitativos na escrita do TCC

3. Validacao fraca
- `definition_refs` podem funcionar como referencia auxiliar
- se a busca recuperar versiculos proximos das referencias extraidas da definicao, isso reforca a plausibilidade do resultado

Limitacao:
- definicoes enciclopedicas descrevem o topico, mas nem sempre representam um gold set robusto de ranking
- por isso, devem entrar como `sinal auxiliar` e nao como unica verdade de referencia

## O que o merge/unified pode trazer para a validacao
O merge de embeddings de versiculos e um dos pontos mais fortes do projeto para pesquisa.

Ele permite testar:
- robustez entre traducoes
- reducao de ruido de versao
- eficiencia com menos candidatos
- perda ou preservacao de detalhes lexicais importantes

Hipoteses testaveis:
- `unified` deve melhorar estabilidade entre consultas conceituais
- `verse` deve ser melhor para consultas muito literais ou dependentes de formulacao exata
- `small + unified` pode ter melhor custo-beneficio
- `verse + rerank large` pode ter melhor topo de ranking

## O que significa "melhor" em cada comparacao
`Melhor` nao significa uma unica metrica.
Precisamos separar:

- `Phase 2`
  - melhor = promove temas com evidencia mais consistente
  - sinais: cobertura, forca, concordancia, estabilidade da decisao

- `Phase 3`
  - melhor = entrega ranking mais preciso e explicavel
  - sinais: Precision@K, Recall@K, MAP, NDCG, diversidade, latencia

- `Embeddings`
  - melhor = melhor equilibrio entre qualidade, estabilidade e custo
  - sinais: ranking, robustez entre versoes, tempo de resposta e reproducibilidade

- `Definicoes e definition_refs`
  - melhor = maior capacidade de explicar e corroborar qualitativamente um resultado
  - sinais: alinhamento semantico, coincidencia de referencias, utilidade para estudos de caso

## Matriz de validacao sugerida
| Camada | Comparacao | Onde medir | Metricas |
|-------|------------|------------|----------|
| Recall | `small` vs `large` | Phase 3 | Recall@K, latencia |
| Ranking | `small` vs `small + large rerank` | Phase 3 | Precision@K, MAP, NDCG |
| Robustez entre traducoes | `verse` vs `unified` | Phase 2 e 3 | concordancia, estabilidade, latencia |
| Evidencia para promocao | `alpha/expand/rerank/mmr` | Phase 2 | cobertura, consistencia, concordancia com crossrefs/comentarios |
| Explicabilidade macro | `book embedding + verse retrieval` | Phase 3 ou extensao | coerencia por livro, interpretabilidade |

## Roadmap experimental recomendado
### Etapa 1
- consolidar o piloto oficial atual
- estabilizar benchmark e readiness
- curar queries com `gold_references` e `relevance_judgments`

### Etapa 2
- rodar baseline de busca hibrida
- comparar `alpha`, `expand`, `rerank`, `mmr_lambda`

### Etapa 3
- comparar `embedding_source=verse` e `embedding_source=unified`
- separar resultados por tipo de consulta

### Etapa 4
- cruzar resultados com crossrefs e comentarios
- medir concordancia e novidade plausivel

### Etapa 5
- se a trilha estiver estavel, testar camada de embeddings de livros como extensao

## Limitacoes que devem aparecer no TCC
- o corpus de topicos processado ainda e parcial
- a evidência principal inicial sai de um piloto, nao do universo total
- nem todo dominio do projeto esta igualmente maduro
- comments, gazetteers e embeddings de livros podem estar em maturidades diferentes
- definicoes e `definition_refs` funcionam melhor como validacao auxiliar do que como gold set principal

Essas limitacoes nao enfraquecem o trabalho se forem explicitadas corretamente.
Elas apenas definem o recorte honesto do experimento.

## Implicacoes para escrita do TCC
Na escrita, esses ativos podem sustentar tres contribuicoes claras:

1. Contribuicao de pipeline
- do topico bruto ao tema vinculado semanticamente

2. Contribuicao de retrieval
- comparacao entre configuracoes de busca hibrida

3. Contribuicao de representacao vetorial
- avaliacao do papel de `small`, `large`, `merge/unified` e embeddings de nivel superior

## Decisao recomendada para a sprint atual
Para nao abrir escopo demais:
- manter o experimento principal em `Phase 2` e `Phase 3`
- comparar `small/large/unified` dentro da busca hibrida atual
- tratar embeddings de livros como extensao controlada ou trabalho futuro, a menos que ja exista trilha reproduzivel pronta

## Resumo executivo
- `Phase 0` prepara sinais semanticos
- `Phase 1` decide entre tema existente e tema proposto
- `Phase 2` usa retrieval para promocao de temas
- `Phase 3` usa retrieval para linking final tema-versiculo

- `small` favorece recall e custo
- `large` favorece precisao no topo do ranking
- `unified/merge` favorece robustez entre traducoes e eficiencia
- embeddings de livros podem abrir retrieval hierarquico e explicabilidade macro

- comentarios CCEL e referencias cruzadas fortalecem a validacao externa
- definicoes e `definition_refs` fortalecem contexto, explicabilidade e validacao fraca
- o piloto atual deve ser tratado como base experimental parcial
- o centro cientifico do TCC esta nas fases `2` e `3`
