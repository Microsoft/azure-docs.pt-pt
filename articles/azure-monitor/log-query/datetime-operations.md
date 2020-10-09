---
title: Trabalhando com valores de data em consultas de registo do Azure Monitor Microsoft Docs
description: Descreve como trabalhar com dados de data e hora em consultas de registo do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77655383"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Trabalhar com valores de hora de data em consultas de registo do Monitor de Azure

> [!NOTE]
> Deve completar [Começar com o portal Analytics](get-started-portal.md) e começar com [consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas de registo do Azure Monitor.


## <a name="date-time-basics"></a>Básicos da hora da data
A língua de consulta Kusto tem dois principais tipos de dados associados com datas e horários: data e intervalo de tempo. Todas as datas são expressas na UTC. Embora vários formatos de datas sejam suportados, o formato ISO8601 é preferido. 

Os intervalos de tempo são expressos como decimal seguidos por uma unidade de tempo:

|abreviatura   | unidade de tempo    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|t           | second       |
|ms          | milissegundo  |
|microsegundo | microsegundo  |
|tique        | nanosegundo   |

As datas podem ser criadas através da função de uma corda utilizando o `todatetime` operador. Por exemplo, para rever os batimentos cardíacos VM enviados num prazo específico, utilize o `between` operador para especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar uma data com a atual. Por exemplo, para ver todos os batimentos cardíacos nos últimos dois minutos, pode utilizar o `now` operador juntamente com um período de tempo que representa dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Está também disponível um atalho para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

O método mais curto e legível é a utilização do `ago` operador:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Suponha que em vez de saber o início e o fim do tempo, sabe a hora de início e a duração. Pode reescrever a consulta da seguinte forma:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Conversão de unidades de tempo
Pode querer expressar uma data ou intervalo de tempo numa unidade de tempo diferente do padrão. Por exemplo, se estiver a rever os erros dos últimos 30 minutos e precisar de uma coluna calculada que mostre há quanto tempo o evento aconteceu:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

A `timeAgo` coluna contém valores como: "00:09:31.5118992", o que significa que são formatados como hh:mm:ss.fffff. Se pretender formatar estes valores à `numver` hora de início, divida esse valor por "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregações e baldes por intervalos de tempo
Outro cenário comum é a necessidade de obter estatísticas durante um determinado período de tempo num determinado período de tempo. Para este cenário, um `bin` operador pode ser usado como parte de uma cláusula resumida.

Utilize a seguinte consulta para obter o número de eventos que ocorreram a cada 5 minutos durante a última meia hora:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Esta consulta produz o seguinte quadro:  

|Tempogerado (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Outra forma de criar baldes de resultados é usar funções, tais `startofday` como:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Esta consulta produz os seguintes resultados:

|carimbo de data/hora|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Fusos horários
Uma vez que todos os valores da data são expressos na UTC, é muitas vezes útil converter estes valores no timezone local. Por exemplo, utilize este cálculo para converter UTC em tempos PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [tempo de tempo a](/azure/kusto/query/todatetimefunction)  [tempo](/azure/kusto/query/totimespanfunction)  |
| Valor redondo para o tamanho do caixote do lixo | [caixote do lixo](/azure/kusto/query/binfunction) |
| Obtenha uma data ou hora específicas | [atrás](/azure/kusto/query/agofunction) [agora](/azure/kusto/query/nowfunction)   |
| Obtenha parte do valor | [datetime_part](/azure/kusto/query/datetime-partfunction) [o](/azure/kusto/query/getmonthfunction) dia do [dia](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) do dia da [semana](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) do dia da semana do [dia do ano](/azure/kusto/query/weekofyearfunction) |
| Obtenha um valor de data relativa  | [fim da semana](/azure/kusto/query/endofdayfunction) [final](/azure/kusto/query/endofweekfunction) do fim [do](/azure/kusto/query/endofmonthfunction) [ano](/azure/kusto/query/endofyearfunction) início [da semana](/azure/kusto/query/startofdayfunction) [início da semana](/azure/kusto/query/startofweekfunction) do início do [10º ano](/azure/kusto/query/startofyearfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) |

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar a [linguagem de consulta kusto](/azure/kusto/query/) com dados de registo do Azure Monitor:

- [Operações de cadeia](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
