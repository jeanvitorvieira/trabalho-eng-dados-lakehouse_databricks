# Bronze para Silver

Aplicação de Data Quality e transformações (`03_BRONZE_TO_SILVER`).

!!! success "Objetivo"
    Transformar dados brutos em dados prontos para análise: padronizar nomes, corrigir tipos, remover duplicados e aplicar regras de domínio.

## Regras aplicadas

| Regra | Detalhe |
|---|---|
| Nomes de colunas | MAIÚSCULAS, snake_case, sem caracteres especiais. Prefixos regionais `NA_`, `EU_`, `JP_`, `OTHER_` viram `NORTH_AMERICA_`, `EUROPE_`, `JAPAN_`, `OTHER_REGIONAL_` (só no início do nome) |
| Colunas técnicas | `DATETIME_BRONZE` e `FILENAME` são removidas |
| Texto | `trim`; vazio, `tbd`, `n/a`, `null`, `none`, `nan` viram `NULL` |
| Listas em texto | `['Humble Games']` vira `Humble Games` (coluna `PUBLISHER` de `metacritic_games`) |
| Tipagem | colunas string em que todos os valores são inteiros, decimais ou datas `yyyy-MM-dd` são convertidas com `try_cast` |
| Faixas válidas | `METASCORE` 0 a 100 e `USER_SCORE` 0 a 10; fora disso vira `NULL` |
| Chaves | linhas sem `ID` ou `NAME` são descartadas |
| Duplicados | removidos por `ID` (`metacritic_games`) ou pela linha inteira (demais tabelas) |
| Auditoria | `FILENAME_BRONZE` (tabela de origem) e `FILEDATE_SILVER` (data/hora do processamento) |

> O compute serverless roda com ANSI mode ligado: um `CAST` inválido gera erro. Por isso a conversão de tipos usa `try_cast`, que devolve `NULL` em vez de derrubar o job.

## Relatório de qualidade

Para cada tabela o notebook imprime `linhas_bronze`, `linhas_sem_chave_removidas`, `duplicadas_removidas` e `linhas_silver`.

## Adicionando regras

As regras específicas ficam no dicionário `RULES` do notebook (`keys`, `not_null`, `ranges`, `list_cols`). Para as demais tabelas basta preencher o dicionário correspondente.
