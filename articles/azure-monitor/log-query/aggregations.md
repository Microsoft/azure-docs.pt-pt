---
title: Agregações em consultas de log de Azure Monitor | Microsoft Docs
description: Descreve as funções de agregação no Azure Monitor consultas de log que oferecem maneiras úteis de analisar seus dados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 86b84e76b4716c1fddda23a6d52c65c0700c5663
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900420"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregações em consultas de log Azure Monitor

> [!NOTE]
> Você deve concluir [a introdução ao portal de análise](get-started-portal.md) e [começar a usar as consultas antes de](get-started-queries.md) concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve as funções de agregação em Azure Monitor consultas de log que oferecem maneiras úteis de analisar seus dados. Todas essas funções funcionam com o operador de `summarize` que produz uma tabela com resultados agregados da tabela de entrada.

## <a name="counts"></a>Contagens

### <a name="count"></a>count
Conte o número de linhas no conjunto de resultados após a aplicação de qualquer filtro. O exemplo a seguir retorna o número total de linhas na tabela _perf_ dos últimos 30 minutos. O resultado é retornado em uma coluna chamada *count_* , a menos que você atribua um nome específico a ele:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Uma visualização de gráfico temporal pode ser útil para ver uma tendência ao longo do tempo:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

A saída deste exemplo mostra a soma de tendência contagem de registros perf em intervalos de 5 minutos:

![Tendência de contagem](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DContar, dcountif
Use `dcount` e `dcountif` para contar valores distintos em uma coluna específica. A consulta a seguir avalia quantos computadores distintos enviaram pulsações na última hora:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Para contar apenas os computadores Linux que enviaram pulsações, use `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Avaliando subgrupos
Para executar uma contagem ou outras agregações em subgrupos em seus dados, use a palavra-chave `by`. Por exemplo, para contar o número de computadores Linux distintos que enviaram pulsações em cada país/região:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Estados Unidos    | 19                  |
|Canadá           | 3                   |
|Irlanda          | 0                   |
|Reino Unido   | 0                   |
|Países Baixos      | 2                   |


Para analisar até mesmo subgrupos menores de seus dados, adicione nomes de coluna adicionais à seção `by`. Por exemplo, talvez você queira contar os computadores distintos de cada país/região por OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentuais e variância
Ao avaliar valores numéricos, uma prática comum é fazer a média deles usando `summarize avg(expression)`. As médias são afetadas por valores extremos que caracterizam apenas alguns casos. Para resolver esse problema, você pode usar funções menos confidenciais, como `median` ou `variance`.

### <a name="percentile"></a>Percentil
Para localizar o valor mediano, use a função `percentile` com um valor para especificar o percentil:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Você também pode especificar percentuais diferentes para obter um resultado agregado para cada um:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Isso pode mostrar que algumas CPUs de computador têm valores medianos semelhantes, mas embora algumas estejam em constante parte da mediana, outros computadores relataram valores de CPU muito menores e mais altos, o que significa que eles tiveram picos.

### <a name="variance"></a>Varia
Para avaliar diretamente a variância de um valor, use os métodos padrão de desvio e variação:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Uma boa maneira de analisar a estabilidade do uso da CPU é combinar DESVPAD com o cálculo mediano:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com Azure monitor dados de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Spersão](charts.md)
