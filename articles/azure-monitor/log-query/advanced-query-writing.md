---
title: Consultas avançadas no Azure Monitor | Microsoft Docs
description: Este artigo fornece um tutorial para usar o portal de análise para escrever consultas no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 8895224bef037c8c3f8b28a6085359837478d924
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894509"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Gravando consultas avançadas no Azure Monitor

> [!NOTE]
> Você deve concluir a introdução [ao Azure Monitor log Analytics](get-started-portal.md) e [a introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Reutilizando código com Let
Use `let` para atribuir resultados a uma variável e se referir a ele mais tarde na consulta:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Você também pode atribuir valores constantes a variáveis. Isso dá suporte a um método para configurar parâmetros para os campos que você precisa alterar toda vez que executar a consulta. Modifique esses parâmetros conforme necessário. Por exemplo, para calcular o espaço livre em disco e a memória livre (em percentils), em uma determinada janela de tempo:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Isso facilita a alteração do início da hora de término na próxima vez que você executar a consulta.

### <a name="local-functions-and-parameters"></a>Funções e parâmetros locais
Use `let` instruções para criar funções que podem ser usadas na mesma consulta. Por exemplo, defina uma função que usa um campo DateTime (no formato UTC) e o converte em um formato padrão dos EUA. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Imprimir
`print` retornará uma tabela com uma única coluna e uma única linha, mostrando o resultado de um cálculo. Isso geralmente é usado nos casos em que você precisa de um cálculo simples. Por exemplo, para localizar a hora atual em PST e adicionar uma coluna com EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataTable
`datatable` permite que você defina um conjunto de dados. Você fornece um esquema e um conjunto de valores e, em seguida, canaliza a tabela em qualquer outro elemento de consulta. Por exemplo, para criar uma tabela de uso de RAM e calcular o valor médio por hora:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

As construções de DataTable também são muito úteis ao criar uma tabela de pesquisa. Por exemplo, para mapear dados de tabela, como IDs de evento da tabela _SecurityEvent_ , para tipos de evento listados em outro lugar, crie uma tabela de pesquisa com os tipos de evento usando `datatable` e ingresse nesse DataTable com os dados _SecurityEvent_ :

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com Azure monitor dados de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Spersão](charts.md)
