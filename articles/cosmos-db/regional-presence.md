---
title: Presença regional com Azure Cosmos DB
description: Este artigo explica sobre a presença regional de Azure Cosmos DB e de diferentes ambientes de nuvem.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753232"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presença regional com Azure Cosmos DB

Azure Cosmos DB é um serviço fundamental no Azure, e, por padrão, está sempre disponível em todas as regiões, onde o Azure está disponível. Atualmente, o Azure está disponível em [regiões 54](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. 

[![Regions onde Azure Cosmos DB está disponível](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB está disponível em todos os cinco ambientes distintos de nuvem do Azure disponíveis para os clientes:

* Nuvem **pública do Azure** , que está disponível globalmente.

* O **Azure China 21vianet** está disponível por meio de uma parceria exclusiva entre a Microsoft e a 21vianet, um dos maiores provedores de Internet do país na China.

* O **Azure Alemanha** fornece serviços em um modelo de dados de confiança, que garante que os dados do cliente permaneçam na Alemanha sob o controle de T-Systems International GmbH, uma subsidiária da alemã Telecom, agindo como o data Trustee do alemão.

* O **Azure governamental** está disponível em quatro regiões no Estados Unidos para agências governamentais dos EUA e seus parceiros. 

* O **Azure governamental para o departamento de defesa (DoD)** está disponível em duas regiões do Estados Unidos para o departamento de defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com distribuição global

Todas as APIs expostas por Azure Cosmos DB (incluindo SQL, MongoDB, Cassandra, Gremlin e Table) estão disponíveis em todas as regiões do Azure por padrão. Por exemplo, você pode ter APIs MongoDB e Cassandra expostas por Azure Cosmos DB não apenas em todas as regiões globais do Azure, mas também em nuvens soberanass como China, Alemanha, governo e as regiões do departamento de defesa (DoD).

Azure Cosmos DB é um serviço de banco de dados [distribuído globalmente](distribute-data-globally.md) . Você pode associar qualquer número de regiões do Azure à sua conta do Azure Cosmos e seus dados são replicados de forma automática e transparente. Você pode adicionar ou remover uma região para sua conta do Azure Cosmos a qualquer momento. Com o recurso de distribuição global pronto e o protocolo de replicação com vários mestres, o Azure Cosmos DB oferece menos de 10 ms latências de leitura e gravação no 99 º percentil, disponibilidade de leitura e gravação de 99,999 e capacidade de dimensionamento elástico do provisionamento taxa de transferência para leituras e gravações em todas as regiões associadas à sua conta do Azure Cosmos. Azure Cosmos DB, também oferece cinco modelos de consistência bem definidos e você pode optar por aplicar um modelo de consistência específico aos seus dados. Por fim, Azure Cosmos DB é o único serviço de banco de dados do setor que fornece um [SLA (contrato de nível de serviço)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) abrangente que abrange a taxa de transferência provisionada, latência no 99 º percentil, alta disponibilidade e consistência. Os recursos acima estão disponíveis em todas as nuvens do Azure.

## <a name="next-steps"></a>Passos seguintes

Agora você pode aprender sobre os principais conceitos do Azure Cosmos DB com os seguintes artigos:

* [Distribuição de dados globais](distribute-data-globally.md)
* [Como gerenciar uma conta de Azure Cosmos DB](manage-account.md)
* [Provisionar a taxa de transferência para bancos de dados e contêineres Cosmos do Azure](set-throughput.md)
* [SLA de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
