---
title: Agregações avançadas em consultas de registo do Monitor Do Azure Microsoft Docs
description: Descreve algumas das opções de agregação mais avançadas disponíveis para consultas de log Do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662183"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agregações avançadas em consultas de registo do Monitor Azure

> [!NOTE]
> Deve completar [as agregações em consultas do Monitor Azure](./aggregations.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve algumas das opções de agregação mais avançadas disponíveis para consultas do Monitor Azure.

## <a name="generating-lists-and-sets"></a>Gerando listas e conjuntos
Pode utilizar `makelist` para girar dados pela ordem dos valores numa determinada coluna. Por exemplo, você pode querer explorar os eventos de ordem mais comuns ocorrem nas suas máquinas. Pode essencialmente girar os dados pela ordem dos EventIDs em cada máquina. 

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

`makelist` gera uma lista na ordem em que os dados foram transmitidos nele. Para separar eventos dos mais antigos aos mais recentes, use `asc` na declaração de encomenda em vez de `desc`. 

Também é útil criar uma lista de valores apenas distintos. Isto é chamado de _Conjunto_ e pode ser gerado com `makeset`:

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

Tal como `makelist`, `makeset` também trabalha com dados encomendados e gerará as matrizes com base na ordem das linhas que são passadas para lá.

## <a name="expanding-lists"></a>Listas de expansão
O funcionamento inverso de `makelist` ou `makeset` é `mvexpand`, que expande uma lista de valores para linhas separadas. Pode expandir-se através de várias colunas dinâmicas, tanto JSON como matriz. Por exemplo, pode verificar a tabela *Heartbeat* para obter soluções que enviem dados de computadores que enviaram um batimento cardíaco na última hora:

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

Utilize `mvexpand` para mostrar cada valor numa linha separada em vez de uma lista separada de vírem:

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
| computador1 | "ChangeTracking" |
| computador2 | "segurança" |
| computador2 | "atualizações" |
| computador3 | "antiMalware" |
| computador3 | "ChangeTracking" |
| ... | ... |


Em seguida, poderia voltar a usá`makelist` para agrupar itens, e desta vez ver a lista de computadores por solução:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Soluções | list_Computer |
|--------------|----------------------|
| "segurança" | ["computer1", "computer2"] |
| "atualizações" | ["computer1", "computer2"] |
| "ChangeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Manuseamento de caixotes em falta
Uma aplicação útil de `mvexpand` é a necessidade de preencher valores predefinidos para os caixotes em falta. Por exemplo, suponha que está à procura do tempo de adoção de uma determinada máquina explorando o seu batimento cardíaco. Também quer ver a fonte do batimento cardíaco que está na coluna da _categoria._ Normalmente, usaríamos uma simples declaração resumindo da seguinte forma:

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

Nestes resultados, embora o balde associado ao "2017-06-06T19:00:00Z" esteja desaparecido porque não há dados de batimentos cardíacos para aquela hora. Utilize a função `make-series` para atribuir um valor predefinido a baldes vazios. Isto gerará uma linha para cada categoria com duas colunas extra, uma para valores, e outra para baldes de tempo correspondentes:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Categoria | count_ | TimeGenerated |
|---|---|---|
| Agente Direto | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

O terceiro elemento da matriz *count_* é um 0 como esperado, e há um carimbo de tempo correspondente de "2017-06-06T19:00:00.00000000Z" na matriz _TimeGenerated._ Este formato de matriz é difícil de ler. Utilize `mvexpand` para expandir as matrizes e produzir a mesma saída de formato gerada por `summarize`:

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



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Reduzir os resultados a um conjunto de elementos: `let`, `makeset`, `toscalar`, `in`
Um cenário comum é selecionar os nomes de algumas entidades específicas com base num conjunto de critérios e, em seguida, filtrar um conjunto de dados diferentes para esse conjunto de entidades. Por exemplo, pode encontrar computadores que se sabe que têm falta de atualizações e identificar IPs a que estes computadores chamaram:


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

Consulte outras lições para utilizar a linguagem de [consulta Kusto](/azure/kusto/query/) com dados de registo do Monitor Azure:

- [Operações de cordas](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junta-se](joins.md)
- [Gráficos](charts.md)
