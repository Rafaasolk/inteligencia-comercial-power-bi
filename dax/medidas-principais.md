# Catálogo DAX — medidas extraídas do modelo

Expressões transcritas do arquivo de auditoria fornecido, sem aplicar snippets de correção ou recomendações automáticas.

## Forecast

### $ Forecast

O que faz: Soma o faturamento realizado com a meta dos dias futuros, projetando o resultado esperado até o fim do período.

```dax
VAR
 vRF = 

CALCULATE
(
    [$ Meta], 
    dCalendario[Passado] = 
FALSE
()
)

RETURN

vRF + [$ Faturamento]
```

### Max Gauge MTD

O que faz: Calcula o teto do medidor do mês (gauge), como 120% do máximo entre meta e forecast, pra deixar margem visual.

```dax
MAX
([$ Meta MTD], [$ Forecast MTD]) * 1.2
```

### Max Gauge YTD

O que faz: Calcula o teto do medidor anual (gauge), como 120% do máximo entre meta e forecast YTD.

```dax
MAX
([$ Meta YTD], [$ Forecast YTD]) * 1.2
```

## Forecast\MTD

### % Forecast vs Meta MTD

O que faz: Compara o forecast mensal com a meta mensal, exibindo em percentual o resultado versus o alvo do mês.

```dax
VAR
 vValor = 
DIVIDE
 ( [$ Forecast MTD] - [$ Meta MTD], [$ Meta MTD])

VAR
 vFormat = 
FORMAT
(vValor, 
"⇧ 0% acima da meta; ⇩ 0% abaixo da meta"
)

RETURN

vFormat
```

### $ Faturamento MTD Até Hoje

O que faz: Acumula o faturamento do mês até hoje, ignorando dias futuros.

```dax
CALCULATE
(
    [$ Faturamento],
    
CALCULATETABLE
(
        
DATESMTD
(dCalendario[Data]),
        dCalendario[Passado] = 
TRUE
()
    )
)
```

### $ Forecast MTD

O que faz: Calcula o forecast dentro do mês em curso, combinando realizadas com metas futuras do mês.

```dax
CALCULATE
(
    [$ Forecast],
    
DATESMTD
(dCalendario[Data])
)
```

### $ Meta MTD

O que faz: Soma a meta acumulada do mês em curso, até o último dia da projeção.

```dax
CALCULATE
(
    [$ Meta],
    
DATESMTD
(dCalendario[Data])
)
```

### Última Compra

O que faz: Identifica a data da última compra registrada no modelo, pra validação de atualização.

```dax
CALCULATE
( 
    
MAX
(fVendas[data]),
    
ALL
()
)
```

## Forecast\Reajuste

### $ Meta MTD Até Hoje

O que faz: Soma a meta do mês, considerando apenas os dias já passados.

```dax
VAR
 vMetaAtual = 

CALCULATE
(
    [$ Meta MTD],
    dCalendario[Passado] = 
TRUE
()
)

RETURN

vMetaAtual
```

### Dias Realizados

O que faz: Conta os dias úteis já transcorridos (passado) no período filtrado.

```dax
CALCULATE
(
    
COUNTROWS
(dCalendario),
    
NOT
(dCalendario[DiaSemana] 
IN
 {0,6}),
    dCalendario[Passado] = 
TRUE
()
)
```

### Dias Úteis

O que faz: Conta os dias da semana (segunda a sexta) do período filtrado, excluindo sábados e domingos.

```dax
CALCULATE
(
    
COUNTROWS
(dCalendario),
    
NOT
(dCalendario[DiaSemana] 
IN
 {0,6})
)
```

### Meta Diária Nova

O que faz: Recalcula a meta diária ajustando-a aos dias úteis restantes e ao faturamento já realizado, atualizando o alvo operacional.

```dax
VAR
 vDiferencaValor = [$ Meta MTD] - [$ Faturamento MTD 
At
é Hoje]

VAR
 vDiasRestantes = [Dias Úteis] - [Dias Realizados]

VAR
 vNovaMeta = 

DIVIDE
(
    vDiferencaValor,
    vDiasRestantes
)

VAR
 vMetaOriginal = 

DIVIDE
(
    [$ Meta MTD],
    [Dias Úteis]
)

VAR
 vNovaMetaCorrigida = 

IF
(
    vNovaMeta > vMetaOriginal,
    vNovaMeta,
    vMetaOriginal
)

VAR
 vResult = 
FORMAT
(vNovaMetaCorrigida, 
"R$ #,0"
)

RETURN


"Meta Diária Necessária: "
 & vResult
```

### Meta Diária Original

O que faz: Divide a meta do mês pela quantidade de dias úteis, exibindo quanto é preciso vender por dia em média.

```dax
VAR
 vMetaDia = 

DIVIDE
(
    [$ Meta MTD],
    [Dias Úteis]
)

VAR
 vResult = 
FORMAT
(vMetaDia, 
"R$ #,0"
)

RETURN


"Meta Diária Original: "
 & vResult
```

## Forecast\YTD

### % Forecast vs Meta YTD

O que faz: Compara o forecast YTD com a meta YTD, mostrando em percentual se a projeção supera ou fica abaixo do alvo anual.

```dax
DIVIDE
 ( [$ Forecast YTD] - [$ Meta YTD], [$ Meta YTD])
```

### $ Faturamento YTD Até Hoje

O que faz: Acumula o faturamento do ano até hoje, ignorando dias futuros.

```dax
CALCULATE
(
    [$ Faturamento],
    
CALCULATETABLE
(
        
DATESYTD
(dCalendario[Data]),
        dCalendario[Passado] = 
TRUE
()
    )
)
```

### $ Forecast YTD

O que faz: Soma o forecast acumulado do ano até a data filtrada, combinando realizadas e metas futuras.

```dax
CALCULATE
(
    [$ Forecast],
    
DATESYTD
(dCalendario[Data])
)
```

### $ Meta YTD

O que faz: Soma a meta acumulada do ano até a data filtrada.

```dax
CALCULATE
(
    [$ Meta],    
    
DATESYTD
(dCalendario[Data])
)
```

## Gerais

### % Faturamento dos Top 3 Vendedores

O que faz: Soma o faturamento dos 3 vendedores com maior volume e divide pelo total geral, mostrando o peso da liderança.

```dax
VAR
 vNumerador = 

CALCULATE
 (
    [$ Faturamento],
    
KEEPFILTERS
(
        
TOPN
 ( 3, 
ALL
 ( dVendedor[descricao] ), [$ Faturamento] )
    )
)

VAR
 vDenominador = 
CALCULATE
([$ Faturamento], 
all
(dVendedor))

RETURN
 

DIVIDE
(vNumerador, vDenominador)
```

### % Faturamento por Vendedor

O que faz: Calcula a participação de cada vendedor no total de faturamento dos vendedores visíveis.

```dax
var
 vTotal = 
CALCULATE
([$ Faturamento], 
ALLSELECTED
(dVendedor))
 
RETURN

 
DIVIDE
(
    [$ Faturamento], 
    vTotal
)
```

### % Faturamento YoY

O que faz: Percentualiza a variação de faturamento comparando o período com o mesmo período do ano anterior.

```dax
VAR
 varFaturamentoLY = 

CALCULATE
 ( 
    [$ Faturamento],
    
SAMEPERIODLASTYEAR
(dCalendario[data])
)


VAR
 varResultado = 
DIVIDE
 ( [$ Faturamento] - varFaturamentoLY, varFaturamentoLY )


RETURN

varResultado
```

### % Margem Bruta

O que faz: Percentualiza a margem bruta dividindo-a pelo faturamento, mostrando quanto de cada real vira lucro.

```dax
DIVIDE
([$ Margem Bruta], [$ Faturamento])
```

### $ Custo

O que faz: Soma o custo total dos itens vendidos, usando o custo unitário de cada produto.

```dax
SUMX
 (
    fVendas, 
    fVendas[item_quantidade] * 
RELATED
 ( dProduto[CustoUnitario] )
)
```

### $ Faturamento

O que faz: Soma o faturamento líquido de todas as vendas, descontando devoluções e descontos concedidos.

```dax
SUMX
 ( 
    fVendas,
    fVendas[item_quantidade] * fVendas[valor_unitario] - fVendas[valor_desconto]
)
```

### $ Margem Bruta

O que faz: Calcula a diferença entre faturamento e custo, mostrando o lucro bruto.

```dax
[$ Faturamento] - [$ Custo]
```

### $ Ticket Medio

O que faz: Divide o faturamento pelo número de notas fiscais, mostrando o valor médio por transação.

```dax
DIVIDE
 ( [$ Faturamento], [Notas Emitidas] )
```

### Itens Vendidos

O que faz: Soma a quantidade total de itens (unidades) vendidas no período filtrado.

```dax
SUM
(fVendas[item_quantidade])
```

### Notas Emitidas

O que faz: Conta o número de notas fiscais distintas do período filtrado.

```dax
DISTINCTCOUNT
 ( fVendas[nfe] )
```

### Rank Vendedor

O que faz: Ordena os vendedores por faturamento, atribuindo um rank de 1 (maior) em diante.

```dax
RANKX
(
ALL
(dVendedor), [$ Faturamento])
```

## Metas

### % Fator por Produto

O que faz: Obtém o peso de cada produto na categoria, usando o faturamento do ano anterior como referência de participação.

```dax
VAR
 vFatLY = 
CALCULATE
([$ Faturamento], 
PREVIOUSYEAR
(dCalendario[Data]))

VAR
 vFatLYTotal = 

CALCULATE
(
    [$ Faturamento],
    
PREVIOUSYEAR
(dCalendario[Data]),
    
ALL
(dProduto), 
    
VALUES
(dProduto[categoria_id])
)

VAR
 vFatorProduto = 
DIVIDE
(vFatLY, vFatLYTotal)

RETURN

vFatorProduto
```

### % Faturamento vs Meta

O que faz: Percentualiza o resultado contra a meta, dividindo a diferença pela meta.

```dax
DIVIDE
 ( [$ Faturamento vs Meta], [$ Meta])
```

### $ Faturamento vs Meta

O que faz: Calcula a diferença em reais entre o faturamento realizado e a meta proposta.

```dax
[$ Faturamento] - [$ Meta]
```

### $ Meta

O que faz: Calcula a meta de faturamento multiplicando a meta base de cada categoria pelo fator de distribuição histórica por produto.

```dax
SUM
(fMetas[Valor]) * [% Fator por Produto]
```

## Recomendações

### Cliente Selecionado

O que faz: Extrai e exibe em maiúsculas o nome do cliente atualmente filtrado.

```dax
UPPER
(
SELECTEDVALUE
(dCliente[descricao]))
```

### H - Itens Recomendados

O que faz: Calcula a quantidade de itens que foram vendidos no período anterior mas não no período atual, apontando o que retomou.

```dax
VAR
 _ItensAtuais = 
VALUES
(fVendas[produto_id])

VAR
 _ItensPassado = 

CALCULATETABLE
(
    
VALUES
(fVendas[produto_id]),
    
ALL
(dCalendario),
    dCalendario[Data] < 
MIN
(dCalendario[Data])
)

VAR
 _ItensRecomendados = 
EXCEPT
(_ItensPassado, _ItensAtuais)

VAR
 _QtdRecomendada = 

CALCULATE
(
    [Itens Vendidos],                           
//retorna a quantidade de itens vendidos

    _ItensRecomendados,                         
//daqueles itens que não foram vendidos no período atual

    
ALL
(dCalendario),                           
//olhando todas as datas

    dCalendario[Data] < 
MIN
(dCalendario[Data])  
//porém só do passado

)

RETURN

_QtdRecomendada
```

### H - Top N Recomendações

O que faz: Lista os 3 produtos mais vendidos no período anterior mas não no atual, sugerindo resgate.

```dax
VAR
 vN = 3

VAR
 vTopN =

TOPN
(
    vN,
    
ALL
(dProduto),
    [H - Itens Recomendados] + dProduto[id]/100000,
    
DESC

)

VAR
 vItens =

CONCATENATEX
(
    vTopN,
   
"▸ "
 & dProduto[id] & 
" - "
 & dProduto[descricao] & 
" - "
 & dProduto[tamanho],
    
UNICHAR
(10), -[H - Itens Recomendados]
)

RETURN


UNICHAR
(10) &
vItens
```

### H - Última Compra Produto

O que faz: Identifica quando cada produto foi vendido pela última vez no período anterior.

```dax
CALCULATE
(
    
MAX
(fVendas[data]),
    dCalendario[Data] < 
MIN
(dCalendario[Data])
)
```

### S - Itens Comprados Clientes v1



```dax
CALCULATE
(
    [Itens Vendidos],
    
ALL
(dCliente),
    
TREATAS
(
        
VALUES
(fSimilaridadeClientes[similar]),
        fVendas[cliente_id]
    )
)
```

### S - Itens Comprados Clientes v2



```dax
CALCULATE
(
    [Itens Vendidos],
    
ALL
(dCliente),
    
TREATAS
(
        
VALUES
(dCliente[id]),
        fSimilaridadeClientes[cliente]
    ),
    
CROSSFILTER
(fSimilaridadeClientes[similar], dClienteSimilar[id], Both)
)
```

### S - Itens Comprados Clientes v3

O que faz: Calcula a quantidade de itens vendidos aos clientes similares ao cliente selecionado, ignorando o filtro direto de cliente para revelar padrões de grupo.

```dax
CALCULATE
(
    [Itens Vendidos],
    
CROSSFILTER
(dCliente[id], fVendas[cliente_id], None),
    
CROSSFILTER
(fSimilaridadeClientes[similar], dClienteSimilar[id], Both)
)
```

### S - Itens Recomendados

O que faz: Identifica produtos que os clientes similares compraram mas o cliente atual não, sugerindo o que recomendar.

```dax
VAR
 vItensCompradosAtual = 
VALUES
(fVendas[produto_id])

VAR
 vItensCompradosSimilares =

CALCULATETABLE
(
    
VALUES
(dProduto[id]),
    
CROSSFILTER
(dCliente[id], fVendas[cliente_id], None),
    
CROSSFILTER
(fSimilaridadeClientes[similar], dClienteSimilar[id], Both)
)

VAR
 vItensRecomendados = 
EXCEPT
(vItensCompradosSimilares, vItensCompradosAtual)

VAR
 vItensQuantidade = 

CALCULATE
(
    [Itens Vendidos],
    vItensRecomendados,
    
CROSSFILTER
(dCliente[id], fVendas[cliente_id], None),
    
CROSSFILTER
(fSimilaridadeClientes[similar], dClienteSimilar[id], Both)
)

RETURN

vItensQuantidade
```

### S - Média Similaridade

O que faz: Calcula a similaridade média entre o cliente selecionado e seus pares, em escala de 0 a 1.

```dax
AVERAGE
(fSimilaridadeClientes[valor])
```

### S - Texto Clientes

O que faz: Monta um texto narrativo listando os clientes similares com seus respectivos scores de similaridade, ordenados do maior pro menor.

```dax
VAR
 vSimilares = 
FILTER
(dClienteSimilar, [S - Média Similaridade])

VAR
 vTextoSimilares =

CONCATENATEX
(
    vSimilares,
    
"▪ "
 & dClienteSimilar[descricao] &
    
" ("
 & 
FORMAT
([S - Média Similaridade], 
"0%"
) & 
")"
,
    
UNICHAR
(10), -[S - Média Similaridade]
)

VAR
 vTexto = 
    
"Com base nos perfis de compra, os clientes mais similares a este são: "
 &
    
UNICHAR
(10) &
    vTextoSimilares

RETURN


IF
(
    
COUNTROWS
(vSimilares) = 0,
    
"Este cliente não possui similares."
,
    vTexto
)
```

### S - Top N Recomendações

O que faz: Lista os 3 produtos mais vendidos pelos clientes similares mas não pelo cliente atual, com descrição e tamanho.

```dax
VAR
 vN = 3

VAR
 vTopN =

TOPN
(
    vN,
    
ALL
(dProduto),
    [S - Itens Recomendados] + 
int
(dProduto[id])/10000,
    
DESC

)

VAR
 vItens =

CONCATENATEX
(
    vTopN,
    
"▸ "
 & dProduto[id] & 
" - "
 & dProduto[descricao] & 
" - "
 & dProduto[tamanho],
    
UNICHAR
(10), -[S - Itens Recomendados]
)

RETURN
 

UNICHAR
(10) &
vItens
```

## Texto

### % Faturamento vs Meta (texto)

O que faz: Exprime o resultado contra a meta como percentual, indicando se ficou acima ou abaixo do alvo.

```dax
FORMAT
([% Faturamento vs Meta], 
"⇧ 0% acima da meta; ⇩ 0% abaixo da meta"
)
```

### % Faturamento YoY (texto)

O que faz: Compara o faturamento do período com o mesmo período do ano anterior, exibindo o percentual de crescimento ou queda formatado.

```dax
FORMAT
([% Faturamento YoY], 
"⇧ 0% vs ano anterior;⇩ 0% vs ano anterior"
)
```

### % Margem Bruta (texto)

O que faz: Formata a margem bruta em percentual com sufixo "de margem bruta" pra leitura direto no visual.

```dax
FORMAT
([% Margem Bruta], 
"0% de margem bruta"
)
```

### % Notas Emitidas YoY (texto)

O que faz: Compara a quantidade de notas fiscais do período com o mesmo período do ano anterior, exibindo o percentual de variação.

```dax
VAR
 varNotasEmitidasLY = 

CALCULATE
 ( 
    [Notas Emitidas],
    
SAMEPERIODLASTYEAR
(dCalendario[data])
)

VAR
 varResultado = 
DIVIDE
 ( [Notas Emitidas] - varNotasEmitidasLY, varNotasEmitidasLY )

RETURN


FORMAT
(varResultado, 
"⇧ 0% vs ano anterior;⇩ 0% vs ano anterior"
)
```

### % Ticket medio YoY (texto)

O que faz: Compara o ticket médio do período com o mesmo período do ano anterior, exibindo a variação percentual.

```dax
VAR
 varTicketMedioLY = 

CALCULATE
 ( 
    [$ Ticket Medio],
    
SAMEPERIODLASTYEAR
 ( dCalendario[data] )
)


VAR
 varResultado = 
DIVIDE
 ( [$ Ticket Medio] - varTicketMedioLY, varTicketMedioLY )


RETURN


FORMAT
(varResultado, 
"⇧ 0% vs ano anterior;⇩ 0% vs ano anterior"
)
```

## UX

### Comparação Faturamento %

O que faz: Calcula a variação percentual de faturamento entre dois meses selecionados.

```dax
VAR
 vMesA = 
SELECTEDVALUE
(dCalendarioA[AnoMesINT])

VAR
 vFatMesA = 

CALCULATE
(
    [$ Faturamento],
    
ALL
(dCalendario),
    dCalendario[AnoMesINT] = vMesA
)

VAR
 vMesB = 
SELECTEDVALUE
(dCalendarioB[AnoMesINT])

VAR
 vFatMesB =

CALCULATE
(
    [$ Faturamento],
    
ALL
(dCalendario),
    dCalendario[AnoMesINT] = vMesB
)

RETURN


DIVIDE
(
    vFatMesB - vFatMesA,
    vFatMesA
)
```

### Comparação Meses Visíveis

O que faz: Retorna 1 se o segundo mês selecionado é mais recente que o primeiro, ou 0 caso contrário.

```dax
VAR
 vMesA = 
SELECTEDVALUE
(dCalendarioA[AnoMesINT])

VAR
 vMesB = 
SELECTEDVALUE
(dCalendarioB[AnoMesINT])

RETURN


IF
(
    vMesB > vMesA,
    1,
    0
)
```

### Comparação Períodos

O que faz: Calcula uma altura de barra relativa quando dois meses específicos são selecionados pra comparação lado a lado.

```dax
VAR
 vMesEixo = 
SELECTEDVALUE
(dCalendario[AnoMesINT])

VAR
 vMesPrimeiro = 
SELECTEDVALUE
(dCalendarioA[AnoMesINT])

VAR
 vMesUltimo = 
SELECTEDVALUE
(dCalendarioB[AnoMesINT])

RETURN


IF
(
    vMesEixo = vMesPrimeiro || vMesEixo = vMesUltimo,
    [Comparação YMax] * 0.95
)
```

### Comparação Título

O que faz: Monta um título dinâmico descrevendo qual períodos estão sendo comparados, ex: "Comparação do faturamento entre os meses jan e mar".

```dax
VAR
 vMesPrimeiro = 
SELECTEDVALUE
(dCalendarioA[AnoMesINT])

VAR
 vMesUltimo = 
SELECTEDVALUE
(dCalendarioB[AnoMesINT])

VAR
 vMesA = 
LOOKUPVALUE
(dCalendario[MesAno], dCalendario[AnoMesINT], vMesPrimeiro)

VAR
 vMesB = 
LOOKUPVALUE
(dCalendario[MesAno], dCalendario[AnoMesINT], vMesUltimo)

RETURN


"Comparação do faturamento entre os meses "
 & vMesA & 
" e "
 & vMesB
```

### Comparação YMax

O que faz: Obtém o faturamento máximo entre todos os meses no contexto, multiplicado por 1.25 pra escala do visual de comparação.

```dax
CALCULATE
(
    
MAXX
(
        
VALUES
(dCalendario[AnoMesINT]),
        [$ Faturamento]
    ),
    
ALL
(dCalendario)
) * 1.25
```

### Cor Rótulos

O que faz: Retorna uma cor (cinza ou vermelho) dependendo se o faturamento acima ou abaixo da meta, pra marcar visualmente o status.

```dax
IF
(
    [$ Faturamento vs Meta] > 0,
    
"#E6E6E6"
,
    
"#FA8187"

)
```

### Cor Scatter Plot Produto



```dax
VAR
 vMargemAbaixo = [% Margem bruta] < [Mediana Margem %]

VAR
 vFaturamentoAbaixo = [$ Faturamento] < [Mediana Faturamento]

RETURN


IF
(
    vMargemAbaixo && vFaturamentoAbaixo, 
    
"#FA8187"
,
    
"#229DB9"

)
```

### Cor Scatter Plot Vendedor

O que faz: Retorna uma cor vermelha se o vendedor perdeu meta e não cresceu, ou azul caso contrário, pra identificar quem precisa de atenção.

```dax
VAR
 vSemBaterMeta = [% Faturamento vs Meta] < 0

VAR
 vSemCrescer = [% Faturamento YoY] < 0

RETURN


IF
(
    vSemBaterMeta && vSemCrescer, 
    
"#FA8187"
,
    
"#229DB9"

)
```

### Dummy Eixo Produto

O que faz: Valor fictício para ajuste de escala ou posicionamento em visuais com produto no eixo.

```dax
-1
```

### Dummy Margem TOP 5

O que faz: Valor fictício usado para alinhamento e ajuste de visual, sem significado de negócio direto.

```dax
0
```

### Dummy Meta Supervisor

O que faz: Valor fictício para controle de layout ou debug em visuais de supervisor.

```dax
0
```

### Mediana Faturamento



```dax
MEDIANX
(
    
ALL
(dProduto[descricao]),
    [$ Faturamento]
)
```

### Mediana Margem %



```dax
MEDIANX
(
    
ALL
(dProduto[descricao]),
    [% Margem bruta]
)
```

### Meses sem bater meta

O que faz: Lista os meses do ano em que o faturamento ficou abaixo da meta.

```dax
CONCATENATEX
(
    
FILTER
(
        
VALUES
('dCalendario'[MesAbre]),
        [% Faturamento vs Meta] < 0
    ),
    dCalendario[MesAbre],
    
", "

)
```

### Meses sem bater meta e sem crescer

O que faz: Lista os meses em que o faturamento ficou abaixo da meta E não cresceu em relação ao ano anterior.

```dax
CONCATENATEX
(
    
FILTER
(
        
VALUES
('dCalendario'[MesAbre]),
        [% Faturamento vs Meta] < 0 &&
        [% Faturamento YoY] < 0
    ),
    dCalendario[MesAbre],
    
", "

)
```

### Valor Coluna Faturamento

O que faz: Formata o faturamento em unidades compactas (Mi, K) conforme a magnitude, poupando espaço no visual.

```dax
VAR
 vFat = [$ Faturamento]

VAR
 vFatFormat = 

SWITCH
(
    
TRUE
(),
    vFat >= 1E8, 
FORMAT
(vFat, 
"#0,, Mi"
),
    vFat >= 1E7, 
FORMAT
(vFat, 
"#0,,.0 Mi"
),
    vFat >= 1E6, 
FORMAT
(vFat, 
"#0,,.00 Mi"
),
    vFat >= 1E5, 
FORMAT
(vFat, 
"#0, K"
),
    vFat >= 1E4, 
FORMAT
(vFat, 
"#0,.0 K"
),
    vFat >= 1E3, 
FORMAT
(vFat, 
"#0,.00 K"
),
    
FORMAT
(vFat, 
"#0"
)
)

RETURN

vFatFormat
```

### Valor Detalhes Meta

O que faz: Formata o percentual de resultado versus meta para exibição em cards e detalhes.

```dax
FORMAT
([% Faturamento vs Meta], 
"⇧ 0%; ⇩ 0%"
)
```
