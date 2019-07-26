---
title: Presença regional com o Azure Cosmos DB
description: Este artigo explica sobre a presença regional do Azure Cosmos DB e ambientes de cloud diferentes.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384036"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presença regional com o Azure Cosmos DB

Azure Cosmos DB é um serviço fundamental no Azure, e, por padrão, está sempre disponível em todas as regiões, onde o Azure está disponível. Atualmente, o Azure está disponível em [54 regiões](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. 

[![Regiões em que Azure Cosmos DB está disponível](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

O cosmos DB está disponível em todos os cinco ambientes distintos de cloud do Azure disponíveis para clientes:

* **Azure público** nuvem, o que está disponível globalmente.

* O **Azure China 21vianet** está disponível por meio de uma parceria exclusiva entre a Microsoft e a 21vianet, um dos maiores provedores de Internet do país na China.

* **O Azure Alemanha** fornece serviços através de um modelo de consignação de dados, o que garante que o cliente dados permanecem na Alemanha sob o controle do T-Systems International GmbH, uma subsidiária da Deutsche telecomunicações, que funcionam como os dados na Alemanha.

* **O Azure Government** está disponível em quatro regiões nos Estados Unidos para organismos públicos dos E.U.A. e respetivos parceiros. 

* O **Azure governamental para o departamento de defesa (DoD)** está disponível em duas regiões do Estados Unidos para o departamento de defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com a distribuição global

Todas as APIs expostas por Azure Cosmos DB (incluindo SQL, MongoDB, Cassandra, Gremlin e Table) estão disponíveis em todas as regiões do Azure por padrão. Por exemplo, você pode ter APIs MongoDB e Cassandra expostas por Azure Cosmos DB não apenas em todas as regiões globais do Azure, mas também em nuvens soberanass como China, Alemanha, governo e as regiões do departamento de defesa (DoD).

Azure Cosmos DB é um serviço de banco de dados [distribuído globalmente](distribute-data-globally.md) . Pode associar qualquer número de regiões do Azure com a sua conta do Cosmos do Azure e os dados são automática e transparente replicados. Pode adicionar ou remover uma região para a sua conta do Cosmos do Azure em qualquer altura. Com a capacidade de distribuição global chave na mão e o protocolo de replicação dominado por várias, Azure Cosmos DB oferece menos de 10 ms de leitura e escrita latências no percentil 99, 99,999 ler e escrever a disponibilidade e capacidade de dimensionar de forma elástica aprovisionado taxa de transferência para leituras e escritas em todas as regiões à conta do Cosmos do Azure. O Azure Cosmos DB, também oferece cinco modelos de consistência bem definidos e pode optar por aplicar um modelo de consistência específicos aos seus dados. Por fim, Azure Cosmos DB é o único serviço de banco de dados do setor que fornece um [SLA (contrato de nível de serviço)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) abrangente que abrange a taxa de transferência provisionada, latência no 99 º percentil, alta disponibilidade e consistência. Os recursos acima estão disponíveis em todas as nuvens do Azure.

## <a name="next-steps"></a>Passos seguintes

Agora você pode aprender sobre os principais conceitos do Azure Cosmos DB com os seguintes artigos:

* [Distribuição de dados globais](distribute-data-globally.md)
* [Como gerir uma conta do Azure Cosmos DB](manage-account.md)
* [Débito de aprovisionamento para contentores do Cosmos do Azure e bases de dados](set-throughput.md)
* [SLA de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
