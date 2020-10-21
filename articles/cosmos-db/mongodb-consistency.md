---
title: Mapeamento dos níveis de consistência para Azure Cosmos DB API para MongoDB
description: Mapeamento dos níveis de consistência para Azure Cosmos DB API para MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 19dcc0f7605ae771e2ff826299413eaa073f212b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284614"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Níveis de consistência para Azure Cosmos DB e a API para MongoDB

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência definidas com precisão. Em vez disso, a MongoDB nativa permite que os utilizadores confivelem as seguintes garantias de consistência: uma preocupação de escrita, uma preocupação de leitura, e a diretiva isMaster - para direcionar as operações de leitura para réplicas primárias ou secundárias para atingir o nível de consistência desejado.

Ao utilizar a API da Azure Cosmos DB para o MongoDB, o condutor do MongoDB trata a sua região de escrita como a réplica primária e todas as outras regiões são lidas réplicas. Pode escolher qual região associada à sua conta Azure Cosmos como réplica primária.

> [!NOTE]
> O modelo de consistência padrão para Azure Cosmos DB é Session. Session é um modelo de consistência centrado no cliente que não é suportado de forma nativa por Cassandra ou MongoDB. Para mais informações sobre qual modelo de consistência escolher ver, [níveis de consistência em Azure Cosmos DB](consistency-levels.md)

Ao usar a API da Azure Cosmos DB para a MongoDB:

* A preocupação com a escrita está mapeada para o nível de consistência padrão configurado na sua conta Azure Cosmos.

* A Azure Cosmos DB irá mapear dinamicamente a preocupação de leitura especificada pelo controlador cliente MongoDB para um dos níveis de consistência do Azure Cosmos DB que é configurado dinamicamente num pedido de leitura.  

* Você pode anotar uma região específica associada à sua conta Azure Cosmos como "Primária" tornando a região como a primeira região writable. 

## <a name="mapping-consistency-levels"></a>Mapeamento dos níveis de consistência

A tabela a seguir ilustra como as preocupações nativas de escrita/leitura da MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao utilizar a API da Azure Cosmos para a MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapeamento do modelo de consistência mongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes ficarem fortes e consistentes com leituras para as suas cargas de trabalho, olhando para a métrica *Probabilisticamente Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, consulte a [métrica de Staleness (PBS) (Monitor Probabilisisticamente Bounded).](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

A estagnação deslimaturada probabilística mostra como a sua eventual consistência é a sua eventual consistência. Esta métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você tem atualmente configurado na sua conta Azure Cosmos. Por outras palavras, pode-se ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os níveis de distribuição e consistência globais para a Azure Cosmos DB:

* [Descrição geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Elevada disponibilidade](high-availability.md)
