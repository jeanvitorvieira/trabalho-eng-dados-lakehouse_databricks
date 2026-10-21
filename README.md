# Trabalho Prático: Lakehouse com Databricks Free Edition e Arquitetura Medalhão

## Descrição do trabalho

Este projeto implementa um pipeline de dados no Databricks com a arquitetura Medalhão (Landing -> Bronze -> Silver -> Gold) orquestrado por Jobs & Pipelines.

A fonte é um banco PostgreSQL (Supabase) com dados de jogos (Metacritic, Steam, vendas regionais, HowLongToBeat e links de walkthrough). O Databricks é a plataforma PaaS e o Delta Lake faz o armazenamento transacional.

O pipeline é organizado em quatro camadas:

- **Landing:** dados extraídos da origem em arquivos CSV (volume `workspace.landing.dados`).
- **Bronze:** dados em Delta Lake, sem transformação de conteúdo.
- **Silver:** dados limpos, tipados e com Data Quality aplicada.
- **Gold:** tabelas dimensionais no modelo Ralph Kimball (esquema estrela).

### Fluxo de dados

![fluxo de dados](/data/images/Gemini_Generated_Image_toxsrqtoxsrqtoxs.jpeg)

## Documentação

A documentação técnica fica em `docs/` (MkDocs Material). Para visualizar localmente:

```bash
poetry run mkdocs serve
```

A publicação no GitHub Pages ainda precisa ser configurada (`poetry run mkdocs gh-deploy`).

## Pré-requisitos

- Databricks Free Edition (compute serverless)
- Python 3.11 (só para uso local: documentação e testes)
- Poetry 2.x
- Git

## Instalação

### 1. Clonar o repositório

```bash
git clone <repo-url>
cd trabalho-eng-dados-lakehouse_databricks
```

### 2. Instalar as dependências (uso local)

```bash
poetry lock
poetry install
```

### 3. Instalar o databricks CLI

```bash
curl -fsSL https://raw.githubusercontent.com/databricks/setup-cli/main/install.sh | sh
```

### 4. Conectar ao seu Workspace

```bash
databricks auth login --host https://SEU-WORKSPACE.cloud.databricks.com
```

### 5. Configurar as credenciais da fonte de dados no Databricks

Com a Databricks CLI:

```bash
databricks secrets create-scope lakehouse
databricks secrets put-secret lakehouse db_host
databricks secrets put-secret lakehouse db_port
databricks secrets put-secret lakehouse db_name
databricks secrets put-secret lakehouse db_user
databricks secrets put-secret lakehouse db_password
```

## Execução dos notebooks

Os notebooks ficam em `src/lakehouse_databricks/notebooks/` e rodam na ordem abaixo:

1. **CLEAN_WORKSPACE** apaga todos os schemas e o volume
2. **00_PREPARING_ENVIRONMENT:** cria schemas (landing, bronze, silver, gold) e o volume
3. **01_DATABASE_TO_LANDING:** extrai as tabelas do PostgreSQL para CSV na LANDING
4. **02_LANDING_TO_BRONZE:** converte os CSV para Delta no BRONZE
5. **03_BRONZE_TO_SILVER:** renome de colunas e Data Quality
6. **04_SILVER_TO_GOLD:** modelagem dimensional Kimball

### Execução via Jobs no Databricks

O projeto inclui `databricks_jobs.json` com o Job encadeado:

1. Importe o repositório como Git folder no Databricks, mantendo a estrutura `src/lakehouse_databricks/notebooks/`
2. Abra `databricks_jobs.json` e ajuste os caminhos `/Repos/seu-usuario/...` para o caminho real do seu Git folder (por exemplo `/Workspace/Users/<email>/trabalho-eng-dados-lakehouse_databricks/...`)
3. Crie o Job em Jobs & Pipelines > Create job (edição como JSON) usando o conteúdo do arquivo
4. Salve e execute (Run now). As tasks respeitam as dependências CLEAN -> 00 -> 01 -> 02 -> 03 -> 04

Veja `docs/jobs_pipeline.md` para detalhes.

## Estrutura do repositório

![Estrutura do repositório](/data/images/Gemini_Generated_Image_24dsfi24dsfi24ds(Editado).jpeg)

## Referências

- [Databricks](https://www.databricks.com/)
- [Delta Lake](https://delta.io/)
- [Arquitetura Medalhao](https://www.databricks.com/glossary/medallion-architecture)
- [Poetry](https://python-poetry.org/)
