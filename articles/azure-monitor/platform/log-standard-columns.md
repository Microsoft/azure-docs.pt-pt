---
title: Colunas padrão nos registos de registos do Azure Monitor Microsoft Docs
description: Descreve colunas que são comuns a vários tipos de dados nos registos do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 01c0b6f280b8179760c6ecc55fd7feca3ddf2080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90039130"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Colunas padrão em Registos monitores Azure
Os dados em Registos monitores Azure são [armazenados como um conjunto de registos num espaço de trabalho do Log Analytics ou na aplicação Application Insights](../log-query/logs-structure.md), cada um com um determinado tipo de dados que tem um conjunto único de colunas. Muitos tipos de dados terão colunas padrão que são comuns em vários tipos. Este artigo descreve estas colunas e fornece exemplos de como pode usá-las em consultas.

Aplicações baseadas em espaço de trabalho em Application Insights armazenam os seus dados num espaço de trabalho do Log Analytics e utilizam as mesmas colunas padrão que outras tabelas no espaço de trabalho. As aplicações clássicas armazenam os seus dados separadamente e têm diferentes colunas padrão, conforme especificado neste artigo.

> [!NOTE]
> Algumas das colunas padrão não aparecerão na vista de esquemas ou intellisense no Log Analytics, e não aparecerão nos resultados da consulta a menos que especifique explicitamente a coluna na saída.

## <a name="timegenerated-and-timestamp"></a>TempoGerertado e com hora
As colunas **TimeGenerated** (Log Analytics workspace) e **timetamp** (Application Insights application) contêm a data e a hora em que o registo foi criado pela fonte de dados. Consulte [o tempo de ingestão de dados do Registo no Azure Monitor](data-ingestion-time.md) para obter mais detalhes.

**TimeGenerated** e **timetamp** fornecem uma coluna comum para usar para filtrar ou resumir pelo tempo. Quando seleciona um intervalo de tempo para uma visualização ou painel de instrumentos no portal Azure, utiliza timeGenerated ou timetamp para filtrar os resultados. 

### <a name="examples"></a>Exemplos

A seguinte consulta devolve o número de erros criados para cada dia na semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A seguinte consulta devolve o número de exceções criadas para cada dia na semana anterior.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
A coluna ** \_ TimeReceived** contém a data e a hora em que o registo foi recebido pelo ponto de ingestão do Monitor Azure na nuvem Azure. Isto pode ser útil para identificar problemas de latência entre a fonte de dados e a nuvem. Um exemplo seria um problema de rede que causasse um atraso com o envio de dados de um agente. Consulte [o tempo de ingestão de dados do Registo no Azure Monitor](data-ingestion-time.md) para obter mais detalhes.

A seguinte consulta dá a latência média por hora para registos de eventos de um agente. Isto inclui o tempo do agente para a nuvem e o tempo total para o registo estar disponível para consultas de registo.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Tipo e itemType
As colunas **Type** (Log Analytics workspace) e **itemType** (application Application Insights) detêm o nome da tabela a partir da qual o registo foi recuperado, do qual também pode ser considerado o tipo de registo. Esta coluna é útil em consultas que combinam registos de várias tabelas, como as que utilizam o `search` operador, para distinguir entre registos de diferentes tipos. **$table** pode ser usado no lugar do **Tipo** em alguns lugares.

### <a name="examples"></a>Exemplos
A seguinte consulta devolve a contagem de registos por tipo recolhidos durante a última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
A coluna ** \_ ItemId** detém um identificador único para o registo.


## <a name="_resourceid"></a>\_ResourceId
A coluna ** \_ ResourceId** detém um identificador único para o recurso a que o registo está associado. Isto dá-lhe uma coluna padrão para usar para estender a sua consulta apenas para registos a partir de um determinado recurso, ou para juntar dados relacionados em várias tabelas.

Para os recursos Azure, o valor de **_ResourceId** é o [URL de ID de recurso Azure](../../azure-resource-manager/templates/template-functions-resource.md). A coluna está atualmente limitada aos recursos da Azure, mas será estendida a recursos fora de Azure, como computadores no local.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm ID de recurso Azure ou pelo menos partes dele como ID de subscrição. Embora estes campos sejam mantidos para retrocompatibilidade, recomenda-se usar a _ResourceId para executar a correlação cruzada, uma vez que será mais consistente.

### <a name="examples"></a>Exemplos
A seguinte consulta junta dados de desempenho e evento para cada computador. Mostra todos os eventos com um ID de _101_ e utilização de processadores acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A seguinte consulta junta-se aos registos _da AzureActivity_ com os registos _SecurityEvent._ Mostra todas as operações de atividade com utilizadores que foram iniciados nestas máquinas.

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

As seguintes análises de consulta **_ResourceId** e agrega volumes de dados faturados por subscrição do Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Utilize estas `union withsource = tt *` consultas com moderação, uma vez que as verificações em todos os tipos de dados são dispendiosas para executar.

## <a name="_isbillable"></a>\_IsBillable
A coluna ** \_ IsBillable** especifica se os dados ingeridos são faturados. Os dados com ** \_ o IsBillable** igual `false` a serem recolhidos gratuitamente e não cobrados na sua conta Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores que enviam tipos de dados faturados, utilize a seguinte consulta:

> [!NOTE]
> Utilize consultas com `union withsource = tt *` moderação, pois as verificações em todos os tipos de dados são caras para executar. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isto pode ser estendido para devolver a contagem de computadores por hora que estão a enviar tipos de dados faturados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
A coluna ** \_ BilledSize** especifica o tamanho dos bytes de dados que serão faturados na sua conta Azure se ** \_ o IsBillable** for verdadeiro.


### <a name="examples"></a>Exemplos
Para ver o tamanho dos eventos faturados por computador, utilize a `_BilledSize` coluna que fornece o tamanho dos bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturados por subscrição, utilize a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturados por grupo de recursos, utilize a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Para ver a contagem de eventos ingeridos por computador, utilize a seguinte consulta:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver a contagem de eventos faturados por computador, utilize a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Para ver a contagem de tipos de dados faturais de um computador específico, utilize a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre como [os dados de registo do Azure Monitor são armazenados](../log-query/log-query-overview.md).
- Obtenha uma lição sobre [escrever consultas de registo](../log-query/get-started-queries.md).
- Obtenha uma lição sobre [juntar mesas em consultas de log](../log-query/joins.md).

