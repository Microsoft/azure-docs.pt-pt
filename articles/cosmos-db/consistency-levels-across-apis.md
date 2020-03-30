---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Compreender o nível de consistência de mapeamento entre diferentes APIs em Azure Cosmos DB e Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131462"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

A Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolos de arame para bases de dados populares. Estes incluem mongoDB, Apache Cassandra, Gremlin e armazenamento de mesa azure. Estas bases de dados não oferecem modelos de consistência definidos com precisão ou garantias apoiadas por SLA para os níveis de consistência. Normalmente fornecem apenas um subconjunto dos cinco modelos de consistência oferecidos pela Azure Cosmos DB. 

Ao utilizar a API SQL, API Gremlin e API de tabela, é utilizado o nível de consistência predefinido configurado na conta Azure Cosmos. 

Ao utilizar a API de Cassandra API ou Azure Cosmos DB para MongoDB, as aplicações obtêm um conjunto completo de níveis de consistência oferecidos por Apache Cassandra e MongoDB, respectivamente, com garantias de consistência e durabilidade ainda mais fortes. Este documento mostra os correspondentes níveis de consistência do Azure Cosmos DB para os níveis de consistência de Apache Cassandra e MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapeamento entre apache Cassandra e níveis de consistência dB da Azure Cosmos

Ao contrário do Azure Cosmos DB, a Apache Cassandra não fornece, de forma nativa, garantias de consistência precisamente definidas.  Em vez disso, a Apache Cassandra fornece um nível de consistência de escrita e um nível de consistência de leitura, para permitir a elevada disponibilidade, consistência e trocas de latência. Ao utilizar a API Cassandra da Azure Cosmos DB: 

* O nível de consistência da escrita de Apache Cassandra está mapeado para o nível de consistência padrão configurado na sua conta Azure Cosmos. A consistência para uma operação de escrita (CL) não pode ser alterada por pedido.

* A Azure Cosmos DB irá mapear dinamicamente o nível de consistência da leitura especificado pelo condutor do cliente Cassandra a um dos níveis de consistência do Azure Cosmos DB configurados dinamicamente a pedido de leitura. 

A tabela que se segue ilustra como os níveis de consistência da Cassandra nativa são mapeados para os níveis de consistência do Azure Cosmos DB ao utilizar a Cassandra API:  

[![Mapeamento do modelo de consistência cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapeamento entre níveis de consistência DB MongoDB e Azure Cosmos

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência precisamente definidas. Em vez disso, o MongoDB nativo permite que os utilizadores configurem as seguintes garantias de consistência: uma preocupação de escrita, uma preocupação de leitura, e a diretiva isMaster - para direcionar as operações de leitura para réplicas primárias ou secundárias para alcançar o nível de consistência desejado. 

Ao utilizar a API da Azure Cosmos DB para o MongoDB, o condutor do MongoDB trata a sua região de escrita como a réplica primária e todas as outras regiões são lidas réplicas. Pode escolher qual a região associada à sua conta Azure Cosmos como uma réplica primária. 

Ao utilizar a API da Azure Cosmos DB para o MongoDB:

* A preocupação com a escrita está mapeada para o nível de consistência padrão configurado na sua conta Azure Cosmos.
 
* A Azure Cosmos DB irá mapear dinamicamente a preocupação de leitura especificada pelo condutor do cliente MongoDB a um dos níveis de consistência do Azure Cosmos DB que é configurado dinamicamente a pedido de leitura.  

* Você pode anotar uma região específica associada com a sua conta Azure Cosmos como "Mestre", tornando a região como a primeira região contificada. 

A tabela que se segue ilustra como as preocupações nativas de escrita/leitura do MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao utilizar a API do Azure Cosmos DB para mongoDB:

[![MongoDB consistência modelo mongoDB mapeamento](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os níveis de consistência e compatibilidade entre as APIs DB azure Cosmos com as APIs de código aberto. Consulte os seguintes artigos:

* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [MongoDB apresenta recursos suportados pela API do Azure Cosmos DB para MongoDB](mongodb-feature-support.md)
* [Apache Cassandra apresenta o apoio da Azure Cosmos DB Cassandra API](cassandra-support.md)
