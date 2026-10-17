# Inteligência Comercial | Power BI

**Projeto de Business Intelligence para análise de faturamento, metas, performance comercial, rentabilidade e projeção de vendas.**

![Visão geral do dashboard](imagens/dashboard-comercial.png)

> **Sobre a publicação:** estudo de caso documentado a partir de um modelo Power BI existente. Identificadores individuais presentes na visualização de equipe foram ocultados. O arquivo PBIX, bases de dados, conexões e informações internas não fazem parte deste repositório.

## Contexto e objetivo

O projeto organiza indicadores comerciais em uma experiência analítica interativa, permitindo acompanhar resultados, comparar períodos, identificar desvios frente às metas e apoiar decisões sobre equipe e produtos.

A solução reúne quatro eixos:

1. **Evolução temporal:** faturamento por mês, comparações com o ano anterior e metas, análises MTD/YTD e comparação de períodos.
2. **Performance comercial:** resultados por vendedor e hierarquia gerencial, ranking, participação no faturamento e identificação de desvios.
3. **Rentabilidade:** faturamento e margem bruta por produto, análise de dispersão e priorização de itens.
4. **Forecast:** acompanhamento de realizado, meta, projeção e esforço diário necessário para alcançar o objetivo.

## Tecnologias e conceitos comprovados

- **Power BI Desktop:** desenvolvimento dos painéis e modelo semântico.
- **DAX:** medidas de faturamento, custo, margem, ranking, inteligência temporal, forecast e recomendações.
- **Power Query / fontes do modelo:** o inventário registra CSV, Excel, JSON, SQL Server e tabelas inseridas manualmente; não se afirma que todas as fontes sejam usadas simultaneamente nos mesmos fluxos.
- **Modelagem tabular:** tabelas fato, dimensões, calendário, tabelas auxiliares e relacionamentos.
- **Análise de similaridade:** estrutura de clientes similares e medidas para recomendação de itens.

## Principais funcionalidades

| Área | Entregas analíticas |
| --- | --- |
| Receita | Faturamento, variação anual, evolução mensal e comparação entre períodos |
| Metas | Desvio absoluto e percentual, atingimento e meses abaixo da meta |
| Equipe | Ranking, participação dos três maiores vendedores e análises por gestor |
| Produtos | Margem bruta, faturamento e dispersão de rentabilidade |
| Forecast | Realizado acumulado, projeção, meta diária original e necessária |
| Recomendações | Medidas de itens recomendados e similaridade entre clientes |

## Forecast comercial

![Forecast de vendas](imagens/forecast.png)

O modelo contém uma medida `$ Forecast` que combina faturamento realizado com a meta atribuída a datas futuras, de acordo com o indicador `dCalendario[Passado]`. Há também medidas MTD e YTD e cálculos para a meta diária necessária.

**Importante:** trata-se de uma projeção orientada por meta, não de um modelo estatístico ou de machine learning. A regra original está documentada no [catálogo de DAX](dax/medidas-principais.md).

## Modelo de dados

O inventário disponibilizado apresenta tabelas de vendas, metas, produtos, clientes, vendedores, calendários e similaridade. Consulte:

- [Inventário e relacionamentos](documentacao/modelo-de-dados.md)
- [Indicadores e regras de negócio](documentacao/indicadores-e-regras.md)
- [Catálogo das medidas DAX](dax/medidas-principais.md)

## Diferenciais técnicos

**Inteligência temporal:** medidas MTD/YTD e YoY, além de comparação dinâmica entre dois períodos.

**Gestão comercial:** integração entre métricas de receita, metas e níveis de vendedor/supervisão/gerência.

**Recomendações:** presença de estrutura de similaridade de clientes e cálculos DAX para identificar itens recomendáveis. O modelo não comprova, por si só, a eficácia dessas recomendações.

**Experiência analítica:** segmentações, alternância de visões, visuais de dispersão, indicadores e análise de desvios.

## Limites e cuidados

Este repositório é uma **documentação técnica de portfólio**, não uma distribuição do sistema de produção. Não contém dados transacionais, credenciais, caminhos locais, arquivos PBIX ou informação que identifique a organização original.

Os relacionamentos e as expressões DAX foram documentados conforme extraídos; isso **não constitui validação de resultados nem certificação de qualidade**. Em especial, existe no inventário uma relação N:N entre metas e categorias de produtos que exige revisão antes de qualquer reutilização dos indicadores em produção.

Não são apresentados ganhos financeiros, percentuais de melhoria ou impactos quantitativos sem evidência.

---
**Áreas:** Business Intelligence · Data Analytics · Analytics Engineering  
**Idioma:** Português
