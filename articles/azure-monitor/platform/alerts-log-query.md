---
title: Consultas de alertas de registo no Azure Monitor | Documentos da Microsoft
description: Fornece recomendações sobre como escrever consultas eficientes para os alertas de registo em atualizações do Azure Monitor e um processo para a conversão de consultas existentes.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995980"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Consultas de alertas de registo no Azure Monitor
[Regras com base em registos do Azure Monitor de alerta](alerts-unified-log.md) executar em intervalos regulares, por isso, deve garantir que são escritos para minimizar a sobrecarga e a latência. Este artigo fornece recomendações sobre como escrever consultas eficientes para os alertas de registo e um processo para a conversão de consultas existentes. 

## <a name="types-of-log-queries"></a>Tipos de consultas de registo
[Registar as consultas no Azure Monitor](../log-query/log-query-overview.md) começar com qualquer um de uma tabela ou um [pesquisa](/azure/kusto/query/searchoperator) ou [União](/azure/kusto/query/unionoperator) operador.

Por exemplo a consulta seguinte tem um âmbito para o _SecurityEvent_ tabela e procura por ID de evento específico. Esta é a única tabela que deve processar a consulta.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Consultas que começam com `search` ou `union` permitem pesquisar em várias colunas numa tabela ou até mesmo várias tabelas. Os exemplos seguintes mostram vários métodos para pesquisa o termo _memória_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Embora `search` e `union` são úteis durante a exploração de dados, pesquisar termos em relação ao modelo de dados inteiro, eles são menos eficientes do que utilizar uma tabela, uma vez que eles devem analisar em várias tabelas. Uma vez que as consultas nas regras de alerta são executadas em intervalos regulares, isso pode resultar numa sobrecarga excessiva adicionando latência para o alerta. Devido a essa sobrecarga, as consultas para regras de alerta de registo no Azure sempre devem começar com uma tabela para definir um âmbito claro, o que melhora o desempenho da consulta e a relevância dos resultados.

## <a name="unsupported-queries"></a>Consultas não suportadas
A partir de Janeiro 11,2019, criar ou modificar regras de alerta de registo que utilizam `search`, ou `union` operadores não será suportado no portal do Azure. Usando esses operadores numa regra de alerta irá devolver uma mensagem de erro. Regras de alerta existentes e regras de alerta criada e editada com a API de análise de registo não são afetadas por esta alteração. Deve ainda considerar alterar qualquer alerta de regras que utilizam esses tipos de consultas para melhorar a eficiência.  

Inicie sessão com as regras de alerta [consultas entre recursos](../log-query/cross-workspace-query.md) não são afetadas por esta alteração, uma vez que utilizam consultas entre recursos `union`, que limita o âmbito de consulta a recursos específicos. Isso não é equivalente de `union *` que não pode ser utilizado.  O exemplo seguinte seria válido numa regra de alerta de registo:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Entre recursos consulta](../log-query/cross-workspace-query.md) no registo de alertas é suportado no novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por predefinição, o Azure Monitor utiliza o [herdados API de alerta do Log Analytics](api-alerts.md) para a criação de novo log de regras de alerta no portal do Azure, a menos que alternar do [API herdada de alertas de registo](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API se tornará o padrão de novas regras de alerta no portal do Azure e permite-lhe criar regras de alertas do log de consulta de entre recursos. Pode criar [entre recursos consulta](../log-query/cross-workspace-query.md) regras de alerta de registo sem fazer o comutador, utilizando o [modelo ARM para scheduledQueryRules API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, mas esta regra de alerta é gerenciável no entanto [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não no portal do Azure.

## <a name="examples"></a>Exemplos
Os exemplos seguintes incluem consultas de registo que usam `search` e `union` e fornecem etapas que pode usar para modificar estas consultas para utilização com regras de alerta.

### <a name="example-1"></a>Exemplo 1
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho usando `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Para modificar esta consulta, comece por usar a seguinte consulta para identificar a tabela que as propriedades pertencem a:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

O resultado desta consulta mostraria que o _CounterName_ propriedade veio a _desempenho_ tabela. 

Pode utilizar este resultado para criar a seguinte consulta que usaria para a regra de alerta:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exemplo 2
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que recupera informações de desempenho usando `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Para modificar esta consulta, comece por usar a seguinte consulta para identificar a tabela que as propriedades pertencem a:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

O resultado desta consulta mostraria que o _ObjectName_ e _CounterName_ propriedade veio o _desempenho_ tabela. 

Pode utilizar este resultado para criar a seguinte consulta que usaria para a regra de alerta:

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
 

Para modificar esta consulta, comece por usar a seguinte consulta para identificar a tabela que as propriedades na primeira parte da consulta pertencem a: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

O resultado desta consulta mostraria que todas essas propriedades vieram os _Perf_ tabela. 

Agora, usar `union` com `withsource` comando para identificar a tabela de origem tem contribuído com cada linha.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

O resultado desta consulta mostraria que essas propriedades também vieram os _Perf_ tabela. 

Pode usar esses resultados para criar a seguinte consulta que usaria para a regra de alerta: 

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
Pretende criar uma regra de alerta de registo utilizando a seguinte consulta que associa os resultados de dois `search` consultas:

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
 

Para modificar a consulta, comece por usar a seguinte consulta para identificar a tabela que contém as propriedades no lado esquerdo da associação: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

O resultado indica que as propriedades no lado esquerdo da associação pertencem à _SecurityEvent_ tabela. 

Agora, utilize a seguinte consulta para identificar a tabela que contém as propriedades no lado direito da associação: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
O resultado indica que as propriedades no lado direito da associação pertencem à tabela de Heartbeat. 

Pode usar esses resultados para criar a seguinte consulta que usaria para a regra de alerta: 


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

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [alertas de registo](alerts-log.md) no Azure Monitor.
- Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md).

