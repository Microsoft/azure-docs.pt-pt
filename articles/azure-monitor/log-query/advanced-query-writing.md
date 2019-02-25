---
title: Avançadas consultas no Azure Monitor do Azure | Documentos da Microsoft
description: Este artigo fornece um tutorial para utilizar o portal de análise para escrever consultas no Azure Monitor.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 0b516dc4fb1ca4736791abdea7f0903b6399fa6e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749907"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Escrever avançadas consultas no Azure Monitor

> [!NOTE]
> Deve efetuar [introdução ao Log Analytics do Azure Monitor](get-started-portal.md) e [introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Reutilização de código com let
Utilize `let` para atribuir os resultados para uma variável e fazer referência a ele mais tarde na consulta:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Também pode atribuir valores constantes para variáveis. Isso oferece suporte a um método para definir os parâmetros para os campos que precisa alterar toda vez que executar a consulta. Modificar os parâmetros conforme necessário. Por exemplo, para calcular o espaço livre em disco e memória livre (em percentis), numa janela de tempo especificado:

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

Isto torna mais fácil alterar o início da hora de fim da próxima vez que executar a consulta.

### <a name="local-functions-and-parameters"></a>Funções locais e parâmetros
Utilize `let` instruções para criar funções que podem ser utilizadas da mesma consulta. Por exemplo, defina uma função que usa um campo datetime (no formato UTC) e converte-o para um formato padrão dos EUA. 

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
`print` Devolve uma tabela com uma única coluna e uma única linha, que mostra o resultado de um cálculo. Isto é frequentemente utilizado em casos em que é necessário um calcuation simple. Por exemplo, para encontrar a hora atual no PST e adicionar uma coluna com EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>DataTable
`datatable` permite-lhe definir um conjunto de dados. Fornecer um esquema e um conjunto de valores e, em seguida, encaminhar a tabela em todos os outros elementos de consulta. Por exemplo, para criar uma tabela de utilização de RAM e calcular o valor médio por hora:

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

Construções de DataTable também são muito úteis durante a criação de uma tabela de pesquisa. Por exemplo, para mapear dados de tabela, como IDs de evento a _SecurityEvent_ tabela, para tipos de eventos listados em outro lugar, crie uma tabela de pesquisa com os tipos de eventos usando `datatable` e Junte-se a essa datatable com  _SecurityEvent_ dados:

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

## <a name="next-steps"></a>Passos Seguintes
Consulte outras lições para utilizar o [linguagem de consulta de Kusto](/azure/kusto/query/) com o Azure Monitor registos de dados:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Estruturas de dados e JSON](json-data-structures.md)
- [Associações](joins.md)
- [Gráficos](charts.md)