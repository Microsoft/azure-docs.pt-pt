---
title: Apache Cassandra e Azure Cosmos DB níveis de consistência
description: Apache Cassandra e Azure Cosmos DB níveis de consistência.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 155e1ed5b089c16887c845c94f36c9c6d9f0af6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284613"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra e Azure Cosmos DB níveis de consistência

Ao contrário de Azure Cosmos DB, Apache Cassandra não fornece garantias de consistência definidas de forma precisa. Em vez disso, a Apache Cassandra proporciona um nível de consistência de escrita e um nível de consistência de leitura, para permitir a alta disponibilidade, consistência e compensações de latência. Ao utilizar a API Cassandra da Azure Cosmos DB:

* O nível de consistência da escrita de Apache Cassandra está mapeado para o nível de consistência padrão configurado na sua conta Azure Cosmos. A consistência para uma operação de escrita (CL) não pode ser alterada por pedido.

* A Azure Cosmos DB irá mapear dinamicamente o nível de consistência de leitura especificado pelo condutor do cliente Cassandra para um dos níveis de consistência DB do Azure Cosmos configurados dinamicamente num pedido de leitura.

## <a name="mapping-consistency-levels"></a>Mapeamento dos níveis de consistência

A tabela a seguir ilustra como os níveis de consistência nativas de Cassandra são mapeados para os níveis de consistência do Azure Cosmos DB ao utilizar a API de Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapeamento do modelo de consistência Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes ficarem fortes e consistentes com leituras para as suas cargas de trabalho, olhando para a métrica *Probabilisticamente Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, consulte a [métrica de Staleness (PBS) (Monitor Probabilisisticamente Bounded).](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

A estagnação deslimaturada probabilística mostra como a sua eventual consistência é a sua eventual consistência. Esta métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você tem atualmente configurado na sua conta Azure Cosmos. Por outras palavras, pode-se ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os níveis de distribuição e consistência globais para a Azure Cosmos DB:

* [Descrição geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Elevada disponibilidade](high-availability.md)
