---
title: Apache Cassandra e Azure Cosmos DB níveis de consistência
description: Apache Cassandra e Azure Cosmos DB níveis de consistência.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516856"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra e Azure Cosmos DB Cassandra API níveis de consistência
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ao contrário de Azure Cosmos DB, Apache Cassandra não fornece garantias de consistência definidas de forma precisa. Em vez disso, a Apache Cassandra proporciona um nível de consistência de escrita e um nível de consistência de leitura, para permitir a alta disponibilidade, consistência e compensações de latência. Ao utilizar a API Cassandra da Azure Cosmos DB:

* O nível de consistência da escrita de Apache Cassandra está mapeado para o nível de consistência padrão configurado na sua conta Azure Cosmos. A consistência para uma operação de escrita (CL) não pode ser alterada por pedido.

* A Azure Cosmos DB irá mapear dinamicamente o nível de consistência de leitura especificado pelo condutor do cliente Cassandra para um dos níveis de consistência DB do Azure Cosmos configurados dinamicamente num pedido de leitura.

## <a name="multi-region-writes-vs-single-region-writes"></a>Multi-região escreve vs Região única escreve

A base de dados Apache Cassandra é um sistema multi-mestre por padrão, e não fornece uma opção fora da caixa para escrever uma única região com replicação multi-região para leituras. No entanto, a Azure Cosmos DB fornece capacidade chave na mão para ter configurações de escrita únicas ou [multi-regiões.](how-to-multi-master.md) Uma das vantagens de poder escolher uma única região escrever configuração em várias regiões é evitar cenários de conflitos entre regiões e a opção de manter uma forte consistência em várias regiões. 

Com as gravações de uma região única, pode manter uma forte consistência, mantendo ao mesmo tempo um nível de elevada disponibilidade entre regiões com [falha automática.](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage) Nesta configuração, ainda é possível explorar a localidade de dados para reduzir a latência da leitura, reduzindo para eventual consistência por pedido. Além destas capacidades, a plataforma DB Azure Cosmos também fornece a capacidade de ativar a [redundância](high-availability.md#availability-zone-support) de zona ao selecionar uma região. Assim, ao contrário da nativa Apache Cassandra, a Azure Cosmos DB permite-lhe navegar no [espectro de trocas de teorema](consistency-levels.md#rto) da CAP com mais granularidade.

## <a name="mapping-consistency-levels"></a>Mapear os níveis de consistência

A plataforma DB Azure Cosmos fornece um conjunto de cinco configurações de consistência orientadas para o caso de uso de negócios bem definidas no que diz respeito à replicação e às trocas definidas pelo [teorema da PAC](https://en.wikipedia.org/wiki/CAP_theorem) e [pelo teorema PACLC.](https://en.wikipedia.org/wiki/PACELC_theorem) Como esta abordagem difere significativamente da Apache Cassandra, recomendamos que você tenha tempo para rever e entender as definições de consistência do Azure Cosmos DB na nossa [documentação](consistency-levels.md), ou assistir a este pequeno guia [de vídeo](https://www.youtube.com/watch?v=t1--kZjrG-o) para entender as definições de consistência na plataforma DB Azure Cosmos.

A tabela a seguir ilustra os possíveis mapeamentos entre apache Cassandra e Azure Cosmos DB níveis de consistência ao usar Cassandra API. Isto mostra configurações para uma única região, leituras multi-regiões com escritos de uma única região, e escritos multi-regiões.

> [!NOTE]
> Estes não são mapeamentos exatos. Em vez disso, fornecemos os análogos mais próximos a Apache Cassandra, e desambiguados quaisquer diferenças qualitativas na coluna mais direita. Como mencionado acima, recomendamos rever as definições de consistência da Azure [Cosmos](consistency-levels.md)DB . 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mapeamento do nível da conta de consistência cassandra" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Mapeamento dinâmico de consistência cassandra" lightbox="./media/cassandra-consistency/dynamic.png" :::

Se a sua conta Azure Cosmos estiver configurada com um nível de consistência diferente da forte consistência, pode descobrir a probabilidade de os seus clientes ficarem fortes e consistentes com leituras para as suas cargas de trabalho, olhando para a métrica *Probabilisticamente Bounded Staleness* (PBS). Esta métrica está exposta no portal Azure, para saber mais, consulte a [métrica de Staleness (PBS) (Monitor Probabilisisticamente Bounded).](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

A estagnação deslimaturada probabilística mostra como a sua eventual consistência é a sua eventual consistência. Esta métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você tem atualmente configurado na sua conta Azure Cosmos. Por outras palavras, pode-se ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de escrita e leitura.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os níveis de distribuição e consistência globais para a Azure Cosmos DB:

* [Descrição geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Elevada disponibilidade](high-availability.md)
