---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Compreender o mapeamento de nível de consistência entre diferentes APIs em Azure Cosmos DB e Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: bb8a413f2e2a3aa4a8facd533d822312bb61fa0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613565"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

A Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolos de arame para bases de dados populares. Estes incluem MongoDB, Apache Cassandra, Gremlin e Azure Table armazenamento. Estas bases de dados não oferecem modelos de consistência definidos com precisão ou garantias apoiadas pelo SLA para os níveis de consistência. Normalmente fornecem apenas um subconjunto dos cinco modelos de consistência oferecidos pela Azure Cosmos DB.

Ao utilizar a API SQL, Gremlin API e API de tabela, é utilizado o nível de consistência padrão configurado na conta Azure Cosmos. 

Ao utilizar a API de Cassandra ou a API da Azure Cosmos para a MongoDB, as aplicações obtêm um conjunto completo de níveis de consistência oferecidos pela Apache Cassandra e pela MongoDB, respectivamente, com garantias de consistência e durabilidade ainda mais fortes. Este documento mostra os níveis de consistência DB correspondentes do Azure Cosmos para os níveis de consistência de Apache Cassandra e MongoDB.

> [!NOTE]
> O modelo de consistência padrão para Azure Cosmos DB é Session. Session é um modelo de consistência centrado no cliente que não é suportado de forma nativa por Cassandra ou MongoDB. Para mais informações sobre qual modelo de consistência escolher ver, [níveis de consistência em Azure Cosmos DB](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapeamento entre apache Cassandra e Azure Cosmos DB níveis de consistência

Ao contrário de Azure Cosmos DB, Apache Cassandra não fornece garantias de consistência definidas de forma precisa.  Em vez disso, a Apache Cassandra proporciona um nível de consistência de escrita e um nível de consistência de leitura, para permitir a alta disponibilidade, consistência e compensações de latência. Ao utilizar a API Cassandra da Azure Cosmos DB: 

* O nível de consistência da escrita de Apache Cassandra está mapeado para o nível de consistência padrão configurado na sua conta Azure Cosmos. A consistência para uma operação de escrita (CL) não pode ser alterada por pedido.

* A Azure Cosmos DB irá mapear dinamicamente o nível de consistência de leitura especificado pelo condutor do cliente Cassandra para um dos níveis de consistência DB do Azure Cosmos configurados dinamicamente num pedido de leitura. 

A tabela a seguir ilustra como os níveis de consistência nativas de Cassandra são mapeados para os níveis de consistência do Azure Cosmos DB ao utilizar a API de Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapeamento do modelo de consistência Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência definidas com precisão. Em vez disso, a MongoDB nativa permite que os utilizadores confivelem as seguintes garantias de consistência: uma preocupação de escrita, uma preocupação de leitura, e a diretiva isMaster - para direcionar as operações de leitura para réplicas primárias ou secundárias para atingir o nível de consistência desejado.

Ao utilizar a API da Azure Cosmos DB para o MongoDB, o condutor do MongoDB trata a sua região de escrita como a réplica primária e todas as outras regiões são lidas réplicas. Pode escolher qual região associada à sua conta Azure Cosmos como réplica primária. 

Ao usar a API da Azure Cosmos DB para a MongoDB:

* A preocupação com a escrita está mapeada para o nível de consistência padrão configurado na sua conta Azure Cosmos.

* A Azure Cosmos DB irá mapear dinamicamente a preocupação de leitura especificada pelo controlador cliente MongoDB para um dos níveis de consistência do Azure Cosmos DB que é configurado dinamicamente num pedido de leitura.  

* Você pode anotar uma região específica associada à sua conta Azure Cosmos como "Primária" tornando a região como a primeira região writable. 

A tabela a seguir ilustra como as preocupações nativas de escrita/leitura da MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao utilizar a API da Azure Cosmos para a MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapeamento do modelo de consistência Cassandra" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os níveis de consistência e compatibilidade entre Azure Cosmos DB APIs com as APIs de código aberto. Consulte os seguintes artigos:

* [Disponibilidade e compensações de desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB apoiados pela API da Azure Cosmos DB para a MongoDB](mongodb-feature-support.md)
* [Apache Cassandra apresenta-se com o apoio da Azure Cosmos DB Cassandra API](cassandra-support.md)
