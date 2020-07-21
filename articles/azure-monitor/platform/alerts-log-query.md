---
title: Consultas de alerta de registo no Azure Monitor / Microsoft Docs
description: Fornece recomendações sobre a escrita de consultas eficientes para alertas de registo em atualizações do Azure Monitor e um processo de conversão de consultas existentes.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: be2d49a824066b8926ae455978facb34c0b44310
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505470"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Consultas de alerta de registo no Monitor Azure
[As regras de alerta baseadas nos registos do Azure Monitor](alerts-unified-log.md) são executadas em intervalos regulares, pelo que deve certificar-se de que são escritas para minimizar a sobrecarga e a latência. Este artigo fornece recomendações sobre a escrita de consultas eficientes para alertas de registo e um processo de conversão de consultas existentes. 

## <a name="types-of-log-queries"></a>Tipos de consultas de log
[As consultas de registo no Azure Monitor](../log-query/log-query-overview.md) começam com uma mesa ou um [operador de pesquisa](/azure/kusto/query/searchoperator) ou de [sindicato.](/azure/kusto/query/unionoperator)

Por exemplo, a seguinte consulta é traçada para a tabela _SecurityEvent_ e procura identificação específica do evento. Esta é a única tabela que a consulta deve processar.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Consultas que começam `search` ou permitem pesquisar em `union` várias colunas numa mesa ou mesmo em várias tabelas. Os exemplos a seguir mostram múltiplos métodos para pesquisar o termo _Memória_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Embora `search` e `union` sejam úteis durante a exploração de dados, pesquisando termos sobre todo o modelo de dados, eles são menos eficientes do que usar uma tabela, uma vez que eles devem digitalizar em várias tabelas. Uma vez que as consultas em regras de alerta são executadas em intervalos regulares, isso pode resultar em excesso de sobrecarga adicionando latência ao alerta. Por causa desta sobrecarga, as consultas para regras de alerta de registo em Azure devem sempre começar com uma tabela para definir um âmbito claro, o que melhora tanto o desempenho da consulta como a relevância dos resultados.

## <a name="unsupported-queries"></a>Consultas não suportadas
A partir de 11 de janeiro de 2019, a criação ou modificação das regras de alerta de registo que utilizam `search` , ou `union` os operadores não serão suportados no portal Azure. A utilização destes operadores numa regra de alerta devolverá uma mensagem de erro. As regras de alerta existentes e as regras de alerta criadas e editadas com a API Log Analytics não são afetadas por esta alteração. Deve ainda considerar alterar quaisquer regras de alerta que utilizem este tipo de consultas para melhorar a sua eficiência.  

As regras de alerta de registo que utilizam [consultas de recursos cruzados](../log-query/cross-workspace-query.md) não são afetadas por esta alteração, uma vez que as consultas de recursos `union` cruzados utilizam, o que limita o âmbito de consulta a recursos específicos. Isto não é equivalente ao `union *` qual não pode ser utilizado.  O exemplo a seguir seria válido numa regra de alerta de registo:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[A consulta de recursos cruzados](../log-query/cross-workspace-query.md) nos alertas de registo é suportada na nova [API agendada deQueryRules](/rest/api/monitor/scheduledqueryrules). Por predefinição, o Azure Monitor utiliza a [api de alerta de log analytics para](api-alerts.md) criar novas regras de alerta de registo a partir do portal Azure, a menos que mude de [API de alertas](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)de registo legados . Após a troca, a nova API torna-se o padrão para novas regras de alerta no portal Azure e permite criar regras de alerta de registo de consulta de recursos cruzados. Pode criar regras de alerta de registo [de consulta de recursos transversais](../log-query/cross-workspace-query.md) sem fazer o switch utilizando o modelo ARM para a API agendada para a [API de Regras Descomulosas agendadas](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas esta regra de alerta é controlável embora [agendada API](/rest/api/monitor/scheduledqueryrules) e não a partir do portal Azure.

## <a name="examples"></a>Exemplos
Os exemplos seguintes incluem consultas de registo que usam `search` e `union` fornecem passos que você pode usar para modificar estas consultas para uso com regras de alerta.

### <a name="example-1"></a>Exemplo 1
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search` : 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar o quadro a que as propriedades pertencem:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

O resultado desta consulta mostraria que a propriedade _CounterName_ veio da tabela _Perf._ 

Pode utilizar este resultado para criar a seguinte consulta que utilizaria para a regra de alerta:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exemplo 2
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho utilizando `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
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
| count 
```
 

### <a name="example-3"></a>Exemplo 3

Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que utiliza ambos `search` e `union` para obter informações de desempenho: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Para modificar esta consulta, comece por utilizar a seguinte consulta para identificar o quadro a que as propriedades na primeira parte da consulta pertencem: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

O resultado desta consulta mostraria que todas estas propriedades provinham da tabela _Perf._ 

Agora use `union` com comando para identificar qual tabela de `withsource` origem contribuiu cada linha.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
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
| count 
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
)  
on Hour 
| count 
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

 
O resultado indica que as propriedades no lado direito da junta pertencem à tabela Heartbeat. 

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

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [os alertas de registo](alerts-log.md) no Azure Monitor.
- Saiba mais [sobre consultas de registo.](../log-query/log-query-overview.md)
