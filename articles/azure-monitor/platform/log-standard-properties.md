---
title: Registos de propriedades padrão no Azure Monitor | Documentos da Microsoft
description: Descreve as propriedades que são comuns a vários tipos de dados nos registos do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: c01cdb967fd7f9516b4403aa4f0c76f2577d5050
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294727"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Registos de propriedades padrão no Azure Monitor
São de dados de registo no Azure Monitor [armazenado como um conjunto de registos](../log-query/log-query-overview.md), cada um com um tipo de dados específico que tenha um conjunto exclusivo de propriedades. Vários tipos de dados terá propriedades padrão que são comuns em vários tipos. Este artigo descreve essas propriedades e fornece exemplos de como pode usá-los em consultas.

Algumas dessas propriedades são ainda estiver no processo que está a ser implementado, pelo que poderá ver em alguns tipos de dados, mas ainda não em outros.

## <a name="timegenerated"></a>TimeGenerated
O **TimeGenerated** propriedade contém a data e hora em que o registo foi criado. Ele fornece uma propriedade comuns a utilizar para filtragem ou resumir por hora. Quando seleciona um intervalo de tempo para uma vista ou dashboard no portal do Azure, ele usa TimeGenerated para filtrar os resultados.

### <a name="examples"></a>Exemplos

A seguinte consulta devolve o número de eventos de erro criadas para cada dia da semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Type
O **tipo** propriedade contém o nome da tabela que o registo foi obtido a partir da qual pode também ser considerado como o tipo de registo. Esta propriedade é útil para consultas que combinam registos de múltiplas tabelas, tais como os que utilizam o `search` operador, para distinguir entre os registros de diferentes tipos. **$table** pode ser utilizado em vez de **tipo** em alguns locais.

### <a name="examples"></a>Exemplos
A seguinte consulta devolve a contagem de registos por tipo recolhido durante a última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
O  **\_ResourceId** propriedade contém um identificador exclusivo para o recurso que o registo está associado. Isto dá-lhe uma propriedade padrão para utilizar para definir o âmbito sua consulta para apenas os registos de um recurso específico ou para associar dados relacionados em várias tabelas.

Para obter recursos do Azure, o valor de **_ResourceId** é o [URL de ID de recurso do Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). A propriedade está limitada aos recursos do Azure, mas ele será expandido para recursos fora do Azure, tais como computadores no local.

> [!NOTE]
> Alguns tipos de dados já tem campos que contêm o ID de recurso do Azure ou, pelo menos, partes do mesmo, como o ID de subscrição. Embora estes campos são mantidos para compatibilidade com versões anteriores, recomenda-se para utilizar o _ResourceId para efetuar a correlação cruzada, uma vez que é mais consistente.

### <a name="examples"></a>Exemplos
A consulta seguinte associa os dados de desempenho e eventos para cada computador. Mostra todos os eventos com o ID _101_ e mais de 50% de utilização do processador.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A seguinte consulta associações _AzureActivity_ regista com _SecurityEvent_ registos. Mostra todas as operações de atividade com utilizadores que foram registadas para essas máquinas.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

A seguinte consulta analisa **_ResourceId** e agregações cobrados volumes de dados por subscrição do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Utilize estes `union withsource = tt *` moderadamente, uma consulta como análises em todos os tipos de dados são dispendiosas.

## <a name="isbillable"></a>\_IsBillable
O  **\_IsBillable** propriedade especifica se os dados ingeridos estão sujeitos a faturação. Dados com o  **\_IsBillable** igual a _falso_ são recolhidos gratuitamente e não lhe é faturada à sua conta do Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores a enviar de tipos de dados de faturação, utilize a seguinte consulta:

> [!NOTE]
> Utilizar consultas com `union withsource = tt *` com moderação, como análises em todos os tipos de dados são dispendiosas. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isso pode ser estendido para devolver a contagem de computadores por hora, que estão a enviar faturados os tipos de dados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
O  **\_BilledSize** propriedade especifica o tamanho em bytes de dados serão cobrados para a sua conta do Azure se  **\_IsBillable** é verdadeiro.

### <a name="examples"></a>Exemplos
Para ver o tamanho de eventos a cobrar ingeridos por computador, utilize o `_BilledSize` propriedade que fornece o tamanho em bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver a contagem de eventos ingeridos por computador, utilize a seguinte consulta:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver a contagem de eventos a cobrar ingeridos por computador, utilize a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Se quiser ver contagens para tipos de dados cobrar estão a enviar dados para um computador específico, utilize a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [são armazenados dados de registo do Azure Monitor](../log-query/log-query-overview.md).
- Obtenha uma lição sobre [escrever consultas de registo](../../azure-monitor/log-query/get-started-queries.md).
- Obtenha uma lição sobre [associar tabelas nas consultas de registo](../../azure-monitor/log-query/joins.md).
