---
title: Consultas de alerta de log no Monitor Azure / Microsoft Docs
description: Fornece recomendações sobre a escrita de consultas eficientes para alertas de registo nas atualizações do Monitor Do Azure e um processo de conversão de consultas existentes.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667793"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Consultas de alerta de log no Monitor Azure
As regras de [alerta baseadas nos registos do Monitor Azure](alerts-unified-log.md) funcionam a intervalos regulares, pelo que deve certificar-se de que são escritas para minimizar as despesas gerais e a latência. Este artigo fornece recomendações sobre a escrita de consultas eficientes para alertas de registo e um processo de conversão de consultas existentes. 

## <a name="types-of-log-queries"></a>Tipos de consultas de registo
[As consultas de log in Azure Monitor](../log-query/log-query-overview.md) começam com uma mesa ou um operador de [pesquisa](/azure/kusto/query/searchoperator) ou [sindicato.](/azure/kusto/query/unionoperator)

Por exemplo, a seguinte consulta é consultada para a tabela _SecurityEvent_ e procura identificação específica do evento. Esta é a única mesa que a consulta deve processar.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Consultas que começam com `search` ou `union` permitem pesquisar várias colunas numa mesa ou mesmo em várias tabelas. Os seguintes exemplos mostram múltiplos métodos para pesquisar o termo _Memória:_

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Embora `search` e `union` sejam úteis durante a exploração de dados, pesquisando termos em todo o modelo de dados, eles são menos eficientes do que usar uma tabela, uma vez que devem digitalizar em várias tabelas. Uma vez que as consultas nas regras de alerta são executadas em intervalos regulares, isto pode resultar em excesso de excesso de latência ao alerta. Devido a esta sobrecarga, as consultas para regras de alerta de log em Azure devem sempre começar com uma tabela para definir um âmbito claro, o que melhora tanto o desempenho da consulta como a relevância dos resultados.

## <a name="unsupported-queries"></a>Consultas não apoiadas
A partir de 11 de janeiro de 2019, a criação ou modificação das regras de alerta de registo que utilizam `search`– ou `union` operadores não serão suportados no portal Azure. A utilização destes operadores numa regra de alerta devolverá uma mensagem de erro. As regras de alerta existentes e as regras de alerta criadas e editadas com a API log analytics não são afetadas por esta alteração. Deve ainda considerar alterar quaisquer regras de alerta que utilizem este tipo de consultas para melhorar a sua eficiência.  

As regras de alerta de log utilizando [consultas de recursos cruzados](../log-query/cross-workspace-query.md) não são afetadas por esta alteração, uma vez que as consultas de recursos cruzados utilizam `union`, o que limita o âmbito de consulta a recursos específicos. Isto não é equivalente a `union *` que não podem ser utilizados.  O seguinte exemplo seria válido numa regra de alerta de registo:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[A consulta de recursos cruzados](../log-query/cross-workspace-query.md) em alertas de registo é suportada na nova [API agendadaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor utiliza o [legado Log Analytics Alert API](api-alerts.md) para criar novas regras de alerta de log a partir do portal Azure, a menos que troque do legado Log [Alerts API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a troca, a nova API torna-se o padrão para novas regras de alerta no portal Azure e permite criar regras de alerta de consulta de recursos cruzados. Pode criar regras de alerta de consulta [de recursos cruzados](../log-query/cross-workspace-query.md) sem elo, utilizando o [modelo ARM para a API agendada](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , mas esta regra de alerta é controlável apesar da [API agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não do portal Azure.

## <a name="examples"></a>Exemplos
Os exemplos seguintes incluem consultas de registo que usam `search` e `union` e fornecem passos que pode usar para modificar estas consultas para uso com regras de alerta.

### <a name="example-1"></a>Exemplo 1
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar a tabela a que as propriedades pertencem:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

O resultado desta consulta mostraria que a propriedade _ContraNome_ veio da tabela _Perf._ 

Pode utilizar este resultado para criar a seguinte consulta que utilizaria para a regra de alerta:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exemplo 2
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar a tabela a que as propriedades pertencem:

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
| count 
```
 

### <a name="example-3"></a>Exemplo 3

Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que utiliza tanto `search` como `union` para obter informações de desempenho: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar a tabela a que as propriedades na primeira parte da consulta pertencem: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

O resultado desta consulta mostraria que todas estas propriedades vieram da mesa _Perf._ 

Use agora `union` com comando `withsource` para identificar que mesa de origem contribuiu para cada linha.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

O resultado desta consulta mostraria que estas propriedades também vieram da tabela _Perf._ 

Pode utilizar estes resultados para criar a seguinte consulta que utilizaria para a regra de alerta: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Exemplo 4
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que se junta aos resultados de duas consultas `search`:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Para modificar a consulta, comece por utilizar a seguinte consulta para identificar a tabela que contém as propriedades do lado esquerdo da união: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

O resultado indica que as propriedades do lado esquerdo da junta pertencem à tabela _SecurityEvent._ 

Agora use a seguinte consulta para identificar a tabela que contém as propriedades no lado direito da união: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
O resultado indica que as propriedades do lado direito da união pertencem à tabela Heartbeat. 

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
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [alertas](alerts-log.md) de log no Monitor Azure.
- Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md).

