# Indicadores e regras de negócio

## Faturamento e custos

O modelo inclui `$ Faturamento`, `$ Custo`, `$ Margem Bruta`, `% Margem Bruta`, `$ Ticket Medio`, `Itens Vendidos` e `Notas Emitidas`. A definição exata de cada indicador está no catálogo DAX, extraído do modelo.

## Metas e atingimento

As medidas `$ Meta`, `$ Faturamento vs Meta` e `% Faturamento vs Meta` apoiam a análise de atingimento. Há medidas para identificar meses abaixo da meta e meses que também não apresentam crescimento anual.

## Inteligência temporal

As expressões de MTD (month-to-date), YTD (year-to-date) e YoY (year-over-year) permitem comparar acumulados e evolução. O modelo também contém lógica de comparação entre dois períodos e calendários auxiliares.

## Performance de vendedores

As medidas `Rank Vendedor`, `% Faturamento por Vendedor` e `% Faturamento dos Top 3 Vendedores` apoiam ranking, participação e concentração de faturamento.

## Forecast

A medida `$ Forecast` adiciona ao faturamento realizado a parcela de metas de datas classificadas como futuras. Medidas adicionais calculam projeções MTD/YTD e a meta diária necessária.

Essa regra é **projeção baseada em metas**; não há evidência de treinamento de algoritmo preditivo.

## Similaridade e recomendação

A estrutura inclui `fSimilaridadeClientes`, `dClienteSimilar` e medidas com prefixos `S -` e `H -`, voltadas a similaridade, histórico de compras e recomendação de itens. As expressões estão reproduzidas no catálogo DAX; não se afirma taxa de conversão, precisão ou efeito causal.

## Ressalvas

Os indicadores foram descritos com base nas expressões disponíveis e nos painéis. O arquivo de auditoria não demonstra testes de reconciliação, validação de resultados em produção ou resultados comerciais atribuíveis à solução.
