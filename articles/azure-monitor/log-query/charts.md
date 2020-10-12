---
title: Criando gráficos e diagramas a partir de consultas de registo do Azure Monitor Microsoft Docs
description: Descreve várias visualizações no Azure Monitor para exibir os seus dados de registo de diferentes maneiras.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670326"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Criação de gráficos e diagramas a partir de consultas de registo do Azure Monitor

> [!NOTE]
> Deve completar [agregações avançadas em consultas de registo do Azure Monitor](advanced-aggregations.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve várias visualizações no Azure Monitor para exibir os seus dados de registo de diferentes maneiras.

## <a name="charting-the-results"></a>Cartografando os resultados
Comece por rever quantos computadores existem por sistema operativo, durante a última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Por predefinição, os resultados são apresentados como tabela:

![Tabela](media/charts/table-display.png)

Para obter uma melhor vista, selecione **Gráfico**, e escolha a opção **Pie** para visualizar os resultados:

![Gráfico circular](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Mostre a média, 50º e 95º percentais do tempo do processador em caixas de 1 hora. A consulta gera várias séries e pode selecionar quais as séries a apresentar na tabela de tempos:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selecione a opção de visualização do gráfico **de linha:**

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
Expressões múltiplas na `by` cláusula de `summarize` criar várias linhas nos resultados, uma para cada combinação de valores.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quando vê os resultados como um gráfico, utiliza a primeira coluna a partir da `by` cláusula. O exemplo a seguir mostra um gráfico de coluna empilhado utilizando o _EventID._ As dimensões devem ser do `string` tipo, por isso, neste exemplo, o _EventID_ está a ser lançado para cordas. 

![Gráfico de barras EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Pode alternar entre selecionando o dropdown com o nome da coluna. 

![Gráfico de gráfico de gráficos De Contas](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar a [linguagem de consulta kusto](/azure/kusto/query/) com dados de registo do Azure Monitor:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
