---
title: Partição de dados em Azure Cosmos DB Gremlin API
description: Saiba como pode usar um gráfico dividido em Azure Cosmos DB. Este artigo também descreve os requisitos e as melhores práticas para um gráfico dividido.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 78c15da1ea9fe5f6307ce388e4d64d372e9eb8c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261771"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usar um grafo de particionado no Azure Cosmos DB

Uma das principais características da API gremlin em Azure Cosmos DB é a capacidade de lidar com gráficos de grande escala através da escala horizontal. Os recipientes podem escalar de forma independente em termos de armazenamento e produção. Pode criar recipientes em Azure Cosmos DB que podem ser automaticamente dimensionado para armazenar dados de gráficos. Os dados são automaticamente equilibrados com base na chave de **partição**especificada .

**É necessária a partilha** se se prevê que o recipiente armazene mais de 20 GB de tamanho ou se pretender alocar mais de 10.000 unidades de pedido por segundo (RUs). Os mesmos princípios gerais do mecanismo de [partição DB Azure Cosmos](partition-data.md) aplicam-se com algumas otimizações específicas do gráfico descritas abaixo.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Partição de gráficos." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mecanismo de partição de gráficos

As seguintes diretrizes descrevem como funciona a estratégia de partição no Azure Cosmos DB:

- **Tanto os vértices como as bordas são armazenados como documentos JSON**.

- **Os vértices requerem uma chave de partição.** Esta chave determinará em que partição o vértice será armazenado através de um algoritmo de hashing. O nome da propriedade chave da partição é definido ao criar um novo recipiente e tem um formato: `/partitioning-key-name` .

- **As bordas serão armazenadas com o seu vértice de origem**. Por outras palavras, para cada vértice, a sua chave de partição define onde são armazenadas juntamente com as suas bordas de saída. Esta otimização é feita para evitar consultas de partição cruzadas ao usar a `out()` cardinalidade em consultas de gráficos.

- **As bordas contêm referências aos vértices a que apontam**. Todas as bordas são armazenadas com as chaves de partição e os IDs dos vértices que estão a apontar. Este cálculo faz com que todas as `out()` consultas de direção sejam sempre uma consulta de partição âmbito, e não uma consulta de divisórias cruzadas cegas. 

- **As consultas de gráficos precisam de especificar uma chave de partição**. Para tirar o máximo partido da divisória horizontal em Azure Cosmos DB, a chave de partição deve ser especificada quando um único vértice é selecionado, sempre que possível. Seguem-se as consultas para a seleção de um ou vários vértices num gráfico dividido:

    - `/id`e `/label` não são suportadas como chaves de partição para um recipiente na API de Gremlin.


    - Selecionando um vértice por ID e, em seguida, **usando o `.has()` passo para especificar a propriedade chave de partição**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selecionando um vértice **especificando um tuple incluindo o valor da chave de partição e o ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Especificando uma **variedade de tuples de valores-chave de partição e IDs:**
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selecionando um conjunto de vértices com os seus IDs e **especificando uma lista de valores-chave de partição:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Utilizando a estratégia de **partição** no início de uma consulta e especificando uma partição para o âmbito do resto da consulta gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Melhores práticas ao usar um gráfico dividido

Utilize as seguintes orientações para garantir o desempenho e a escalabilidade ao utilizar gráficos divididos com recipientes ilimitados:

- **Especifique sempre o valor da chave de partição ao consultar um vértice**. Obter vértice de uma partição conhecida é uma forma de alcançar o desempenho. Todas as operações subsequentes de adjacência serão sempre miradas para uma partição, uma vez que edges contêm iD de referência e chave de partição para os seus vértices-alvo.

- **Utilize a direção de saída quando consultar as bordas sempre que possível**. Como mencionado acima, as bordas são armazenadas com os seus vértices de origem na direção de saída. Assim, as chances de recorrer a consultas de partição cruzada são minimizadas quando os dados e consultas são projetados com este padrão em mente. Pelo contrário, a `in()` consulta será sempre uma consulta dispendiosa.

- **Escolha uma chave de partição que distribuirá uniformemente os dados através de divisórias**. Esta decisão depende fortemente do modelo de dados da solução. Leia mais sobre a criação de uma chave de partição adequada em [Partition e escala em Azure Cosmos DB](partition-data.md).

- **Otimize as consultas para obter dados dentro dos limites de uma partição.** Uma estratégia de partição ideal seria alinhada com os padrões de consulta. As consultas que obtêm dados de uma única partição proporcionam o melhor desempenho possível.

## <a name="next-steps"></a>Próximos passos

Em seguida, pode proceder à leitura dos seguintes artigos:

* Saiba mais sobre [partição e escala em Azure Cosmos DB](partition-data.md).
* Conheça o [apoio da Gremlin na API de Gremlin.](gremlin-support.md)
* Saiba mais sobre [a introdução à API de Gremlin.](graph-introduction.md)
