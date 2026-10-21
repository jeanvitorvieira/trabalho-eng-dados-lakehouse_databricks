# Trabalho Prático: Lakehouse com Databricks Free Edition e Arquitetura Medalhao

## Descrição do trabalho

Este projeto tem como objetivo implementar um pipeline de dados no Databricks implementando a arquitetura Medalhão (Landing -> Bronze -> Silver -> Gold) com Jobs & Pipelines.

A solução utiliza um banco de dados relacional ou não relacional como fonte, o Databricks como plataforma PaaS e o Delta Lake para armazenamento transacional.

O pipeline é organizado em quatro camadas principais:

- **Landing:** armazena os dados extraídos da origem em arquivos CSV/JSON.
- **Bronze:** armazena os dados no formato Delta Lake sem transformações.
- **Silver:** armazena os dados limpos com Data Quality aplicada.
- **Gold:** armazena tabelas dimensionais no modelo Ralph Kimball.

### Fluxo de dados

```text
Fonte de Dados
    |
    | Extração
    v
Databricks - LANDING
    |
    | Leitura e escrita Delta
    v
Databricks - BRONZE
    |
    | Data Quality
    v
Databricks - SILVER
    |
    | Modelagem Dimensional
    v
Databricks - GOLD
```

## Documentação

A documentação técnica do projeto está disponível em [GitHub Pages] (a configurar).

## Pré-requisitos

- Databricks Free Edition
- Python 3.11+
- Poetry
- Git

## Instalação

### 1. Clonar o repositório

```bash
git clone <repo-url>
cd trabalho-eng-dados-lakehouse_databricks
```

### 2. Configurar as variáveis de ambiente

```bash
cp .env.example .env
```

Preencha as variáveis conforme as configurações do Databricks e da fonte de dados.

### 3. Instalar as dependências

```bash
poetry install
```

## Execução dos notebooks

Os notebooks devem ser executados na ordem correspondente às etapas do pipeline.

1. **00_initial_setup:** preparar conexões
2. **01_landing_extraction:** extrair dados para LANDING
3. **02_landing_to_bronze:** converter para Delta no BRONZE
4. **03_bronze_to_silver:** aplicar Data Quality
5. **04_silver_to_gold:** modelagem dimensional Kimball
6. **05_cleanup:** limpeza de ambiente

### Execução via Jobs no Databricks

O projeto inclui `databricks_jobs.json` com a definição do Job encadeado. Passo a passo:

1. Importe o repositório para Databricks Repos mantendo a estrutura `src/lakehouse_databricks/notebooks/`
2. Abra Databricks > Workflows > Jobs > New Job
3. Selecione **Import job JSON** e cole o conteúdo de `databricks_jobs.json`. Ajuste os caminhos `/Repos/seu-usuario/...` para o seu usuário/repo.
4. Configure o cluster com runtime Databricks Runtime 14.x+ com Delta Lake habilitado
5. Salve e execute o Job. As tasks respeitam as dependências 00 → 01 → 02 → 03 → 04 → 05

Veja `docs/jobs_pipeline.md` para detalhes completos.

## Estrutura do repositório

```
trabalho-eng-dados-lakehouse_databricks/
├── data/                   # Dados de exemplo
├── docs/                   # Documentação MkDocs
├── src/
│   └── lakehouse_databricks/
│       ├── notebooks/      # Notebooks do pipeline
│       └── database/       # Scripts DDL
├── tests/                  # Testes
├── .env.example
├── .gitignore
├── mkdocs.yml
├── poetry.lock
└── pyproject.toml
```

## Referências

- [Databricks](https://www.databricks.com/)
- [Delta Lake](https://delta.io/)
- [Arquitetura Medalhao](https://www.databricks.com/glossary/medallion-architecture)
- [Poetry](https://python-poetry.org/)
