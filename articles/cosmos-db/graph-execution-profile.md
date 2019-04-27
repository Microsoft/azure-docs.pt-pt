---
title: Avaliar as suas consultas com a função de perfil de execução para a API de Gremlin do Azure Cosmos DB
description: Saiba como resolver problemas e melhorar suas consultas do Gremlin utilizando o passo de perfil de execução.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888417"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Como utilizar o passo de perfil de execução para avaliar as suas consultas do Gremlin

Este artigo fornece uma descrição geral de como utilizar o passo de perfil de execução para bases de dados de gráfico de API do Azure Cosmos DB Gremlin. Este passo fornece as informações relevantes para resolução de problemas e otimizações de consulta, que é compatível com qualquer consulta Gremlin que pode ser executada numa conta de API do Gremlin do Cosmos DB.

Para utilizar neste passo, basta acrescentar o `executionProfile()` chamada no final da sua consulta do Gremlin de função. **A consulta do Gremlin será executada** e o resultado da operação irá devolver um objeto de resposta JSON com o perfil de execução da consulta.

Por exemplo:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Após chamar o `executionProfile()` passo, a resposta será um objeto JSON que inclui o passo de Gremlin executado, o tempo total que demorou e uma matriz dos operadores de tempo de execução do Cosmos DB que a instrução resultou em.

> [!NOTE]
> Essa implementação do perfil de execução não está definida na especificação do Apache Tinkerpop. É específico para a implementação do Azure Cosmos DB Gremlin da API.


## <a name="response-example"></a>Exemplo de resposta

Segue-se um exemplo anotado de saída que vai ser devolvido:

> [!NOTE]
> Neste exemplo está anotado com comentários que explicam a estrutura geral da resposta. Uma resposta de executionProfile real não contêm quaisquer comentários.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> O passo de executionProfile executará a consulta do Gremlin. Isto inclui a `addV` ou `addE`passos, que resultarão na criação e irão consolidar as alterações especificadas na consulta. Como resultado, as unidades de pedido gerado pela consulta Gremlin também são cobradas.

## <a name="execution-profile-response-objects"></a>Objetos de resposta de perfil de execução

A resposta de uma função de executionProfile() resulta numa hierarquia de objetos JSON com a seguinte estrutura:
  - **Objeto de operação do gremlin**: Representa a operação de Gremlin toda que foram executada. Contém as seguintes propriedades.
    - `gremlin`: A instrução de Gremlin explícita que foi executada.
    - `totalTime`: O tempo, em milissegundos, que a execução do passo incorreu num. 
    - `metrics`: Uma matriz que contém cada um dos operadores de tempo de execução do Cosmos DB que foram executados para realizar a consulta. Esta lista é ordenada por ordem de execução.
    
  - **Operadores de tempo de execução do cosmos DB**: Representa cada um dos componentes de toda a operação do Gremlin. Esta lista é ordenada por ordem de execução. Cada objeto contém as seguintes propriedades:
    - `name`: Nome do operador. Este é o tipo de passo que foi avaliado e executado. Leia mais na tabela abaixo.
    - `time`: Período de tempo, em milissegundos, que demorou um operador de determinado.
    - `annotations`: Contém informações adicionais, específicas para o operador que foi executado.
    - `annotations.percentTime`: Porcentagem do tempo total que demorou a executar o operador específico.
    - `counts`: Número de objetos que foram devolvidas da camada de armazenamento por este operador. Isso está contido no `counts.resultCount` valor escalar dentro.
    - `storeOps`: Representa uma operação de armazenamento que pode abranger uma ou várias partições.
    - `storeOps.fanoutFactor`: Representa o número de partições que esta operação de armazenamento específico acessada.
    - `storeOps.count`: Representa o número de resultados que esta operação de armazenamento devolvida.
    - `storeOps.size`: Representa o tamanho em bytes do resultado de uma operação de armazenamento fornecida.

Operador de tempo de execução de Gremlin do cosmos DB|Descrição
---|---
`GetVertices`| Este passo obtém um conjunto predicated de objetos da camada de persistência. 
`GetEdges`| Este passo obtém as margens que são adjacentes a um conjunto de vértices. Este passo pode resultar numa ou muitas operações de armazenamento.
`GetNeighborVertices`| Este passo obtém os vértices ligados a um conjunto de limites. As bordas contêm a partição de chaves e IDs de vértices da sua origem e de destino.
`Coalesce`| Este passo de contas para a avaliação de duas operações sempre que o `coalesce()` passo Gremlin é executado.
`CartesianProductOperator`| Este passo computa um produto Cartesiano entre dois conjuntos de dados. Normalmente, executado sempre que os predicados `to()` ou `from()` são utilizados.
`ConstantSourceOperator`| Este passo computa uma expressão para produzir um valor constante como resultado.
`ProjectOperator`| Este passo prepara e serializa uma resposta com o resultado de operações anteriores.
`ProjectAggregation`| Este passo prepara e serializa uma resposta para uma operação de agregação.

> [!NOTE]
> Esta lista irá continuar a ser atualizada à medida que são adicionados novos operadores.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemplos sobre como analisar uma resposta de perfil de execução

Seguem-se exemplos de otimizações comuns que podem ser identificados com a resposta de perfil de execução:
  - Consulta de fan-out cego.
  - Consulta não filtrada.

### <a name="blind-fan-out-query-patterns"></a>Padrões de consulta do tipo fan-out cego

Suponha a seguinte resposta de perfil de execução de um **graph particionada**:

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

As seguintes conclusões podem ser feitas a partir do mesmo:
- A consulta é uma única pesquisa de ID, uma vez que a instrução de Gremlin segue o padrão `g.V('id')`.
- A julgar pela `time` métrica, a latência desta consulta parece ser alto, uma vez que é [mais de 10 MS para uma única operação de leitura de ponto](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Se olhar para o `storeOps` de objeto, podemos ver que o `fanoutFactor` é `5`, que significa que [5 partições](https://docs.microsoft.com/azure/cosmos-db/partition-data) foram acedidos por esta operação.

Como uma conclusão dessa análise, podemos determinar que a primeira consulta está a aceder a mais partições que o necessário. Isso pode ser resolvido ao especificar a chave de particionamento na consulta como um predicado. Esta ação irá originar a menor latência e menos custo por consulta. Saiba mais sobre [criação de partições de gráfico](graph-partitioning.md). Uma consulta mais ideal seria `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Padrões de consulta não filtrado

Compare as respostas de perfil de duas execução seguintes. Para simplificar, nestes exemplos, utilize um único gráfico particionado.

Esta primeira consulta obtém todos os vértices com a etiqueta `tweet` e, em seguida, obtém seus vértices vizinhos:

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

Tenha em atenção o perfil da mesma consulta, mas agora com um filtro adicional, `has('lang', 'en')`, antes de explorar os vértices adjacentes:

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

Essas duas consultas atingiu o mesmo resultado, no entanto, o primeiro será necessário mais unidades de pedido, uma vez que ela necessária para fazer a iteração de um maior conjunto de dados inicial antes de consultar os itens adjacentes. Podemos ver indicadores desse comportamento ao comparar os seguintes parâmetros de ambas as respostas:
- O `metrics[0].time` valor é maior na primeira resposta, que indica que este passo único demorou mais tempo a resolver.
- O `metrics[0].counts.resultsCount` valor é superior também na primeira resposta, que indica que o conjunto de dados do trabalho inicial era maior.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [Gremlin funcionalidades suportadas](gremlin-support.md) no Azure Cosmos DB. 
* Saiba mais sobre o [API do Gremlin no Azure Cosmos DB](graph-introduction.md).
