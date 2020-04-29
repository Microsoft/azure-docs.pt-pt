---
title: Trabalhar com os valores da data em consultas de registo do Monitor Azure. Microsoft Docs
description: Descreve como trabalhar com dados de data e hora em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655383"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Trabalhar com os valores da data em consultas de registo do Monitor Azure

> [!NOTE]
> Você deve completar [Começar com o portal Analytics](get-started-portal.md) e começar com [consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas de registo do Monitor Azure.


## <a name="date-time-basics"></a>Prazo de data básico
A linguagem de consulta kusto tem dois tipos principais de dados associados a datas e horários: data e tempo. Todas as datas são expressas na UTC. Enquanto vários formatos de data são suportados, o formato ISO8601 é preferido. 

Os tempos são expressos como decimal seguidos por uma unidade do tempo:

|abreviatura   | unidade de tempo    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|t           | second       |
|ms          | milissegundo  |
|microsegundo | microsegundo  |
|tique        | nanossegundo   |

Os tempos de data podem `todatetime` ser criados lançando uma corda utilizando o operador. Por exemplo, para rever os batimentos cardíacos vm `between` enviados num prazo específico, utilize o operador para especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar uma data com o presente. Por exemplo, para ver todos os batimentos cardíacos `now` nos últimos dois minutos, pode utilizar o operador juntamente com uma espálhá-lo que representa dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Está também disponível um atalho para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

No entanto, o método mais `ago` curto e legível é a utilização do operador:
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

## <a name="converting-time-units"></a>Unidades de tempo de conversão
Pode desejar expressar uma data ou uma hora numa unidade de tempo diferente da predefinição. Por exemplo, se estiver a rever os erros dos últimos 30 minutos e precisar de uma coluna calculada que mostre há quanto tempo o evento aconteceu:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

A `timeAgo` coluna tem valores como: "00:09:31.5118992", o que significa que são formatados como hh:mm:ss.fffffff. Se quiser formatar estes `numver` valores até aos minutos desde a hora de início, divida esse valor em "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregações e baldes por intervalos de tempo
Outro cenário comum é a necessidade de obter estatísticas durante um determinado período de tempo num determinado período de tempo. Para este cenário, um `bin` operador pode ser utilizado como parte de uma cláusula de resumo.

Utilize a seguinte consulta para obter o número de eventos que ocorreram a cada 5 minutos durante a última meia hora:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Esta consulta produz a seguinte tabela:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Outra forma de criar baldes de resultados `startofday`é utilizar funções, tais como:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Esta consulta produz os seguintes resultados:

|carimbo de data/hora|count_|
|--|--|
|2018-07-28t00:00:00.000|7,136|
|2018-07-29t00:00:00.000|12,315|
|2018-07-30t00:00:00.000|16,847|
|2018-07-31t00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Fusos horários
Uma vez que todos os valores da data são expressos na UTC, é muitas vezes útil converter estes valores no fuso horário local. Por exemplo, utilize este cálculo para converter utc em tempos PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Valor redondo para o tamanho do caixote do lixo | [caixote do lixo](/azure/kusto/query/binfunction) |
| Obtenha uma data ou hora específicas | [há](/azure/kusto/query/agofunction) [agora](/azure/kusto/query/nowfunction)   |
| Obter parte do valor | [datetime_part](/azure/kusto/query/datetime-partfunction) [mês](/azure/kusto/query/getmonthfunction) [mensal](/azure/kusto/query/monthofyearfunction) do dia [do](/azure/kusto/query/dayofmonthfunction) [dia](/azure/kusto/query/getyearfunction) do dia [da semana](/azure/kusto/query/dayofweekfunction) semana semana do [ano](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Obtenha um valor relativo da data  | [fim do fim](/azure/kusto/query/endofdayfunction) [do](/azure/kusto/query/endofweekfunction) dia final do fim [do mês](/azure/kusto/query/endofmonthfunction) final do [mês](/azure/kusto/query/endofyearfunction) início [do](/azure/kusto/query/startofdayfunction) início do mês início [do](/azure/kusto/query/startofweekfunction) mês início [do](/azure/kusto/query/startofmonthfunction) [ano](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para utilizar a linguagem de [consulta Kusto](/azure/kusto/query/) com dados de registo do Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Escrita de consulta avançada](advanced-query-writing.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
