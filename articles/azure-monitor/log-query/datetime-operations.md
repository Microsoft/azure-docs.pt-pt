---
title: Trabalhando com valores de data e hora em consultas de log de Azure Monitor | Microsoft Docs
description: Descreve como trabalhar com dados de data e hora em Azure Monitor consultas de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d659be5b817317e7cec5726718f154825674349e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365347"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Trabalhando com valores de data e hora em consultas de log de Azure Monitor

> [!NOTE]
> Você deve concluir [a introdução ao portal de análise](get-started-portal.md) e [começar a usar as consultas antes de](get-started-queries.md) concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas Azure Monitor log.


## <a name="date-time-basics"></a>Noções básicas de data e hora
A linguagem de consulta Kusto tem dois tipos de dados principais associados a datas e horas: DateTime e TimeSpan. Todas as datas são expressas em UTC. Embora haja suporte para vários formatos DateTime, o formato ISO8601 é preferencial. 

Os TimeSpans são expressos como um decimal seguido por uma unidade de tempo:

|abreviada   | unidade de tempo    |
|:---|:---|
|d           | dia          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milésimo  |
|microssegundos | microssegundos  |
|traços        | 100 nanossegundos   |

DateTimes podem ser criados por meio da conversão de uma cadeia de caracteres usando o operador de `todatetime`. Por exemplo, para examinar as pulsações de VM enviadas em um período específico, use o operador `between` para especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar um DateTime com o presente. Por exemplo, para ver todas as pulsações nos últimos dois minutos, você pode usar o operador de `now` junto com um TimeSpan que representa dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Um atalho também está disponível para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

O método mais curto e legível, embora esteja usando o operador de `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Suponha que, em vez de saber a hora de início e término, você saiba a hora de início e a duração. Você pode reescrever a consulta da seguinte maneira:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Convertendo unidades de tempo
Talvez você queira expressar um DateTime ou TimeSpan em uma unidade de tempo diferente do padrão. Por exemplo, se você estiver revisando eventos de erro dos últimos 30 minutos e precisar de uma coluna calculada mostrando quanto tempo atrás o evento ocorreu:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

A coluna `timeAgo` contém valores como: "00:09:31.5118992", o que significa que eles são formatados como hh: mm: SS. fffffff. Se você quiser formatar esses valores para a `numver` de minutos desde a hora de início, divida esse valor por "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregações e bucketing por intervalos de tempo
Outro cenário comum é a necessidade de obter estatísticas em um determinado período de tempo em um intervalo de tempo específico. Para esse cenário, um operador de `bin` pode ser usado como parte de uma cláusula de resumo.

Use a consulta a seguir para obter o número de eventos que ocorreram a cada 5 minutos durante a última meia hora:

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

Outra maneira de criar buckets de resultados é usar funções, como `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Essa consulta produz os seguintes resultados:

|carimbo de data/hora|contagem_|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5\.416|


## <a name="time-zones"></a>Fusos horários
Como todos os valores de data e hora são expressos em UTC, geralmente é útil converter esses valores no fuso horário local. Por exemplo, use este cálculo para converter UTC para horas PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Valor arredondado para tamanho do compartimento | [bin](/azure/kusto/query/binfunction) |
| Obter uma data ou hora específica | [ago](/azure/kusto/query/agofunction) [now](/azure/kusto/query/nowfunction)   |
| Obter parte do valor | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Obter um valor de data relativa  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFMONTH](/azure/kusto/query/endofmonthfunction) [ENDOFYEAR](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com Azure monitor dados de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Spersão](charts.md)
