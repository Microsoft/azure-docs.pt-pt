---
title: Propriedades padrão em registros de log Azure Monitor | Microsoft Docs
description: Descreve as propriedades que são comuns a vários tipos de dados em logs de Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 514f98d95090f978395dd3f7decdcc0743a1628a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289157"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Propriedades padrão em logs de Azure Monitor
Os dados em logs de Azure Monitor são [armazenados como um conjunto de registros em um espaço de trabalho log Analytics ou Application insights aplicativo](../log-query/logs-structure.md), cada um com um tipo de dados específico que tem um conjunto exclusivo de propriedades. Muitos tipos de dados terão propriedades padrão comuns em vários tipos. Este artigo descreve essas propriedades e fornece exemplos de como você pode usá-las em consultas.

> [!NOTE]
> Algumas das propriedades padrão não serão mostradas na exibição de esquema ou no IntelliSense em Log Analytics e não serão mostradas nos resultados da consulta, a menos que você especifique explicitamente a propriedade na saída.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
As propriedades **TimeGenerated** (log Analytics espaço de trabalho) e **timestamp** (Application insights aplicativo) contêm a data e a hora em que o registro foi criado pela fonte de dados. Confira [tempo de ingestão de dados de log no Azure monitor](data-ingestion-time.md) para obter mais detalhes.

**TimeGenerated** e **timestamp** fornecem uma propriedade comum a ser usada para filtrar ou resumir por tempo. Quando você seleciona um intervalo de tempo para um modo de exibição ou painel no portal do Azure, ele usa TimeGenerated ou timestamp para filtrar os resultados. 

### <a name="examples"></a>Exemplos

A consulta a seguir retorna o número de eventos de erro criados para cada dia da semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A consulta a seguir retorna o número de exceções criadas para cada dia da semana anterior.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_timereceber
A propriedade **timereceber\_** contém a data e a hora em que o registro foi recebido pelo ponto de ingestão de Azure monitor na nuvem do Azure. Isso pode ser útil para identificar problemas de latência entre a fonte de dados e a nuvem. Um exemplo seria um problema de rede causando um atraso com os dados enviados de um agente. Confira [tempo de ingestão de dados de log no Azure monitor](data-ingestion-time.md) para obter mais detalhes.

A consulta a seguir fornece a latência média por hora para registros de eventos de um agente. Isso inclui o tempo do agente para a nuvem e o tempo total para que o registro esteja disponível para consultas de log.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Digite e itemType
As propriedades **Type** (log Analytics Workspace) e **ItemType** (Application insights Application) mantêm o nome da tabela da qual o registro foi recuperado, que também pode ser considerado como o tipo de registro. Essa propriedade é útil em consultas que combinam registros de várias tabelas, como aquelas que usam o operador de `search`, para distinguir entre registros de tipos diferentes. **$Table** pode ser usado no lugar do **tipo** em alguns lugares.

### <a name="examples"></a>Exemplos
A consulta a seguir retorna a contagem de registros por tipo coletados na última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>ItemId \_
A propriedade **\_ItemId** mantém um identificador exclusivo para o registro.


## <a name="_resourceid"></a>\_ResourceId
A propriedade **\_ResourceId** contém um identificador exclusivo para o recurso ao qual o registro está associado. Isso lhe dá uma propriedade padrão a ser usada para fazer o escopo de sua consulta somente para registros de um recurso específico ou para unir dados relacionados em várias tabelas.

Para recursos do Azure, o valor de **_ResourceId** é a [URL da ID de recurso do Azure](../../azure-resource-manager/templates/template-functions-resource.md). A propriedade está atualmente limitada aos recursos do Azure, mas será estendida para recursos fora do Azure, como computadores locais.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm a ID de recurso do Azure ou pelo menos partes dele, como a ID da assinatura. Embora esses campos sejam mantidos para compatibilidade com versões anteriores, é recomendável usar o _ResourceId para executar a correlação cruzada, pois ele será mais consistente.

### <a name="examples"></a>Exemplos
A consulta a seguir une dados de desempenho e eventos para cada computador. Ele mostra todos os eventos com uma ID de _101_ e a utilização do processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A consulta a seguir une registros _AzureActivity_ com registros _SecurityEvent_ . Ele mostra todas as operações de atividade com usuários que se conectaram a esses computadores.

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

A consulta a seguir analisa **_ResourceId** e agrega os volumes de dados cobrados por assinatura do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Use essas consultas `union withsource = tt *` com moderação, pois as verificações entre os tipos de dados são caras de serem executadas.

## <a name="_isbillable"></a>\_isnotable
A propriedade **Isnotable\_** especifica se os dados ingeridos são faturáveis. Os dados com **\_Isnotable** igual a _false_ são coletados gratuitamente e não são faturados para sua conta do Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores que enviam tipos de dados cobrados, use a seguinte consulta:

> [!NOTE]
> Use consultas com `union withsource = tt *` com moderação, pois as verificações entre os tipos de dados são caras de serem executadas. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isso pode ser estendido para retornar a contagem de computadores por hora que estão enviando tipos de dados cobrados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
A propriedade **\_BilledSize** especifica o tamanho em bytes de dados que serão cobrados em sua conta do Azure se **\_isbillble** for true.


### <a name="examples"></a>Exemplos
Para ver o tamanho dos eventos faturáveis ingeridos por computador, use a propriedade `_BilledSize`, que fornece o tamanho em bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturáveis ingeridos por assinatura, use a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturáveis ingeridos por grupo de recursos, use a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Para ver a contagem de eventos ingeridos por computador, use a seguinte consulta:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver a contagem de eventos faturáveis ingeridos por computador, use a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Para ver a contagem de tipos de dados faturáveis de um computador específico, use a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre como [Azure monitor dados de log são armazenados](../log-query/log-query-overview.md).
- Obtenha uma lição sobre como [escrever consultas de log](../../azure-monitor/log-query/get-started-queries.md).
- Obtenha uma lição sobre como [unir tabelas em consultas de log](../../azure-monitor/log-query/joins.md).
