---
title: Referência rápida KQL
description: Uma lista de funções kQL úteis e suas definições com exemplos de sintaxe.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: c5d331b809d3f9f7db8d8c36add8b283bf0b9e39
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664869"
---
# <a name="kql-quick-reference"></a>Referência rápida KQL

Este artigo mostra-lhe uma lista de funções e suas descrições para ajudá-lo a começar a usar a Linguagem De Consulta Kusto.

| Operador/Função                               | Descrição                           | Sintaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtro/Pesquisa/Condição**                      |**_Encontre dados relevantes filtrando ou procurando_** |                      |
| [onde onde](/azure/kusto/query/whereoperator)                      | Filtros num predicado específico           | `T | where Predicate`                         |
| [onde contém/tem](/azure/kusto/query/whereoperator)        | `Contains`: Procura qualquer correspondência de substring <br> `Has`: Procura uma palavra específica (melhor desempenho)  | `T | where col1 contains/has "[search term]"`|
| [pesquisa](/azure/kusto/query/searchoperator)                    | Procura todas as colunas na tabela pelo valor | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [tomar](/azure/kusto/query/takeoperator)                        | Devolve o número especificado de registos. Usar para testar uma consulta<br>**_Nota:_** `_take`_ e `_limit`_ são sinónimos. | `T | take NumberOfRows` |
| [caso](/azure/kusto/query/casefunction)                        | Adiciona uma declaração de condição, semelhante a se/then/elseif em outros sistemas. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinto](/azure/kusto/query/distinctoperator)                | Produz uma tabela com a combinação distinta das colunas fornecidas da tabela de entrada | `distinct [ColumnName], [ColumnName]` |
| **Data/Hora**                                   |**_Operações que utilizam funções de data e hora_**               |                          |
|[atrás](/azure/kusto/query/agofunction)                           | Devolve o tempo compensado em relação ao tempo que a consulta executa. Por exemplo, `ago(1h)` está uma hora antes da leitura do relógio atual. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Devolve dados em [vários formatos](/azure/kusto/query/format-datetimefunction#supported-formats)de data. | `format_datetime(datetime , format)` |
| [caixote do lixo](/azure/kusto/query/binfunction)                          | Arredonda todos os valores num prazo e agrupa-os | `bin(value,roundTo)` |
| **Criar/Remover Colunas**                   |**_Adicione ou remova colunas numa tabela_** |                                                    |
| [imprimir](/azure/kusto/query/printoperator)                      | Saídas de uma única linha com uma ou mais expressões escalar | `print [ColumnName =] ScalarExpression [',' ...]` |
| [projeto](/azure/kusto/query/projectoperator)                  | Seleciona as colunas a incluir na ordem especificada | `T | project ColumnName [= Expression] [, ...]` <br> Ou <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projeto-longe](/azure/kusto/query/projectawayoperator)         | Seleciona as colunas para excluir da saída | `T | project-away ColumnNameOrPattern [, ...]` |
| [estender](/azure/kusto/query/extendoperator)                    | Cria uma coluna calculada e adiciona-a ao conjunto de resultados | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Ordenar e Agregar Dataset**                 |**_Reestruturar os dados separando ou agrupando-os de forma significativa_**|                  |
| [classificar](/azure/kusto/query/sortoperator)                        | Classifica as linhas da tabela de entrada por uma ou mais colunas em ordem ascendente ou descendente | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Início](/azure/kusto/query/topoperator)                          | Devolve as primeiras linhas N do conjunto de dados quando o conjunto de dados é classificado utilizando `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [resumo](/azure/kusto/query/summarizeoperator)              | Agrupa as linhas de acordo com as colunas de grupo `by`, e calcula as agregações em cada grupo | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Conta os registos na tabela de entrada (por exemplo, T)<br>Este operador está em falta para `summarize count() `| `T | count` |
| [aderir](/azure/kusto/query/joinoperator)                        | Funde as linhas de duas tabelas para formar uma nova tabela, combinando valores das colunas especificadas de cada tabela. Suporta uma gama completa de tipos de adesão: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [união](/azure/kusto/query/unionoperator)                      | Leva duas ou mais mesas e devolve todas as suas fileiras | `[T1] | union [T2], [T3], …` |
| [alcance](/azure/kusto/query/rangeoperator)                      | Gera uma tabela com uma série aritmética de valores | `range columnName from start to stop step step` |
| **Dados do Formato**                                 | **_Reestruturar os dados para a produção de forma útil_** | |
| [olhar para cima](/azure/kusto/query/lookupoperator)                    | Estende as colunas de uma tabela de factos com valores olhados numa tabela de dimensão | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expandir](/azure/kusto/query/mvexpandoperator)               | Transforma matrizes dinâmicas em linhas (expansão de vários valores) | `T | mv-expand Column` |
| [parse](/azure/kusto/query/parseoperator)                      | Avalia uma expressão de cadeia e analisa o seu valor numa ou mais colunas calculadas. Utilização para estruturar dados não estruturados. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [séries de faz-séries](/azure/kusto/query/make-seriesoperator)          | Cria uma série de valores agregados especificados ao longo de um eixo especificado | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [deixe](/azure/kusto/query/letstatement)                         | Liga um nome a expressões que podem referir-se ao seu valor vinculado. Os valores podem ser expressões de lambda para criar funções ad-hoc como parte da consulta. Use `let` para criar expressões sobre tabelas cujos resultados parecem uma nova tabela. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Geral**                                     | **_Operações diversas e função_** | |
| [invocar](/azure/kusto/query/invokeoperator)                    | Executa a função na tabela que recebe como entrada. | `T | invoke function([param1, param2])` |
| [avaliar o pluginName](/azure/kusto/query/evaluateoperator)     | Avalia extensões de linguagem de consulta (plugins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualização**                               | **_Operações que exibem os dados num formato gráfico_** | |
| [render](/azure/kusto/query/renderoperator) | Renderiza resultados como uma saída gráfica | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
