# Chamado #001: Conhecendo a base

**Quem pediu:** Daiane Tavares (Coordenação de BI) · **Resolvido em:** 03/10/2026 · **Tipo:** SQL (DuckDB, schema `vendas`)

> A Nexora é uma empresa fictícia. Todos os dados deste chamado são fictícios.

## Problema
"Antes de qualquer coisa, preciso que você conheça o tamanho da nossa operação, com números tirados da base e não da apresentação institucional. Me devolve uma linha só com: quantas lojas temos, quantos cadastros de clientes existem, quantos pedidos foram registrados e o período que esses pedidos cobrem (primeiro e último dia)."

## Diagnóstico
Comecei explorando a base `nexora_lab` para descobrir onde estava cada informação. Os números pedidos estavam em três tabelas do schema `vendas`: `loja`, `cliente` e `pedido`. As três contagens saíram da quantidade de linhas de cada tabela. O período dos pedidos saiu da coluna `criado_em` da tabela `pedido`: a data mais antiga marca o primeiro pedido, e a mais recente, o último. Como `criado_em` guarda data e hora e a Daiane pediu só o dia, converti as duas datas para `DATE`.

## Solução
```sql
SELECT
  (SELECT COUNT(*) FROM vendas.loja)    AS lojas,
  (SELECT COUNT(*) FROM vendas.cliente) AS cadastros_de_clientes,
  (SELECT COUNT(*) FROM vendas.pedido)  AS pedidos,
  (SELECT CAST(MIN(criado_em) AS DATE) FROM vendas.pedido) AS primeiro_pedido,
  (SELECT CAST(MAX(criado_em) AS DATE) FROM vendas.pedido) AS ultimo_pedido;
```

Cada número é calculado por uma consulta separada, entre parênteses (uma subconsulta). O `SELECT` de fora junta as cinco respostas numa linha só, e o `AS` dá o nome de cada coluna.

Resultado:

| lojas | cadastros_de_clientes | pedidos | primeiro_pedido | ultimo_pedido |
| --- | --- | --- | --- | --- |
| 14 | 52001 | 200687 | 2023-01-01 | 2025-12-31 |

Em resumo: a Nexora tem 14 lojas, 52.001 cadastros de clientes e 200.687 pedidos, registrados entre 01/01/2023 e 31/12/2025.

## O que fica de lição
Os números vieram da base, não da apresentação, como a Daiane pediu. O cuidado maior foi com o nome da coluna: a pergunta era sobre **cadastros** de clientes, não sobre pessoas. Uma mesma pessoa pode ter mais de um cadastro, então "52.001 clientes" seria uma afirmação diferente, e provavelmente errada. Antes de responder, vale confirmar o que exatamente está sendo contado.
