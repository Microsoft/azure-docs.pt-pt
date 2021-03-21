---
title: Otimização de consultas de alerta de registo | Microsoft Docs
description: Recomendações para escrever consultas de alerta eficientes
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: a7d65d7c65dabde3834458a36b50216878f7cf8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031298"
---
# <a name="optimizing-log-alert-queries"></a>Otimização de consultas de alerta de registo
Este artigo descreve como escrever e converter consultas [de Alerta de Log](./alerts-unified-log.md) para obter um desempenho ideal. Consultas otimizadas reduzem a latência e a carga de alertas, que funcionam com frequência.

## <a name="how-to-start-writing-an-alert-log-query"></a>Como começar a escrever uma consulta de registo de alerta

As consultas de alerta começam por [consulta dos dados de registo no Log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) que indicam o problema. Pode usar o [tópico de exemplos de consulta de alerta](../logs/example-queries.md) para entender o que pode descobrir. Também pode [começar a escrever a sua própria consulta.](../logs/log-analytics-tutorial.md) 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Consultas que indicam o problema e não o alerta

O fluxo de alerta foi construído para transformar os resultados que indicam o problema para um alerta. Por exemplo, num caso de consulta como:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Se a intenção do utilizador for alertar, quando este tipo de evento acontece, a lógica de alerta `count` apia-se à consulta. A consulta que irá decorrer será:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Não há necessidade de adicionar lógica de alerta à consulta e fazer que pode até causar problemas. No exemplo acima, se incluir `count` na sua consulta, resultará sempre no valor 1, uma vez que o serviço de alerta fará `count` de `count` .

### <a name="avoid-limit-and-take-operators"></a>Evitar `limit` e `take` operadores

O uso `limit` e nas consultas pode aumentar a `take` latência e a carga de alertas, uma vez que os resultados não são consistentes com o tempo. Prefere usá-lo apenas se necessário.

## <a name="log-query-constraints"></a>Restrições de consulta de registo
[As consultas de registo no Azure Monitor](../logs/log-query-overview.md) começam com uma [`search`](/azure/kusto/query/searchoperator) mesa, ou [`union`](/azure/kusto/query/unionoperator) operador.

As consultas para as regras de alerta de registo devem sempre começar com uma tabela para definir um âmbito claro, o que melhora tanto o desempenho da consulta como a relevância dos resultados. As consultas em regras de alerta são executadas com frequência, pelo que a utilização e a utilização `search` pode resultar em excesso de `union` latência aérea ao alerta, uma vez que requer a digitalização em várias tabelas. Estes operadores também reduzem a capacidade do serviço de alerta para otimizar a consulta.

Não apoiamos a criação ou modificação de regras de alerta de registo que utilizam `search` ou `union` operadores, esperam consultas de recursos cruzados.

Por exemplo, a seguinte consulta de alerta é traçada para a tabela _SecurityEvent_ e procura identificação específica do evento. É a única mesa que a consulta deve processar.

``` Kusto
SecurityEvent
| where EventID == 4624
```

As regras de alerta de registo que utilizam [consultas de recursos cruzados](../logs/cross-workspace-query.md) não são afetadas por esta alteração, uma vez que as consultas de recursos cruzados utilizam um tipo de , que limita o âmbito de `union` consulta a recursos específicos. O exemplo a seguir seria consulta válida de alerta de registo:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [As consultas de recursos cruzados](../logs/cross-workspace-query.md) são suportadas na nova [API agendada deQueryRules.](/rest/api/monitor/scheduledqueryrules) Se ainda utilizar o [legado Log Analytics Alert API](./api-alerts.md) para criar alertas de registo, pode aprender a mudar [aqui](../alerts/alerts-log-api-switch.md).

## <a name="examples"></a>Exemplos
Os exemplos seguintes incluem consultas de registo que usam `search` e `union` fornecem passos que você pode usar para modificar estas consultas para uso em regras de alerta.

### <a name="example-1"></a>Exemplo 1
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar o quadro a que as propriedades pertencem:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

O resultado desta consulta mostraria que a propriedade _CounterName_ veio da tabela _Perf._

Pode utilizar este resultado para criar a seguinte consulta que utilizaria para a regra de alerta:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Exemplo 2
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar o quadro a que as propriedades pertencem:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

O resultado desta consulta mostraria que a propriedade _ObjectName_ e _CounterName_ veio da tabela _Perf._

Pode utilizar este resultado para criar a seguinte consulta que utilizaria para a regra de alerta:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Exemplo 3

Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que utiliza ambos `search` e `union` para recuperar informações de desempenho: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar o quadro a que as propriedades na primeira parte da consulta pertencem: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

O resultado desta consulta mostraria que todas estas propriedades provinham da tabela _Perf._

Agora use `union` com comando para identificar qual tabela de `withsource` origem contribuiu cada linha.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

O resultado desta consulta mostraria que estas propriedades também provinham da tabela _Perf._

Pode utilizar estes resultados para criar a seguinte consulta que utilizaria para a regra de alerta: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Exemplo 4
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que se junta aos resultados de duas `search` consultas:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Para modificar a consulta, comece por utilizar a seguinte consulta para identificar a tabela que contém as propriedades no lado esquerdo da junta: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

O resultado indica que as propriedades no lado esquerdo da junta pertencem à tabela _SecurityEvent._ 

Agora use a seguinte consulta para identificar a tabela que contém as propriedades no lado direito da junta: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
O resultado indica que as propriedades no lado direito da junta pertencem à tabela _Heartbeat._

Pode utilizar estes resultados para criar a seguinte consulta que utilizaria para a regra de alerta: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os alertas de registo](alerts-log.md) no Azure Monitor.
- Saiba mais [sobre consultas de registo.](../logs/log-query-overview.md)