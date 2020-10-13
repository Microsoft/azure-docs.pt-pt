---
title: Agregações avançadas em consultas de registo do Azure Monitor Microsoft Docs
description: Descreve algumas das opções de agregação mais avançadas disponíveis para consultas de registo do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324561"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agregações avançadas em consultas de log do Azure Monitor

> [!NOTE]
> Deve completar [agregações em consultas do Azure Monitor](./aggregations.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve algumas das opções de agregação mais avançadas disponíveis para consultas do Azure Monitor.

## <a name="generating-lists-and-sets"></a>Gerando listas e conjuntos
Pode utilizar `makelist` para orientar dados pela ordem dos valores numa determinada coluna. Por exemplo, pode querer explorar os eventos de ordem mais comuns que ocorrem nas suas máquinas. Pode essencialmente girar os dados por ordem de EventosIDs em cada máquina. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| computador1 | [704,701,1501,1500,1085,704,704,701] |
| computador2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` gera uma lista na ordem que os dados foram passados para ele. Para separar eventos do mais antigo ao mais novo, utilize `asc` na declaração de encomenda em vez de `desc` . 

Também é útil criar uma lista de valores apenas distintos. Isto é chamado de _Conjunto_ e pode ser gerado `makeset` com:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computador|list_EventID|
|---|---|
| computador1 | [704,701,1501,1500,1085] |
| computador2 | [326,105,302,301,300,102] |
| ... | ... |

Como `makelist` , também funciona com `makeset` dados encomendados e gerará as matrizes com base na ordem das linhas que são passadas para o mesmo.

## <a name="expanding-lists"></a>Listas de expansão
O funcionamento inverso de `makelist` ou é , que `makeset` `mvexpand` expande uma lista de valores para linhas separadas. Pode expandir-se através de qualquer número de colunas dinâmicas, tanto JSON como matriz. Por exemplo, pode verificar a tabela *Heartbeat* para obter soluções de envio de dados de computadores que enviaram um batimento cardíaco na última hora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| computador1 | "segurança", "atualizações", "changeTracking" |
| computador2 | "segurança", "atualizações" |
| computador3 | "antiMalware", "changeTracking" |
| ... | ... |

Utilize `mvexpand` para mostrar cada valor numa linha separada em vez de uma lista separada por vírgulas:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computador | Soluções | 
|--------------|----------------------|
| computador1 | "segurança" |
| computador1 | "atualizações" |
| computador1 | "changeTracking" |
| computador2 | "segurança" |
| computador2 | "atualizações" |
| computador3 | "antiMalware" |
| computador3 | "changeTracking" |
| ... | ... |


Em seguida, pode `makelist` usar novamente para agrupar itens em conjunto, e desta vez ver a lista de computadores por solução:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Soluções | list_Computer |
|--------------|----------------------|
| "segurança" | ["computador1", "computador2"] |
| "atualizações" | ["computador1", "computador2"] |
| "changeTracking" | ["computador1", "computador3"] |
| "antiMalware" | ["computador3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Manuseamento de caixotes em falta
Uma aplicação útil `mvexpand` é a necessidade de preencher valores predefinidos para os caixotes em falta. Por exemplo, suponha que está à procura do tempo de uma determinada máquina explorando o seu batimento cardíaco. Também quer ver a fonte do batimento cardíaco que está na coluna de _categoria._ Normalmente, usaríamos uma simples declaração resumida da seguinte forma:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Categoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente Direto | 2017-06-06T17:00:00Z | 15 |
| Agente Direto | 2017-06-06T18:00:00Z | 60 |
| Agente Direto | 2017-06-06T20:00:00Z | 55 |
| Agente Direto | 2017-06-06T21:00:00Z | 57 |
| Agente Direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Nestes resultados, embora o balde associado a "2017-06-06T19:00:00Z" esteja em falta porque não há dados do batimento cardíaco para essa hora. Utilize a `make-series` função para atribuir um valor predefinido a baldes vazios. Isto gerará uma linha para cada categoria com duas colunas de matriz extra, uma para valores e outra para baldes de tempo correspondentes:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Categoria | count_ | TimeGenerated |
|---|---|---|
| Agente Direto | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.000000Z","2017-06-06T18:00.0000000Z","2017-06-06T19:00:00.000000Z","2017-06-06T20:00:000000Z","2017-06-06T21:00:00.00000Z",...] |
| ... | ... | ... |

O terceiro elemento da matriz *count_* é um 0 como esperado, e há um timetamp correspondente de "2017-06T19:00:00.0000000Z" na matriz _timeGenerated._ Este formato de matriz é difícil de ler. Utilize `mvexpand` para expandir as matrizes e produzir a mesma saída de formato gerada `summarize` por:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Categoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente Direto | 2017-06-06T17:00:00Z | 15 |
| Agente Direto | 2017-06-06T18:00:00Z | 60 |
| Agente Direto | 2017-06-06T19:00:00Z | 0 |
| Agente Direto | 2017-06-06T20:00:00Z | 55 |
| Agente Direto | 2017-06-06T21:00:00Z | 57 |
| Agente Direto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>A redução dos resultados para um conjunto de elementos: `let` `makeset` , `toscalar` , `in`
Um cenário comum é selecionar os nomes de algumas entidades específicas com base num conjunto de critérios e, em seguida, filtrar um conjunto de dados diferente para esse conjunto de entidades. Por exemplo, pode encontrar computadores que são conhecidos por terem atualizações em falta e identificar iPs a que estes computadores chamaram:


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

Consulte outras lições para utilizar a [linguagem de consulta kusto](/azure/kusto/query/) com dados de registo do Azure Monitor:

- [Operações de cadeia](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas]()
- [Estruturas de dados e JSON](json-data-structures.md)
- [Associações](joins.md)
- [Gráficos](charts.md)

