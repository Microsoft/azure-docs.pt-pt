---
title: Criando gráficos e diagramas de consultas Azure Monitor log | Microsoft Docs
description: Descreve várias visualizações no Azure Monitor para exibir os dados de log de maneiras diferentes.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ca60ed68277486ca5ed7d7d90ad72506a4844936
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75398072"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Criando gráficos e diagramas de consultas Azure Monitor log

> [!NOTE]
> Você deve concluir [agregações avançadas em consultas de Azure monitor log antes de](advanced-aggregations.md) concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve várias visualizações no Azure Monitor para exibir os dados de log de maneiras diferentes.

## <a name="charting-the-results"></a>Criação de gráficos dos resultados
Comece examinando quantos computadores existem por sistema operacional, durante a última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Por padrão, os resultados são exibidos como uma tabela:

![Tabelas](media/charts/table-display.png)

Para obter uma exibição melhor, selecione **gráfico**e escolha a opção **pizza** para visualizar os resultados:

![Gráfico circular](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Gráficos de
Mostre os percentuais médio, 50 º e 95 º do tempo do processador em compartimentos de 1 hora. A consulta gera várias séries e você pode selecionar qual série deve ser mostrada no gráfico de tempo:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selecione a opção de exibição de gráfico de **linhas** :

![Gráfico de linhas](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Linha de referência

Uma linha de referência pode ajudá-lo a identificar facilmente se a métrica excedeu um limite específico. Para adicionar uma linha a um gráfico, estenda o conjunto de um com uma coluna constante:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Linha de referência](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Várias dimensões
Várias expressões na cláusula `by` de `summarize` criar várias linhas nos resultados, uma para cada combinação de valores.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quando você exibe os resultados como um gráfico, ele usa a primeira coluna da cláusula `by`. O exemplo a seguir mostra um gráfico de colunas empilhadas usando o _EventID._ As dimensões devem ser do tipo `string`, portanto, neste exemplo, o _EventID_ está sendo convertido em String. 

![Gráfico de barras EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Você pode alternar entre selecionando o menu suspenso com o nome da coluna. 

![Tipo de conta de gráfico de barras](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com Azure monitor dados de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
