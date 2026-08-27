# Desperdício Zero — Réplica Analítica

## Contexto

Terceira etapa da trilha de portfólio em Análise de Dados. Réplica analítica do projeto real
**"Desperdício Zero"**, que rodei presencialmente no Assaí Atacadista — apoiando ~150 lojas e
35 inaugurações, reduzindo o percentual de perda de aproximadamente 7% para 2% através de
controles de despesas e acompanhamento de indicadores operacionais.

Este projeto simula esse mesmo cenário com dados fictícios, mas com o desenho de queda real
que o projeto original teve, para praticar SQL orientado a uma pergunta de negócio genuína:
**"o esforço de redução de desperdício está funcionando, e onde ele funciona melhor?"**

## Estrutura

```
├── gerar_dataset_desperdicio.py   # script que gera os dados simulados
├── perdas.csv                      # 2.625 registros: unidade x categoria x mês
├── portfolio_desperdicio.db        # banco SQLite pronto para consultas
└── exercicios_sql.md               # exercícios progressivos com gabarito
```

## Sobre os dados

15 unidades, 7 categorias de produto (Hortifruti, Açougue, Padaria, Frios e Laticínios,
Mercearia, Bebidas, Congelados), 25 meses (jun/2023 a jun/2025). O percentual de perda começa
em ~5% de média geral e cai consistentemente até ~1,8%, cruzando a meta de 2% no período final —
replicando o formato de queda do projeto real.

## O que este projeto demonstra

- Modelagem de dados temporais (série mensal)
- Consultas de tendência (`GROUP BY` em coluna de data)
- Comparação com meta (benchmark) usando `HAVING` e subqueries
- Ranking multi-dimensional (unidade × categoria) para priorização de ação

## Como rodar

```bash
python3 gerar_dataset_desperdicio.py
```

## Trilha completa

1. ✅ SQL aplicado — [repositório](../sql-qualidade-portfolio)
2. ✅ Dashboard de Não Conformidades — [repositório](../dashboard-nao-conformidades-powerbi)
3. ✅ Desperdício Zero replicado (este repositório)
4. 🔜 Python (pandas) cruzando as bases anteriores

---
*Projeto de portfólio de Felipe Oliveira de Lima — em transição de carreira para Análise de Dados.*
