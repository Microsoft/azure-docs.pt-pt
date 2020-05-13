---
title: Propriedades standard em registos de registos do Monitor Azure / Microsoft Docs
description: Descreve propriedades que são comuns a vários tipos de dados em registos do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: b0ec666f2cfadc3a1571f3ed1d26c92bcbbca3a2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196242"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Propriedades standard em Registos de Monitor Estoque Azure
Os dados em Registos do Monitor Do Azure são [armazenados como um conjunto de registos numa aplicação log Analytics ou aplicação Application Insights,](../log-query/logs-structure.md)cada um com um determinado tipo de dados que tem um conjunto único de propriedades. Muitos tipos de dados terão propriedades padrão que são comuns em vários tipos. Este artigo descreve estas propriedades e fornece exemplos de como pode usá-las em consultas.

> [!IMPORTANT]
> Se estiver a utilizar a APM 2.1, as aplicações Application Insights são armazenadas num espaço de trabalho de Log Analytics com todos os outros dados de registo. As tabelas foram rebatizadas e reestruturadas, mas têm as mesmas informações que as tabelas do pedido de Insights de Aplicação. Estas novas tabelas têm as mesmas propriedades padrão que outras tabelas no espaço de trabalho Log Analytics.

> [!NOTE]
> Algumas das propriedades padrão não aparecerão na vista do esquema ou no intellisense no Log Analytics, e não aparecerão em resultados de consulta a menos que especifique explicitamente a propriedade na saída.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
As propriedades **TimeGenerated** (Log Analytics) e **timestamp** (aplicação Application Insights) contêm a data e a hora que o registo foi criado pela fonte de dados. Consulte o tempo de [ingestão](data-ingestion-time.md) de dados de registo no Monitor Azure para obter mais detalhes.

**TimeGenerated** e **timestamp** fornecem uma propriedade comum para filtrar ou resumir pelo tempo. Quando seleciona um intervalo de tempo para uma vista ou painel no portal Azure, utiliza timeGenerated ou timestamp para filtrar os resultados. 

### <a name="examples"></a>Exemplos

A seguinte consulta devolve o número de erros criados para cada dia na semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A consulta seguinte devolve o número de exceções criadas para cada dia na semana anterior.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
A propriedade ** \_ TimeReceived** contém a data e hora que o registo foi recebido pelo ponto de ingestão do Monitor Azure na nuvem Azure. Isto pode ser útil para identificar problemas de latência entre a fonte de dados e a nuvem. Um exemplo seria um problema de networking que causaria um atraso com a envio de dados de um agente. Consulte o tempo de [ingestão](data-ingestion-time.md) de dados de registo no Monitor Azure para obter mais detalhes.

A seguinte consulta dá a latência média por hora para registos de eventos de um agente. Isto inclui o tempo do agente para a nuvem e o tempo total para que o registo esteja disponível para consultas de registo.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Tipo e artigoTipo
As propriedades **do Tipo** (Log Analytics) e **do itemType** (aplicação Application Insights) possuem o nome da tabela a que o registo foi recuperado, do qual também pode ser considerado o tipo de registo. Esta propriedade é útil em consultas que combinam registos de várias tabelas, como as que usam o `search` operador, para distinguir entre registos de diferentes tipos. **$table** pode ser usado no lugar do **Tipo** em alguns lugares.

### <a name="examples"></a>Exemplos
A seguinte consulta devolve a contagem de registos por tipo recolhido ao longo da última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
A propriedade ** \_ ItemId** detém um identificador único para o registo.


## <a name="_resourceid"></a>\_ResourceId
A propriedade ** \_ ResourceId** detém um identificador único para o recurso com o que o registo está associado. Isto dá-lhe uma propriedade padrão para usar para analisar a sua consulta apenas para registos a partir de um determinado recurso, ou para juntar dados relacionados em várias tabelas.

Para os recursos Azure, o valor do **_ResourceId** é o URL de ID de [recurso Azure.](../../azure-resource-manager/templates/template-functions-resource.md) A propriedade está atualmente limitada aos recursos Azure, mas será estendida a recursos fora de Azure, como computadores no local.

> [!NOTE]
> Alguns tipos de dados já têm campos que contêm ID de recurso Azure ou pelo menos partes dele como ID de subscrição. Embora estes campos sejam mantidos para retrocompatibilidade, recomenda-se utilizar o _ResourceId para realizar a correlação cruzada, uma vez que será mais consistente.

### <a name="examples"></a>Exemplos
A seguinte consulta junta dados de desempenho e eventos para cada computador. Mostra todos os eventos com uma identificação de _101_ e utilização de processador acima de 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A seguinte consulta junta-se aos registos _da AzureActivity_ com registos _securityEvent._ Mostra todas as operações de atividade com utilizadores que foram iniciados nestas máquinas.

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

As seguintes consultas analisam **_ResourceId** e agrega volumes de dados faturados por subscrição azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Utilize estas consultas com moderação, uma vez que as análises através de tipos de `union withsource = tt *` dados são caras para executar.

## <a name="_isbillable"></a>\_IsBillable
A propriedade ** \_ IsBillable** especifica se os dados ingeridos são faturados. Os dados com ** \_ IsBillable** iguais a `false` são recolhidos gratuitamente e não faturados na sua conta Azure.

### <a name="examples"></a>Exemplos
Para obter uma lista de computadores que enviam tipos de dados faturados, utilize a seguinte consulta:

> [!NOTE]
> Utilize consultas com `union withsource = tt *` moderação, pois as digitalizações entre os tipos de dados são caras para executar. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Isto pode ser alargado para devolver a contagem de computadores por hora que estão a enviar tipos de dados faturados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_Faturado
A propriedade ** \_ BilledSize** especifica o tamanho em bytes de dados que serão faturados na sua conta Azure se ** \_ isBillable** for verdadeiro.


### <a name="examples"></a>Exemplos
Para ver o tamanho dos eventos faturados ingeridos por computador, utilize a `_BilledSize` propriedade que fornece o tamanho em bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturados ingeridos por subscrição, utilize a seguinte consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Para ver o tamanho dos eventos faturados ingeridos por grupo de recursos, utilize a seguinte consulta:

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

Para ver a contagem de eventos faturados ingeridos por computador, utilize a seguinte consulta: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Para ver a contagem de tipos de dados faturados de um computador específico, utilize a seguinte consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre como os dados de registo do [Monitor Azure são armazenados](../log-query/log-query-overview.md).
- Obtenha uma lição sobre escrever consultas de [registo.](../../azure-monitor/log-query/get-started-queries.md)
- Obtenha uma lição sobre [juntar mesas em consultas](../../azure-monitor/log-query/joins.md)de log .