---
title: Agregações avançadas em consultas de log de Azure Monitor | Microsoft Docs
description: Descreve algumas das opções de agregação mais avançadas disponíveis para Azure Monitor consultas de log.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f34e71c4e15e3bb09676e366313e90a7261439e5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900429"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agregações avançadas em consultas de log de Azure Monitor

> [!NOTE]
> Você deve concluir as [agregações em Azure monitor consultas](./aggregations.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve algumas das opções de agregação mais avançadas disponíveis para Azure Monitor consultas.

## <a name="generating-lists-and-sets"></a>Gerando listas e conjuntos
Você pode usar `makelist` para dinamizar dados pela ordem de valores em uma coluna específica. Por exemplo, talvez você queira explorar os eventos de pedido mais comuns que ocorrem em seus computadores. Essencialmente, é possível dinamizar os dados pela ordem de EventIDs em cada computador. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| Computador1 | [704, 701, 1501, 1500, 1085, 704, 704, 701] |
| Computador2 | [326.105.302.301.300.102] |
| ... | ... |

`makelist` gera uma lista na ordem em que os dados foram passados para ela. Para classificar eventos do mais antigo para o mais recente, use `asc` na instrução Order em vez de `desc`. 

Também é útil criar uma lista de valores apenas distintos. Isso é chamado de _conjunto_ e pode ser gerado com `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| Computador1 | [704, 701, 1501, 1500, 1085] |
| Computador2 | [326.105.302.301.300.102] |
| ... | ... |

Assim como `makelist`, `makeset` também funciona com dados ordenados e gerará as matrizes com base na ordem das linhas que são passadas para ela.

## <a name="expanding-lists"></a>Expandindo listas
A operação inversa de `makelist` ou `makeset` é `mvexpand`, que expande uma lista de valores para separar linhas. Ele pode ser expandido em qualquer número de colunas dinâmicas, JSON e Array. Por exemplo, você pode verificar a tabela de *pulsação* para soluções que enviam dados de computadores que enviaram uma pulsação na última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| Computador1 | "Security", "Updates", "changeTracking" |
| Computador2 | "segurança", "Atualizações" |
| computer3 | "AntiMalware", "changeTracking" |
| ... | ... |

Use `mvexpand` para mostrar cada valor em uma linha separada em vez de uma lista separada por vírgulas:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| Computador1 | segurança |
| Computador1 | actualiza |
| Computador1 | ChangeTracking |
| Computador2 | segurança |
| Computador2 | actualiza |
| computer3 | AntiMalware |
| computer3 | ChangeTracking |
| ... | ... |


Em seguida, você pode usar `makelist` novamente para agrupar itens e, desta vez, ver a lista de computadores por solução:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Soluções | list_Computer |
|--------------|----------------------|
| segurança | ["Computador1", "Computador2"] |
| actualiza | ["Computador1", "Computador2"] |
| ChangeTracking | ["Computador1", "computer3"] |
| AntiMalware | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Lidando com compartimentos ausentes
Um aplicativo útil do `mvexpand` é a necessidade de preencher valores padrão no para compartimentos ausentes. Por exemplo, suponha que você esteja procurando o tempo de atividade de um determinado computador explorando sua pulsação. Você também deseja ver a origem da pulsação que está na coluna _categoria_ . Normalmente, usaremos uma instrução Resume simples da seguinte maneira:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Categoria | TimeGenerated | contar |
|--------------|----------------------|--------|
| Agente direto | 2017-06-06T17:00:00Z | 15 |
| Agente direto | 2017-06-06T18:00:00Z | 60 |
| Agente direto | 2017-06-06T20:00:00Z | 55 |
| Agente direto | 2017-06-06T21:00:00Z | 57 |
| Agente direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Nesses resultados, embora o Bucket associado a "2017-06-06T19:00:00Z" esteja ausente porque não há dados de pulsação para aquela hora. Use a função `make-series` para atribuir um valor padrão a buckets vazios. Isso irá gerar uma linha para cada categoria com duas colunas de matriz extras, uma para valores e outra para os buckets de tempo correspondentes:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Categoria | contar | TimeGenerated |
|---|---|---|
| Agente direto | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

O terceiro elemento da matriz *count_* é um 0 como esperado e há um carimbo de data/hora correspondente de "2017-06-06T19:00:00.0000000 z" na matriz _TimeGenerated_ . No entanto, esse formato de matriz é difícil de ser lido. Use `mvexpand` para expandir as matrizes e produzir a mesma saída de formato gerada por `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Categoria | TimeGenerated | contar |
|--------------|----------------------|--------|
| Agente direto | 2017-06-06T17:00:00Z | 15 |
| Agente direto | 2017-06-06T18:00:00Z | 60 |
| Agente direto | 2017-06-06T19:00:00Z | 0 |
| Agente direto | 2017-06-06T20:00:00Z | 55 |
| Agente direto | 2017-06-06T21:00:00Z | 57 |
| Agente direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Restringir resultados a um conjunto de elementos: `let`, `makeset`, `toscalar``in`
Um cenário comum é selecionar os nomes de algumas entidades específicas com base em um conjunto de critérios e, em seguida, filtrar um conjunto de dados diferente para esse conjunto de entidades. Por exemplo, você pode encontrar computadores que são conhecidos por ter atualizações ausentes e identificar IPs para os quais esses computadores se chamaram:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Passos seguintes

Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com Azure monitor dados de log:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Spersão](charts.md)