# Jobs e Pipeline

Encadeamento dos notebooks via Jobs & Pipelines do Databricks.

## Visão geral

O pipeline Medalhão é executado como um Job com 6 tasks sequenciais:
00_initial_setup → 01_landing_extraction → 02_landing_to_bronze → 03_bronze_to_silver → 04_silver_to_gold → 05_cleanup

## Pré-requisitos

- Repositório importado no Databricks Repos com caminho `/Repos/<usuario>/trabalho-eng-dados-lakehouse_databricks/src/lakehouse_databricks/notebooks/`
- Cluster com Delta Lake habilitado
- Variáveis de ambiente configuradas no `.env` ou no Secret Scope

## Passo a passo para criar o Job

1. **Importar repositório**
   - Databricks > Repos > New > Import from Git
   - URL do repositório GitHub do trabalho

2. **Ajustar `databricks_jobs.json`**
   - Abra `databricks_jobs.json` na raiz do projeto
   - Substitua `seu-usuario` pelos caminhos reais do Repos

3. **Criar Job via UI**
   - Workflows > Jobs > New Job
   - Clique em **Import job JSON**
   - Cole o conteúdo de `databricks_jobs.json`
   - Revise os notebook paths e o cluster

4. **Configurar parâmetros**
   - Em cada task, defina `base_parameters` se necessário: catalog, schema, DB_SOURCE
   - Habilite notificações e retry conforme desejado

5. **Executar**
   - Clique em Run Now
   - Acompanhe o fluxo no painel de runs. Cada task aguarda a conclusão da anterior.

## Manutenção

- Para alterar a ordem, edite as dependências `depends_on` no JSON
- Para adicionar Data Quality checks, atualize `03_bronze_to_silver.ipynb`
- Para evoluir o modelo dimensional, atualize `04_silver_to_gold.ipynb`

Arquivo de definição: `databricks_jobs.json`
