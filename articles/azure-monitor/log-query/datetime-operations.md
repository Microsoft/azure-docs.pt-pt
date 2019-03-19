---
title: Trabalhar com valores de hora da data em consultas de registo do Azure Monitor | Documentos da Microsoft
description: Descreve como trabalhar com dados de data e hora em consultas de registo do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 402511ba3c45e8bd12cb7f92ecd54f6084c8ada2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009010"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Trabalhar com valores de hora da data em consultas de registo do Azure Monitor

> [!NOTE]
> Deve efetuar [começar com o portal do Analytics](get-started-portal.md) e [introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas de registo do Azure Monitor.


## <a name="date-time-basics"></a>Noções básicas de tempo de data
A linguagem de consulta de Kusto tem dois tipos de dados principal associados com datas e horas: datetime e período de tempo. Todas as datas são expressos em UTC. Embora vários formatos de datetime sejam suportados, o formato de ISO8601 é preferencial. 

Períodos de tempo são expressos como um valor decimal seguido de uma unidade de tempo:

|forma abreviada   | unidade de tempo    |
|:---|:---|
|d           | dia          |
|h           | hora         |
|m           | minuto       |
|s           | segundo       |
|ms          | milissegundo  |
|microssegundo | microssegundo  |
|escala        | nanosecond   |

Datetimes podem ser criadas por lançando uma cadeia, utilizando o `todatetime` operador. Por exemplo, para rever os heartbeats VM enviados num período de tempo específico, utilize o `between` operador especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar um datetime ao presente. Por exemplo, para ver todos os heartbeats durante os últimos dois minutos, pode usar o `now` operador em conjunto com um intervalo de tempo que representa os dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Um atalho também está disponível para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

O método mais curto e mais legível que está a utilizar o `ago` operador:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Suponha que, em vez de saber o tempo de início e de fim, sabe que a hora de início e a duração. Pode reescrever a consulta da seguinte forma:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Conversão de unidades de tempo
Pode querer express um datetime ou timespan numa unidade de tempo diferente da predefinição. Por exemplo, se estiver a rever eventos de erro dos últimos 30 minutos e precisar de uma coluna calculada que mostra como há muito tempo evento ter acontecido:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

O `timeAgo` coluna contém valores, tais como: "00:09:31.5118992", que significa que estão formatados como hh:mm:ss.fffffff. Se pretender formatar estes valores para o `numver` de minutos desde a hora de início, dividi-lo por "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>As agregações e bucketing por intervalos de tempo
Outro cenário comum é a necessidade de obter as estatísticas sobre um determinado período de tempo num grão de tempo específico. Para este cenário, um `bin` operador pode ser usado como parte de uma cláusula de summarize.

Utilize a seguinte consulta para obter o número de eventos que ocorreram durante a última meia hora a cada 5 minutos:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Essa consulta produz a seguinte tabela:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Outra forma de criar registos de resultados é usar as funções, tais como `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Essa consulta produz os seguintes resultados:

|carimbo de data/hora|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Fusos horários
Uma vez que todos os valores de datetime são expressos em UTC, muitas vezes é útil converter esses valores para o fuso horário local. Por exemplo, utilize este cálculo para converter UTC PST vezes:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Valor arredonda para o tamanho de discretização | [bin](/azure/kusto/query/binfunction) |
| Obtenha uma data específica ou a hora | [ago](/azure/kusto/query/agofunction) [now](/azure/kusto/query/nowfunction)   |
| Obtenha a parte do valor | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Obter um valor de data relativa  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Passos Seguintes
Consulte outras lições para utilizar o [linguagem de consulta de Kusto](/azure/kusto/query/) com o Azure Monitor registos de dados:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consultas avançado](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)