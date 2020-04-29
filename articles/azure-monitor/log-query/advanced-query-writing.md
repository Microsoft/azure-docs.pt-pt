---
title: Consultas avançadas no Monitor Azure / Microsoft Docs
description: Este artigo fornece um tutorial para usar o portal Analytics para escrever consultas no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670292"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Escrever consultas avançadas no Monitor Azure

> [!NOTE]
> Deve completar [O Get started com o Azure Monitor Log Analytics](get-started-portal.md) e começar com [consultas](get-started-queries.md) antes de concluir esta aula.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Reutilizar código com let
Utilize `let` para atribuir resultados a uma variável e consulte-o mais tarde na consulta:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Também pode atribuir valores constantes a variáveis. Isto suporta um método para configurar parâmetros para os campos que precisa de alterar sempre que executa a consulta. Modifique os parâmetros conforme necessário. Por exemplo, calcular o espaço de disco livre e a memória livre (em percentículos), numa determinada janela temporal:

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

Isto facilita a mudança do início do tempo final da próxima vez que fizer a consulta.

### <a name="local-functions-and-parameters"></a>Funções e parâmetros locais
Utilize `let` declarações para criar funções que podem ser usadas na mesma consulta. Por exemplo, defina uma função que tome um campo de data (no formato UTC) e converta-a num formato americano padrão. 

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
`print`devolverá uma tabela com uma única coluna e uma única linha, mostrando o resultado de um cálculo. Isto é frequentemente usado em casos em que você precisa de um cálculo simples. Por exemplo, para encontrar o tempo atual em PST e adicionar uma coluna com EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`permite definir um conjunto de dados. Fornece um esquema e um conjunto de valores e, em seguida, tubo a mesa em qualquer outro elemento de consulta. Por exemplo, criar uma tabela de utilização de RAM e calcular o seu valor médio por hora:

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

As construções datatable também são muito úteis na criação de uma mesa de lookup. Por exemplo, mapear dados de tabelas, tais como IDs de eventos da tabela _SecurityEvent,_ para tipos de eventos listados em outros lugares, criar uma tabela de lookup com os tipos de eventos que usam `datatable` e juntar esta datatable com dados do _SecurityEvent:_

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
Consulte outras lições para utilizar a linguagem de [consulta Kusto](/azure/kusto/query/) com dados de registo do Monitor Azure:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Associações](joins.md)
- [Gráficos](charts.md)
