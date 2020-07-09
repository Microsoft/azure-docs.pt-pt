---
title: Presença regional com Azure Cosmos DB
description: Este artigo explica sobre a presença regional de Azure Cosmos DB e diferentes ambientes em nuvem.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 78f02c07932c6a058ba4fa27289640b7efd56169
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85113675"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presença regional com Azure Cosmos DB

Azure Cosmos DB é um serviço fundamental em Azure, e, por padrão, está sempre disponível em todas as regiões, onde o Azure está disponível. Atualmente, a Azure está disponível em [54 regiões](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. 

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Regiões onde a Azure Cosmos DB está disponível" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB está disponível em todos os cinco ambientes distintos da nuvem Azure disponíveis para os clientes:

* **Nuvem pública azul,** que está disponível globalmente.

* **O Azure China 21Vianet** está disponível através de uma parceria única entre a Microsoft e a 21Vianet, um dos maiores fornecedores de internet do país na China.

* **A Azure Germany** presta serviços ao abrigo de um modelo de fiduciário de dados, que garante que os dados dos clientes permanecem na Alemanha sob o controlo da T-Systems International GmbH, uma subsidiária da Deutsche Telecom, atuando como o administrador de dados alemão.

* **O Governo de Azure** está disponível em quatro regiões dos Estados Unidos para agências governamentais dos EUA e seus parceiros. 

* **O Governo de Azure para o Departamento de Defesa (DoD)** está disponível em duas regiões dos Estados Unidos para o Departamento de Defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com distribuição global

Todas as APIs expostas por Azure Cosmos DB (incluindo SQL, MongoDB, Cassandra, Gremlin e Table) estão disponíveis em todas as regiões do Azure por padrão. Por exemplo, você pode ter MongoDB e Cassandra APIs expostos por Azure Cosmos DB não só em todas as regiões globais do Azure, mas também em nuvens soberanas como china, Alemanha, Governo e Departamento de Defesa (DoD).

Azure Cosmos DB é um serviço de base de [dados distribuído globalmente.](distribute-data-globally.md) Pode associar qualquer número de regiões do Azure à sua conta Azure Cosmos e os seus dados são replicados de forma automática e transparente. Pode adicionar ou remover uma região na sua conta Azure Cosmos a qualquer momento. Com a capacidade de distribuição global chave na mão e o protocolo de replicação multi-masterizado, a Azure Cosmos DB oferece menos de 10 ms de leitura e escrita de latências no percentil 99, 99.999 leitura e escrita disponibilidade, e capacidade de dimensionar elasticamente a produção provisível para leituras e escritas em todas as regiões associadas à sua conta Azure Cosmos. A Azure Cosmos DB, também oferece cinco modelos de consistência bem definidos e pode optar por aplicar um modelo de consistência específico aos seus dados. Finalmente, a Azure Cosmos DB é o único serviço de base de dados na indústria que fornece um acordo abrangente [de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) que engloba a produção, latência no percentil 99, alta disponibilidade e consistência. As capacidades acima estão disponíveis em todas as nuvens Azure.

## <a name="next-steps"></a>Próximos passos

Agora pode aprender sobre conceitos fundamentais da Azure Cosmos DB com os seguintes artigos:

* [Distribuição global de dados](distribute-data-globally.md)
* [Como gerir uma conta DB da Azure Cosmos](manage-account.md)
* [Produção de provisões para contentores e bases de dados da Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
