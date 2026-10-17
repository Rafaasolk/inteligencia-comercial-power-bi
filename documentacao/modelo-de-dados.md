# Modelo de dados

## Inventário de tabelas

A lista abaixo reproduz os itens apresentados na seção de inventário do arquivo de auditoria, sem notas ou recomendações automáticas.

| Tabela | Tipo | Colunas | Linhas |
| --- | --- | ---: | ---: |
| dCliente | Dimensão | 7 | 701 |
| dProduto | Dimensão | 7 | 93 |
| dVendedor | Dimensão | 7 | 12 |
| fVendas | Fato | 9 | 39.960 |
| fMetas | Fato | 4 | 24.960 |
| Medidas | Medidas | 0 | — |
| auxGerenteSupervisor | Auxiliar | 3 | 2 |
| dCalendario | Calendário | 18 | 1.095 |
| fSimilaridadeClientes | Fato | 3 | 1.444 |
| dClienteSimilar | Dimensão | 7 | 701 |
| auxVisao | Auxiliar | 2 | 2 |
| dCalendarioA | Calendário | 18 | 1.095 |
| dCalendarioB | Calendário | 18 | 1.095 |

> Observação: o resumo do relatório informa 12 tabelas, mas a tabela de inventário apresenta **13 entradas**. Preservamos as entradas listadas e registramos a divergência, sem corrigir a contagem por suposição.

## Relacionamentos registrados

| Nº | Origem | Destino | Cardinalidade | Filtro | Ativo |
| --- | --- | --- | --- | --- | --- |
| 1 | fVendas[cliente_id] | dCliente[id] | N:1 | Única | ✓ |
| 2 | fVendas[produto_id] | dProduto[id] | N:1 | Única | ✓ |
| 3 | fVendas[vendedor_id] | dVendedor[id] | N:1 | Única | ✓ |
| 4 | fVendas[data] | dCalendario[Data] | N:1 | Única | ✓ |
| 5 | fMetas[categoria_id] | dProduto[categoria_id] | N:N | Única | ✓ |
| 6 | fMetas[Data] | dCalendario[Data] | N:1 | Única | ✓ |
| 7 | fSimilaridadeClientes[cliente] | dCliente[id] | N:1 | Única | ✓ |
| 8 | fSimilaridadeClientes[similar] | dClienteSimilar[id] | N:1 | Única | ✓ |
| 9 | fVendas[cliente_id] | dClienteSimilar[id] | N:1 | Única | ✓ |
| 10 | fMetas[vendedor_id] | dVendedor[id] | N:1 | Única | ✓ |

## Leitura da arquitetura

- `fVendas`: fatos de transações comerciais, relacionados a cliente, produto, vendedor e calendário.
- `fMetas`: fatos de metas, relacionados a produto por categoria, calendário e vendedor.
- `fSimilaridadeClientes`: pares de clientes e seus similares.
- `dCliente`, `dClienteSimilar`, `dProduto`, `dVendedor`: dimensões de análise.
- `dCalendario`, `dCalendarioA`, `dCalendarioB`: tabelas de calendário; as duas últimas dão suporte à comparação entre períodos.
- `auxGerenteSupervisor` e `auxVisao`: tabelas auxiliares de seleção.
- `Medidas`: tabela organizadora de medidas DAX.

### Atenção à cardinalidade

O relacionamento `fMetas[categoria_id]` → `dProduto[categoria_id]` consta como **N:N ativo**. A documentação registra a modelagem existente, mas não presume que o cruzamento de metas por produto esteja validado. Qualquer reutilização deve conferir granularidade e propagação de filtros.

## Fontes registradas

CSV, Excel, JSON, SQL Server e tabela digitada manualmente. Não são publicados caminhos de origem nem configurações de acesso.
