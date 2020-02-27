---
title: Criação de partições de dados na API do Azure Cosmos DB Gremlin
description: Saiba como pode utilizar um gráfico particionado no Azure Cosmos DB. Este artigo também descreve os requisitos e melhores práticas para um gráfico particionada.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623378"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usando um gráfico de particionadas no Azure Cosmos DB

Um dos principais recursos da API do Gremlin no Azure Cosmos DB é a capacidade de processar gráficos em grande escala através do dimensionamento horizontal. Os contentores podem dimensionar de forma independente em termos de armazenamento e débito. Pode criar contentores no Azure Cosmos DB, que pode ser dimensionado automaticamente para armazenar dados de gráficos. Os dados são automaticamente equilibrados com base na tecla de **partição**especificada .

**A partilha é necessária** se se espera que o recipiente guarde mais de 20 GB de tamanho ou se quiser alocar mais de 10.000 unidades de pedido por segundo (RUs). Os mesmos princípios gerais do mecanismo de [partição Do Azure Cosmos DB](partition-data.md) aplicam-se com algumas otimizações específicas do gráfico descritas abaixo.

![Partição de gráficos.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mecanismo de partição de gráficos

As seguintes diretrizes descrevem como funciona a estratégia de partição no Azure Cosmos DB:

- **Tanto os vértices como as bordas são armazenados como documentos JSON**.

- **Os vértices requerem uma chave de partição.** Esta chave determinará qual partição vértice será armazenado por meio de um algoritmo de hash. O nome da propriedade chave de divisória é definido na criação de um novo recipiente e tem um formato: `/partitioning-key-name`.

- **As bordas serão armazenadas com o seu vértice**de origem. Em outras palavras, para cada vértice a respetiva chave de partição define onde estão armazenados juntamente com bordas de saída. Esta otimização é feita para evitar consultas de divisória cruzada quando se utiliza a `out()` cardinalidade em consultas de gráfico.

- **As bordas contêm referências aos vértices a que apontam**. Todas as bordas são armazenadas com as chaves de partição e iDs dos vértices que estão apontando. Esta computação faz com que todas as consultas de direção `out()` sejam sempre uma consulta dividida e não uma consulta de divisória cruzada cega. 

- **As consultas de gráfico supor uma tecla de partição**. Para aproveitar ao máximo a criação de partições horizontais no Azure Cosmos DB, a chave de partição deve ser especificada quando é selecionado um único vértice, sempre que possível. Seguem-se as consultas para a seleção de vértices de um ou vários num gráfico particionado:

    - `/id` e `/label` não são suportados como chaves de partição para um contentor na API de Gremlin.


    - Selecionando um vértice por ID, **utilizando o `.has()` passo para especificar a propriedade da chave de divisória:** 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selecionando um vértice **especificando uma tuple, incluindo o valor da chave da divisória e id:** 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Especificando uma **série de tupículos de valores-chave de partição e iDs:**
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selecionando um conjunto de vértices com as suas IDs e **especificando uma lista de valores-chave de partição:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Utilizando a estratégia de **partição** no início de uma consulta e especificando uma partição para o âmbito do resto da consulta gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Melhores práticas quando utilizar um gráfico particionado

Utilize as seguintes diretrizes para garantir o desempenho e escalabilidade ao utilizar gráficos particionados com os contentores ilimitados:

- **Especifique sempre o valor da chave da divisória ao consultar um vértice**. Obter o vértice de uma partição conhecida é uma forma de obter um desempenho. Todas as operações subsequentes de adjacência serão sempre direcionadas para uma partição, uma vez que as Bordas contêm identificação de referência e chave de partição dos seus vértices-alvo.

- **Utilize a direção de saída quando consultar as bordas sempre que possível**. Conforme mencionado acima, os limites são armazenados com seus vértices de origem na direção de saída. Portanto, as chances de recorrer a consultas entre partições são minimizadas quando os dados e as consultas são projetadas com esse padrão em mente. Pelo contrário, a `in()` query será sempre uma pergunta cara.

- Escolha uma chave de **partição que distribua uniformemente os dados através das divisórias**. Essa decisão depende intensamente o modelo de dados da solução. Leia mais sobre a criação de uma chave de partição apropriada em [Partição e escala em Azure Cosmos DB](partition-data.md).

- **Otimize as consultas para obter dados dentro dos limites de uma partição**. Uma estratégia de particionamento ideal seria alinhada para os padrões de consultas. Consultas que obtêm dados de uma única partição fornecem o melhor desempenho possível.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode avançar para ler os artigos seguintes:

* Saiba mais sobre [partição e escala em Azure Cosmos DB.](partition-data.md)
* Conheça o [suporte gremlin na API Gremlin.](gremlin-support.md)
* Saiba mais sobre [introdução à API Gremlin.](graph-introduction.md)
