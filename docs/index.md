# Lakehouse com Databricks e Arquitetura Medalhao

## Visão geral

Este projeto implementa um pipeline de dados utilizando a arquitetura Medalhao no Databricks Free Edition.

### Fluxo visual

```mermaid
flowchart LR
    Fonte[(Fonte PostgreSQL)] --> Landing[(LANDING CSV)]
    Landing --> Bronze[(BRONZE Delta)]
    Bronze --> Silver[(SILVER Delta + DQ)]
    Silver --> Gold[(GOLD Dimensional)]
    Gold --> BI[Relatórios/BI]
```

### Camadas

- **Landing**: dados brutos extraídos da fonte em CSV
- **Bronze**: dados em Delta Lake sem transformação de conteúdo
- **Silver**: dados limpos, tipados e com Data Quality
- **Gold**: tabelas dimensionais Kimball

### Tabelas da fonte

| Tabela na fonte | Arquivo na landing | Tabela bronze / silver |
|---|---|---|
| `games` | `games.csv` | `metacritic_games` |
| `steam_metrics` | `steam_metrics.csv` | `steam_metrics` |
| `regional_sales` | `regional_sales.csv` | `regional_sales` |
| `how_long_to_beat` | `how_long_to_beat.csv` | `how_long_to_beat` |
| `walkthrough_links` | `walkthrough_links.csv` | `walkthrough_links` |

## Estrutura

Consulte a documentação nas seções ao lado.
