# Desperdício Zero — Exercícios de SQL

Dataset: `portfolio_desperdicio.db` (tabela `perdas`)

Mesma lógica da Fase 1: tente escrever a query sozinho antes de olhar o gabarito.

---

## Nível 1 — Visão geral

**1.1** Qual o percentual médio de perda em toda a base, sem filtro nenhum?

```sql
SELECT ROUND(AVG(percentual_perda), 2) AS media_geral
FROM perdas;
```

**1.2** Qual o valor total perdido em R$ em toda a base?

```sql
SELECT ROUND(SUM(valor_perda), 2) AS total_perdido
FROM perdas;
```

---

## Nível 2 — Evolução no tempo (a pergunta mais importante do projeto)

**2.1** Qual a evolução mês a mês do percentual médio de perda? (a query que prova a redução)

```sql
SELECT ano_mes, ROUND(AVG(percentual_perda), 2) AS media_perda
FROM perdas
GROUP BY ano_mes
ORDER BY ano_mes;
```

**2.2** Comparando o primeiro mês da base com o último, qual foi a redução percentual?

```sql
WITH extremos AS (
    SELECT ano_mes, AVG(percentual_perda) AS media
    FROM perdas
    WHERE ano_mes IN (
        (SELECT MIN(ano_mes) FROM perdas),
        (SELECT MAX(ano_mes) FROM perdas)
    )
    GROUP BY ano_mes
)
SELECT * FROM extremos ORDER BY ano_mes;
```
*(Calcule a diferença percentual manualmente entre os dois valores retornados — é um exercício de interpretação, não só de SQL.)*

---

## Nível 3 — Por categoria

**3.1** Qual categoria tem o maior percentual médio de perda?

```sql
SELECT categoria, ROUND(AVG(percentual_perda), 2) AS media_perda
FROM perdas
GROUP BY categoria
ORDER BY media_perda DESC;
```

**3.2** Quais categorias já atingiram a meta (2%) no último mês da base?

```sql
SELECT categoria, ROUND(AVG(percentual_perda), 2) AS media_ultimo_mes
FROM perdas
WHERE ano_mes = (SELECT MAX(ano_mes) FROM perdas)
GROUP BY categoria
HAVING media_ultimo_mes <= 2.0
ORDER BY media_ultimo_mes;
```

---

## Nível 4 — Por unidade (ranking e priorização)

**4.1** Ranking de unidades pelo valor total perdido (para saber onde o esforço de redução teve mais impacto financeiro).

```sql
SELECT unidade, ROUND(SUM(valor_perda), 2) AS total_perdido
FROM perdas
GROUP BY unidade
ORDER BY total_perdido DESC
LIMIT 10;
```

**4.2** Quais unidades ainda estão acima da meta (2%) considerando a média dos últimos 3 meses da base?

```sql
WITH ultimos_3_meses AS (
    SELECT DISTINCT ano_mes FROM perdas ORDER BY ano_mes DESC LIMIT 3
)
SELECT unidade, ROUND(AVG(percentual_perda), 2) AS media_recente
FROM perdas
WHERE ano_mes IN (SELECT ano_mes FROM ultimos_3_meses)
GROUP BY unidade
HAVING media_recente > 2.0
ORDER BY media_recente DESC;
```

---

## Nível 5 — Cruzamento unidade x categoria (a análise mais rica)

**5.1** Qual a combinação unidade + categoria com maior valor perdido acumulado? (aponta onde intervir primeiro)

```sql
SELECT unidade, categoria, ROUND(SUM(valor_perda), 2) AS total_perdido
FROM perdas
GROUP BY unidade, categoria
ORDER BY total_perdido DESC
LIMIT 10;
```

**5.2** Comparando Hortifruti entre todas as unidades: quais estão performando pior que a média da categoria?

```sql
WITH media_hortifruti AS (
    SELECT AVG(percentual_perda) AS media
    FROM perdas
    WHERE categoria = 'Hortifruti'
)
SELECT unidade, ROUND(AVG(percentual_perda), 2) AS media_unidade
FROM perdas
WHERE categoria = 'Hortifruti'
GROUP BY unidade
HAVING media_unidade > (SELECT media FROM media_hortifruti)
ORDER BY media_unidade DESC;
```

---

## Como isso vira apresentação

O Nível 2 (evolução no tempo) é a espinha dorsal da história: "começamos perdendo X%, terminamos
perdendo Y%, uma redução de Z%". O Nível 3 e 4 respondem "onde e o quê" — exatamente a mesma lógica
de storytelling usada no Dashboard de Não Conformidades (Fase 2).
