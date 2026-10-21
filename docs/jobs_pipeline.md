# Jobs e Pipeline

Encadeamento dos notebooks via Jobs & Pipelines do Databricks.

## Visão geral

O pipeline Medalhão é executado como um Job com 5 tasks sequenciais:

```text
00_preparing_environment → 01_database_to_landing → 02_landing_to_bronze → 03_bronze_to_silver → 04_silver_to_gold
```

O notebook `CLEAN_WORKSPACE` **não** entra no Job: ele apaga todos os schemas e é executado manualmente quando se quer recomeçar do zero.

## Pré-requisitos

- Repositório importado como Git folder no Databricks
- Compute serverless (padrão do Free Edition, não é necessário configurar cluster)
- Secret Scope `lakehouse` com as credenciais da fonte (ou parâmetros do Job `db_host`, `db_port`, `db_name`, `db_user`, `db_password`)

## Passo a passo para criar o Job

1. **Importar repositório**
   - Workspace > Create > Git folder
   - URL do repositório GitHub do trabalho

2. **Ajustar `databricks_jobs.json`**
   - Abra `databricks_jobs.json` na raiz do projeto
   - Substitua `/Repos/seu-usuario/...` pelo caminho real do Git folder

3. **Criar o Job**
   - Jobs & Pipelines > Create job
   - Edite como JSON e cole o conteúdo de `databricks_jobs.json`
   - Revise os caminhos dos notebooks

4. **Executar**
   - Clique em Run now
   - Acompanhe o fluxo no painel de runs. Cada task aguarda a conclusão da anterior.

## Manutenção

- Para alterar a ordem, edite as dependências `depends_on` no JSON
- Para adicionar regras de Data Quality, atualize o dicionário `RULES` em `03_BRONZE_TO_SILVER.ipynb`
- Para evoluir o modelo dimensional, atualize `04_SILVER_TO_GOLD.ipynb`

Arquivo de definição: `databricks_jobs.json`
