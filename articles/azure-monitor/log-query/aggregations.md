---
title: Agregações em consultas de registo do Monitor Azure Microsoft Docs
description: Descreve funções de agregação em consultas de registo do Monitor Azure que oferecem formas úteis de analisar os seus dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670309"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregações em consultas de registo do Monitor Azure

> [!NOTE]
> Você deve completar [Começar com o portal Analytics](get-started-portal.md) e começar com [consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve funções de agregação em consultas de registo do Monitor Azure que oferecem formas úteis de analisar os seus dados. Todas estas funções `summarize` funcionam com o operador que produz uma tabela com resultados agregados da tabela de entrada.

## <a name="counts"></a>Contagens

### <a name="count"></a>count
Conte o número de linhas no resultado definido após a aplicação de filtros. O exemplo seguinte devolve o número total de linhas na tabela _Perf_ dos últimos 30 minutos. O resultado é devolvido numa coluna chamada *count_* a menos que lhe atribua um nome específico:


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

Uma visualização de um timechart pode ser útil para ver uma tendência ao longo do tempo:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

A saída deste exemplo mostra a linha de tendência da contagem de registos perf em intervalos de 5 minutos:

![Tendência de contagem](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>contagem, dcountif
Utilizar `dcount` `dcountif` e contar valores distintos numa coluna específica. A seguinte consulta avalia quantos computadores distintos enviaram batimentos cardíacos na última hora:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Para contar apenas os computadores Linux que enviaram batimentos cardíacos, use: `dcountif`

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Avaliação de subgrupos
Para efetuar uma contagem ou outras agregações `by` em subgrupos nos seus dados, utilize a palavra-chave. Por exemplo, para contar o número de computadores Linux distintos que enviaram batimentos cardíacos em cada país/região:

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


Para analisar subgrupos ainda mais pequenos dos seus `by` dados, adicione nomes de colunas adicionais à secção. Por exemplo, é melhor contar os computadores distintos de cada país/região por OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiles e Variação
Ao avaliar valores numéricos, uma prática `summarize avg(expression)`comum é média-los usando . As médias são afetadas por valores extremos que caracterizam apenas alguns casos. Para resolver este problema, pode utilizar funções menos sensíveis, tais como `median` ou `variance`.

### <a name="percentile"></a>Percentil
Para encontrar o valor `percentile` mediano, utilize a função com um valor para especificar o percentil:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Também pode especificar percentículos diferentes para obter um resultado agregado para cada um:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Isto pode mostrar que alguns CPUs de computador têm valores medianos semelhantes, mas enquanto alguns são constantes em torno da mediana, outros computadores reportaram valores de CPU muito mais baixos e mais altos, o que significa que experimentaram picos.

### <a name="variance"></a>Desvio
Para avaliar diretamente a variação de um valor, utilize os métodos de desvio e variação padrão:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Uma boa forma de analisar a estabilidade da utilização do CPU é combinar stdev com o cálculo mediano:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Consulte outras lições para utilizar a linguagem de [consulta Kusto](/azure/kusto/query/) com dados de registo do Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
