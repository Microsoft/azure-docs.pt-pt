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
ms.openlocfilehash: 4c8761d82c8a735ac9c4bff2e5ac0107b2a57fe0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537538"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Usando um gráfico de particionadas no Azure Cosmos DB

Um dos principais recursos da API do Gremlin no Azure Cosmos DB é a capacidade de processar gráficos em grande escala através do dimensionamento horizontal. Os contentores podem dimensionar de forma independente em termos de armazenamento e débito. Pode criar contentores no Azure Cosmos DB, que pode ser dimensionado automaticamente para armazenar dados de gráficos. Os dados é automaticamente balanceados com base no especificado **chave de partição**.

**É necessário criar partições** se se esperar que o contentor para armazenar mais de 10 GB de tamanho ou se pretender atribuir mais de 10.000 unidades de pedido por segundo (RUs). Os mesmos princípios gerais do [mecanismo de criação de partições do Azure Cosmos DB](partition-data.md) aplicam-se com algumas otimizações de gráfico específicos descritas abaixo.

![Criação de partições de gráfico.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mecanismo de criação de partições de gráfico

As diretrizes seguintes descrevem como funciona a estratégia de particionamento no Azure Cosmos DB:

- **Vértices e margens são armazenadas como documentos JSON**.

- **Vértices requerem uma chave de partição**. Esta chave determinará qual partição vértice será armazenado por meio de um algoritmo de hash. O nome da propriedade da chave de partição é definido durante a criação de um contentor novo e tem um formato: `/partitioning-key-name`.

- **Margens serão armazenadas com seu vértice de origem**. Em outras palavras, para cada vértice a respetiva chave de partição define onde estão armazenados juntamente com bordas de saída. Essa otimização é feita para evitar consultas entre partições quando utiliza o `out()` cardinalidade em consultas de gráficos.

- **Margens contêm referências para os vértices apontam para**. Todas as margens são armazenadas com as chaves de partição e as identificações dos vértices que apontam para. Este cálculo faz com que todos os `out()` consultas de direção ser sempre uma consulta particionada âmbito e não uma consulta entre partições cego. 

- **Consultas de gráficos tem de especificar uma chave de partição**. Para aproveitar ao máximo a criação de partições horizontais no Azure Cosmos DB, a chave de partição deve ser especificada quando é selecionado um único vértice, sempre que possível. Seguem-se as consultas para a seleção de vértices de um ou vários num gráfico particionado:

    - `/id` e `/label` não são suportados como chaves de partição para um contentor na API do Gremlin.


    - Selecionar um vértice por ID, em seguida, **utilizando o `.has()` passo para especificar a propriedade da chave de partição**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selecionar um vértice pela **especificando uma tupla, incluindo o valor da chave de partição e ID**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Especificar um **matriz de cadeias de identificação de valores de chave de partição e IDs**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selecionar um conjunto de vértices com suas IDs e **especificando uma lista de valores de chave de partição**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Utilizar o **estratégia de partição** no início de uma consulta e especificar uma partição para o âmbito do resto da consulta Gremlin: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Melhores práticas quando utilizar um gráfico particionado

Utilize as seguintes diretrizes para garantir o desempenho e escalabilidade ao utilizar gráficos particionados com os contentores ilimitados:

- **Sempre especificar o valor da chave de partição ao consultar um vértice**. Obter o vértice de uma partição conhecida é uma forma de obter um desempenho. Todas as operações subsequentes adjacency sempre serão confinadas a uma partição, uma vez margens contém a chave de partição e ID de referência para seus vértices de destino.

- **Utilizar a direção de saída ao consultar as margens, sempre que possível**. Conforme mencionado acima, os limites são armazenados com seus vértices de origem na direção de saída. Portanto, as chances de recorrer a consultas entre partições são minimizadas quando os dados e as consultas são projetadas com esse padrão em mente. Por outro lado, o `in()` consulta será sempre uma consulta de fan-out Caro.

- **Escolha uma chave de partição que irá distribuir uniformemente os dados em partições**. Essa decisão depende intensamente o modelo de dados da solução. Saiba mais sobre criar uma chave de partição adequado no [criação de partições e dimensionamento no Azure Cosmos DB](partition-data.md).

- **Otimizar as consultas para obter dados dentro dos limites de uma partição**. Uma estratégia de particionamento ideal seria alinhada para os padrões de consultas. Consultas que obtêm dados de uma única partição fornecem o melhor desempenho possível.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para ler os artigos seguintes:

* Saiba mais sobre [particionar e dimensionar no Azure Cosmos DB](partition-data.md).
* Saiba mais sobre o [suporte para Gremlin na API do Gremlin](gremlin-support.md).
* Saiba mais sobre [introdução à API do Gremlin](graph-introduction.md).
