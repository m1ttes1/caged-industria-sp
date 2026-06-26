# CAGED Indústria SP — Análise de Emprego Formal

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![DuckDB](https://img.shields.io/badge/DuckDB-0.10-FFF000?style=flat-square)](https://duckdb.org)
[![pandas](https://img.shields.io/badge/pandas-2.2-150458?style=flat-square&logo=pandas)](https://pandas.pydata.org)

SQL analysis of formal employment in São Paulo's manufacturing sector using **Novo CAGED** microdata (2021–2024).

## Pergunta

> Como a indústria de transformação em São Paulo se recuperou após 2020?
> Quais municípios lideraram a retomada e que perfil de trabalhador foi mais contratado?

## Dataset

**Novo CAGED** — Cadastro Geral de Empregados e Desempregados (Ministério do Trabalho e Emprego)

| Campo | Valor |
|-------|-------|
| Fonte | MTE / PDET — http://pdet.mte.gov.br/microdados |
| Período | Jan/2021 – Dez/2023 |
| Filtro | UF = 35 (SP) · Seção C (Indústria de Transformação) |
| Volume estimado | ~2M registros/ano |
| Formato | .txt (ponto e vírgula, encoding latin-1) |

## Notebooks

| Notebook | Descrição |
|----------|-----------|
| `01_download_dados.ipynb` | Download Novo CAGED via FTP, filtro SP + Seção C, export Parquet |
| `02_analise_sql.ipynb` | Queries DuckDB: window functions, CTEs, 3 visualizações matplotlib |

## SQL Highlights

**Window function — Saldo acumulado:**
```sql
SELECT
    municipio_nome,
    competencia,
    SUM(saldo_mov) OVER (
        PARTITION BY municipio_nome
        ORDER BY competencia
        ROWS UNBOUNDED PRECEDING
    ) AS saldo_acumulado
FROM caged_sp_industria
```

**CTE — Ranking de recuperação pós-2021:**
```sql
WITH recuperacao AS (
    SELECT
        municipio_nome,
        SUM(saldo_mov) AS saldo_total
    FROM caged_sp_industria
    WHERE competencia >= '2021-06-01'
    GROUP BY municipio_nome
)
SELECT * FROM recuperacao ORDER BY saldo_total DESC
```

## Setup

```bash
pip install -r requirements.txt

# 1. Baixar dados reais (30-60 min, ~2GB)
jupyter notebook notebooks/01_download_dados.ipynb

# 2. Análise SQL + visualizações
jupyter notebook notebooks/02_analise_sql.ipynb
```

> **Nota:** O notebook `02` inclui dataset sintético (~500 rows) para demonstração imediata.
> Para análise completa, execute o notebook `01` primeiro.

## Demo

Visualização interativa disponível em [vmittestainer.github.io](https://vmittestainer.github.io) — seção Projects.
