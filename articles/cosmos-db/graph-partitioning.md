---
title: Partição de dados na API Azure Cosmos DB Gremlin
description: Saiba como pode usar um gráfico dividido em Azure Cosmos DB. Este artigo também descreve os requisitos e as melhores práticas para um gráfico dividido.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623378"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usar um grafo de particionado no Azure Cosmos DB

Uma das principais características da API Gremlin em Azure Cosmos DB é a capacidade de lidar com gráficos de grande escala através da escala horizontal. Os recipientes podem escalar de forma independente em termos de armazenamento e de entrada. Pode criar recipientes em Azure Cosmos DB que podem ser automaticamente dimensionados para armazenar dados de gráficos. Os dados são automaticamente equilibrados com base na tecla de **partição**especificada .

**A partilha é necessária** se se espera que o recipiente guarde mais de 20 GB de tamanho ou se quiser alocar mais de 10.000 unidades de pedido por segundo (RUs). Os mesmos princípios gerais do mecanismo de [partição Do Azure Cosmos DB](partition-data.md) aplicam-se com algumas otimizações específicas do gráfico descritas abaixo.

![Partição de gráficos.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mecanismo de partição de gráficos

As seguintes diretrizes descrevem como funciona a estratégia de partição no Azure Cosmos DB:

- **Tanto os vértices como as bordas são armazenados como documentos JSON**.

- **Os vértices requerem uma chave de partição.** Esta chave determinará em que divisória o vértice será armazenado através de um algoritmo de hashing. O nome da propriedade chave de divisória é `/partitioning-key-name`definido na criação de um novo recipiente e tem um formato: .

- **As bordas serão armazenadas com o seu vértice**de origem. Por outras palavras, para cada vértice, a sua chave de partição define onde são armazenadas juntamente com as suas bordas de saída. Esta otimização é feita para evitar consultas `out()` de divisória cruzada quando se utiliza a cardinalidade em consultas de gráfico.

- **As bordas contêm referências aos vértices a que apontam**. Todas as bordas são armazenadas com as chaves de partição e iDs dos vértices que estão apontando. Esta computação `out()` faz com que todas as consultas de direção sejam sempre uma consulta dividida e não uma consulta de divisória cruzada cega. 

- **As consultas de gráfico supor uma tecla de partição**. Para tirar o máximo partido da divisória horizontal em Azure Cosmos DB, a chave de partição deve ser especificada quando um único vértice é selecionado, sempre que possível. Seguem-se consultas para selecionar um ou vários vértices num gráfico dividido:

    - `/id`e `/label` não são suportadas como chaves de partição para um recipiente na API de Gremlin.


    - Selecionando um vértice por ID, **utilizando o `.has()` passo para especificar a propriedade da chave de partição:** 
    
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

## <a name="best-practices-when-using-a-partitioned-graph"></a>Boas práticas ao usar um gráfico dividido

Utilize as seguintes diretrizes para garantir o desempenho e a escalabilidade ao utilizar gráficos divididos com recipientes ilimitados:

- **Especifique sempre o valor da chave da divisória ao consultar um vértice**. Obter vértice de uma divisória conhecida é uma forma de alcançar o desempenho. Todas as operações subsequentes de adjacência serão sempre direcionadas para uma partição, uma vez que as Bordas contêm identificação de referência e chave de partição dos seus vértices-alvo.

- **Utilize a direção de saída quando consultar as bordas sempre que possível**. Como mencionado acima, as bordas são armazenadas com os seus vértices de origem na direção de saída. Assim, as chances de recorrer a consultas de partição cruzada são minimizadas quando os dados e consultas são projetados com este padrão em mente. Pelo contrário, a `in()` quea será sempre uma pergunta cara.

- Escolha uma chave de **partição que distribua uniformemente os dados através das divisórias**. Esta decisão depende fortemente do modelo de dados da solução. Leia mais sobre a criação de uma chave de partição apropriada em [Partição e escala em Azure Cosmos DB](partition-data.md).

- **Otimize as consultas para obter dados dentro dos limites de uma partição**. Uma estratégia de partição ideal estaria alinhada com os padrões de consulta. As consultas que obtêm dados de uma única divisória proporcionam o melhor desempenho possível.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder à leitura dos seguintes artigos:

* Saiba mais sobre [partição e escala em Azure Cosmos DB.](partition-data.md)
* Conheça o [suporte gremlin na API Gremlin.](gremlin-support.md)
* Saiba mais sobre [introdução à API Gremlin.](graph-introduction.md)
