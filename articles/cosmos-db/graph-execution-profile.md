---
title: Use o perfil de execução para avaliar consultas em Azure Cosmos DB Gremlin API
description: Aprenda a resolver problemas e a melhorar as suas consultas gremlin usando o passo do perfil de execução.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087490"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Como utilizar o passo do perfil de execução para avaliar as consultas do Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo fornece uma descrição geral de como utilizar o passo do perfil de execução para bases de dados de gráficos da API do Gremlin no Azure Cosmos DB. Este passo fornece informações relevantes para a resolução de problemas e otimizações de consulta, e é compatível com qualquer consulta do Gremlin que possa ser executada relativamente a uma conta da API do Gremlin no Cosmos DB.

Para utilizar este passo, basta anexar a `executionProfile()` chamada de função no final da sua consulta Gremlin. **A sua consulta gremlin será executada** e o resultado da operação devolverá um objeto de resposta JSON com o perfil de execução de consulta.

Por exemplo:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Depois de chamar o `executionProfile()` passo, a resposta será um objeto JSON que inclui o passo gremlin executado, o tempo total que demorou, e uma variedade de operadores de tempo de execução cosmos DB que a declaração resultou.

> [!NOTE]
> Esta implementação para o Perfil de Execução não está definida na especificação Apache Tinkerpop. É específico da implementação da Azure Cosmos DB Gremlin API.


## <a name="response-example"></a>Exemplo de resposta

Segue-se um exemplo anotado da saída que será devolvida:

> [!NOTE]
> Este exemplo é anotado com comentários que explicam a estrutura geral da resposta. Uma resposta real doProfile de execução não conterá comentários.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> O passo de execução do Profe executará a consulta gremlin. Isto inclui os `addV` `addE` ou passos, que resultarão na criação e cometerá as alterações especificadas na consulta. Como resultado, as Unidades de Pedido geradas pela consulta Gremlin também serão cobradas.

## <a name="execution-profile-response-objects"></a>Objetos de resposta de perfil de execução

A resposta de uma função de execuçãoProfile() produzirá uma hierarquia de objetos JSON com a seguinte estrutura:
  - **Objeto de operação Gremlin**: Representa toda a operação Gremlin que foi executada. Contém as seguintes propriedades.
    - `gremlin`: A declaração explícita de Gremlin que foi executada.
    - `totalTime`: O tempo, em milissegundos, em que a execução do passo incorreu. 
    - `metrics`: Uma matriz que contém cada um dos operadores de tempo de execução da Cosmos DB que foram executados para cumprir a consulta. Esta lista está ordenada por ordem de execução.
    
  - **Operadores de tempo de execução Cosmos DB**: Representa cada um dos componentes de toda a operação Gremlin. Esta lista está ordenada por ordem de execução. Cada objeto contém as seguintes propriedades:
    - `name`: Nome do operador. Este é o tipo de passo que foi avaliado e executado. Leia mais na tabela abaixo.
    - `time`: Quantidade de tempo, em milissegundos, que um determinado operador demorou.
    - `annotations`: Contém informações adicionais, específicas do operador que foi executado.
    - `annotations.percentTime`: Percentagem do tempo total que demorou a executar o operador específico.
    - `counts`: Número de objetos que foram devolvidos da camada de armazenamento por este operador. Isto está contido no `counts.resultCount` valor escalar dentro.
    - `storeOps`: Representa uma operação de armazenamento que pode abranger uma ou múltiplas divisórias.
    - `storeOps.fanoutFactor`: Representa o número de divisórias a que esta operação de armazenamento específica acedeu.
    - `storeOps.count`: Representa o número de resultados que esta operação de armazenamento devolveu.
    - `storeOps.size`: Representa o tamanho dos bytes do resultado de uma determinada operação de armazenamento.

Operador de tempo de execução Cosmos DB Gremlin|Description
---|---
`GetVertices`| Este passo obtém um conjunto de objetos pré-indicados da camada de persistência. 
`GetEdges`| Este passo obtém as bordas adjacentes a um conjunto de vértices. Este passo pode resultar em uma ou muitas operações de armazenamento.
`GetNeighborVertices`| Este passo obtém os vértices que estão ligados a um conjunto de bordas. As bordas contêm as chaves de partição e as identificações da sua origem e vértices-alvo.
`Coalesce`| Este passo explica a avaliação de duas operações sempre que o `coalesce()` passo Gremlin é executado.
`CartesianProductOperator`| Este passo calcula um produto cartesiano entre dois conjuntos de dados. Normalmente executado sempre que os predicados `to()` ou `from()` são usados.
`ConstantSourceOperator`| Este passo computa uma expressão para produzir um valor constante como resultado.
`ProjectOperator`| Este passo prepara e serializa uma resposta utilizando o resultado das operações anteriores.
`ProjectAggregation`| Este passo prepara e serializa uma resposta para uma operação agregada.

> [!NOTE]
> Esta lista continuará a ser atualizada à medida que forem adicionados novos operadores.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemplos sobre como analisar uma resposta de perfil de execução

Seguem-se exemplos de otimizações comuns que podem ser detetadas utilizando a resposta do Perfil de Execução:
  - Consulta cega de fan-out.
  - Consulta não filtrada.

### <a name="blind-fan-out-query-patterns"></a>Padrões cegos de consulta de fan-out

Assuma a seguinte resposta do perfil de execução a partir de um **gráfico dividido:**

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

A partir daí podem ser feitas as seguintes conclusões:
- A consulta é uma única procura de identificação, uma vez que a declaração de Gremlin segue o `g.V('id')` padrão.
- A julgar pela `time` métrica, a latência desta consulta parece ser alta, uma vez que é [mais de 10ms para uma única operação de leitura de pontos.](./introduction.md#guaranteed-speed-at-any-scale)
- Se olharmos para o `storeOps` objeto, podemos ver que é , o `fanoutFactor` que significa que `5` [5 divisórias](./partitioning-overview.md) foram acedidas por esta operação.

Como conclusão desta análise, podemos determinar que a primeira consulta é aceder a mais divisórias do que o necessário. Isto pode ser abordado especificando a chave de partição na consulta como predicado. Isto conduzirá a menos latência e menos custo por consulta. Saiba mais sobre [a partição de gráficos.](graph-partitioning.md) Uma consulta mais ideal `g.V('tt0093640').has('partitionKey', 't1001')` seria.

### <a name="unfiltered-query-patterns"></a>Padrões de consulta não filtrados

Compare as duas respostas seguintes do perfil de execução. Para simplificar, estes exemplos usam um único gráfico dividido.

Esta primeira consulta recupera todos os vértices com a etiqueta `tweet` e, em seguida, obtém os seus vértices vizinhos:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Note o perfil da mesma consulta, mas agora com um filtro adicional, `has('lang', 'en')` antes de explorar os vértices adjacentes:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Estas duas consultas atingiram o mesmo resultado, no entanto, a primeira exigirá mais Unidades de Pedido, uma vez que necessitava de iterar um conjunto de dados inicial maior antes de consultar os itens adjacentes. Podemos ver indicadores deste comportamento ao comparar os seguintes parâmetros de ambas as respostas:
- O `metrics[0].time` valor é maior na primeira resposta, o que indica que este único passo demorou mais tempo a ser resolvido.
- O `metrics[0].counts.resultsCount` valor é mais elevado também na primeira resposta, o que indica que o conjunto de dados de trabalho inicial foi maior.

## <a name="next-steps"></a>Passos seguintes
* Conheça as [funcionalidades suportadas da Gremlin](gremlin-support.md) no Azure Cosmos DB. 
* Saiba mais sobre a [API de Gremlin em Azure Cosmos DB](graph-introduction.md).