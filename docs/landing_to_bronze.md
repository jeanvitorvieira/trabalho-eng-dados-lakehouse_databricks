# Landing para Bronze

Descrição da etapa de ingestão dos dados de Landing para Bronze em Delta Lake.

## 1. Fonte -> Landing (`01_DATABASE_TO_LANDING`)

- Conecta no PostgreSQL via JDBC (`sslmode=require`). As credenciais vêm do Secret Scope `lakehouse` ou de parâmetros do Job.
- Lê cada tabela de origem e grava um CSV com cabeçalho em `/Volumes/workspace/landing/dados/<tabela>.csv`.
- O Spark grava uma pasta `temp_<tabela>`; o notebook copia o arquivo `part-*` para o nome final e apaga a pasta temporária.

## 2. Landing -> Bronze (`02_LANDING_TO_BRONZE`)

- Lê cada CSV com `header=true` e `inferSchema=true`.
- Adiciona duas colunas de metadado:

| Coluna | Conteúdo |
|---|---|
| `datetime_bronze` | data/hora do processamento |
| `filename` | nome do arquivo de origem |

- Grava tabelas Delta MANAGED em `workspace.bronze` com `mode("overwrite")` e `overwriteSchema=true`.

## Validação

```sql
SHOW TABLES IN bronze;
DESCRIBE DETAIL bronze.metacritic_games;
DESCRIBE EXTENDED bronze.metacritic_games;  -- Type = MANAGED
```
