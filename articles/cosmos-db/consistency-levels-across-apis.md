---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Compreender os níveis de consistência através de APIs no Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235617"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

O Azure Cosmos DB fornece suporte nativo para conexão APIs compatíveis com o protocolo para bases de dados populares. Estes incluem o armazenamento de MongoDB, Apache Cassandra, Gremlin e tabelas do Azure. Esses bancos de dados não oferecem precisamente os modelos de consistência definidos ou garantias apoiado pelo SLA para os níveis de consistência. Normalmente, eles fornecem apenas um subconjunto dos modelos de cinco consistência oferecidos pelo Azure Cosmos DB. 

Ao utilizar a API de SQL, Gremlin API e API de tabela, é utilizado o nível de consistência predefinido configurado na conta do Cosmos do Azure. 

Ao utilizar a API de Cassandra API ou do Azure Cosmos DB para o MongoDB, aplicativos de obtém um conjunto completo de níveis de consistência oferecidos pelo Apache Cassandra e MongoDB, respetivamente, com ainda mais forte consistência e garantias de durabilidade. Este documento mostra os níveis de consistência correspondentes do Azure Cosmos DB para Apache Cassandra e níveis de consistência do MongoDB.


## <a id="cassandra-mapping"></a>Mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

Ao contrário de AzureCosmos DB, o Apache Cassandra fornece nativamente precisamente as garantias de consistência definidos.  Em vez disso, o Apache Cassandra fornece um nível de consistência de escrita e um nível de consistência de leitura, para ativar as compensações de latência, consistência e disponibilidade elevadas. Ao utilizar a API de Cassandra do Azure Cosmos DB: 

* O nível de consistência de escrita de Apache Cassandra está mapeado para o nível de consistência predefinido configurado na sua conta do Cosmos do Azure. 

* O Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo controlador de cliente do Cassandra para um dos níveis de consistência do Azure Cosmos DB configurados dinamicamente numa solicitação de leitura. 

A tabela a seguir ilustra como os níveis de consistência nativos do Cassandra são mapeados para níveis de consistência do Azure Cosmos DB ao utilizar a API de Cassandra:  

[![Mapeamento do modelo de consistência de Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de consistência do MongoDB e o Azure Cosmos DB

Ao contrário do Azure Cosmos DB, a MongoDB nativo não fornece garantias de consistência definidas precisamente. Em vez disso, o MongoDB nativa permite aos utilizadores configurar as seguintes garantias de consistência: uma preocupação de escrita, uma preocupação de leitura e a diretiva de isMaster - para direcionar as operações de leitura a réplicas primárias ou secundárias para alcançar o nível de consistência pretendido. 

Ao utilizar a API do Azure Cosmos DB para o MongoDB, o driver do MongoDB trata a sua região de escrita como a réplica primária e todas as outras regiões são lidas a réplica. Pode escolher qual a região à conta do Cosmos do Azure como uma réplica primária. 

Ao utilizar a API do Azure Cosmos DB para o MongoDB:

* A preocupação de escrita é mapeada para o nível de consistência predefinido configurado na sua conta do Cosmos do Azure.
 
* O Azure Cosmos DB dinamicamente mapeará a preocupação de leitura especificada pelo controlador de cliente MongoDB para um dos níveis de consistência do Azure Cosmos DB, que está configurado dinamicamente numa solicitação de leitura. 

* Pode anotar uma região específica à conta do Cosmos do Azure como "Principal", tornando a região que a primeira região gravável. 

A tabela seguinte ilustra como o MongoDB nativo escrita/leitura preocupações estão mapeadas para os níveis de consistência do Cosmos do Azure ao utilizar a API do Azure Cosmos DB para o MongoDB:

[![Mapeamento de modelo de consistência do MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os níveis de consistência e a compatibilidade entre as APIs do Azure Cosmos DB com as APIs de código-fonte aberto. Consulte os seguintes artigos:

* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Funcionalidades do MongoDB suportadas pela API do Azure Cosmos DB para o MongoDB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra suportados a API de Cassandra do Azure Cosmos DB](cassandra-support.md)