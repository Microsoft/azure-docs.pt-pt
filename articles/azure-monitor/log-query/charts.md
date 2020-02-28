---
title: Criação de gráficos e diagramas a partir de consultas de registo do Monitor Azure  Microsoft Docs
description: Descreve várias visualizações no Monitor Azure para exibir os seus dados de registo de diferentes formas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670326"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Criação de gráficos e diagramas de consultas de registo do Monitor Azure

> [!NOTE]
> Deve completar [agregações avançadas em consultas](advanced-aggregations.md) de registo do Monitor Azure antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve várias visualizações no Monitor Azure para exibir os seus dados de registo de diferentes maneiras.

## <a name="charting-the-results"></a>Cartografando os resultados
Comece por rever quantos computadores existem por sistema operativo, durante a última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Por predefinição, os resultados exibem-se como uma tabela:

![Tabela](media/charts/table-display.png)

Para obter uma melhor vista, selecione **Gráfico**, e escolha a opção **Pie** para visualizar os resultados:

![Gráfico de torta](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Tabelas temporais
Mostre a média, 50º e 95º percentículos de tempo de processador em caixotes de 1 hora. A consulta gera várias séries e pode selecionar quais séries mostrar no gráfico de tempo:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selecione **a** opção de exibição de gráficos line:

![Gráfico de linhas](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Linha de referência

Uma linha de referência pode ajudá-lo a identificar facilmente se a métrica excedeu um limiar específico. Para adicionar uma linha a um gráfico, estenda o conjunto de dados com uma coluna constante:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Linha de referência](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Múltiplas dimensões
Expressões múltiplas na cláusula `by` de `summarize` criar várias linhas nos resultados, uma para cada combinação de valores.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quando vê os resultados como um gráfico, utiliza a primeira coluna a partir da cláusula `by`. O exemplo seguinte mostra um gráfico de coluna empilhado utilizando o _EventID._ As dimensões devem ser de `string` tipo, por isso, neste exemplo, o _EventID_ está a ser lançado para a cadeia. 

![Gráfico de bar EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Pode alternar entre seleção do dropdown com o nome da coluna. 

![Gráfico de barras Tipo de conta](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar a linguagem de [consulta Kusto](/azure/kusto/query/) com dados de registo do Monitor Azure:

- [Operações de cordas](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Junta-se](joins.md)
