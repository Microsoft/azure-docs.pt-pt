---
title: Presença regional com Azure Cosmos DB
description: Este artigo explica sobre a presença regional de Azure Cosmos DB e diferentes ambientes de nuvem.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753232"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presença regional com Azure Cosmos DB

O Azure Cosmos DB é um serviço fundacional em Azure, e, por padrão, está sempre disponível em todas as regiões, onde o Azure está disponível. Atualmente, o Azure está disponível em [54 regiões](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. 

[![Regiões onde o Azure Cosmos DB está disponível](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB está disponível em todos os cinco ambientes de nuvem Azure distintos disponíveis para os clientes:

* **Nuvem pública azul,** que está disponível globalmente.

* **A Azure China 21Vianet** está disponível através de uma parceria única entre a Microsoft e a 21Vianet, um dos maiores fornecedores de internet do país na China.

* A **Azure Germany** presta serviços ao abrigo de um modelo de fiduciário de dados, que garante que os dados dos clientes permanecem na Alemanha sob o controlo da T-Systems International GmbH, uma subsidiária da Deutsche Telecom, atuando como o administrador alemão de dados.

* O **Governo azure** está disponível em quatro regiões dos Estados Unidos para agências governamentais dos EUA e seus parceiros. 

* **O Governo de Azure para o Departamento de Defesa (DoD)** está disponível em duas regiões dos Estados Unidos para o Departamento de Defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com distribuição global

Todos os APIs expostos pela Azure Cosmos DB (incluindo SQL, MongoDB, Cassandra, Gremlin e Table) estão disponíveis em todas as regiões de Azure por padrão. Por exemplo, pode ter APIs MongoDB e Cassandra expostos pela Azure Cosmos DB não só em todas as regiões globais de Azure, mas também em nuvens soberanas como china, Alemanha, Governo e Regiões do Departamento de Defesa (DoD).

Azure Cosmos DB é um serviço de base de dados [distribuído globalmente.](distribute-data-globally.md) Pode associar qualquer número de regiões Azure à sua conta Azure Cosmos e os seus dados são replicados automaticamente e de forma transparente. Pode adicionar ou remover uma região à sua conta Azure Cosmos a qualquer momento. Com a capacidade de distribuição global chave na mão e o protocolo de replicação multi-masterizado, o Azure Cosmos DB oferece menos de 10 ms de leitura e escrita de lateências no percentil 99, 99.999 disponibilidade de leitura e escrita, e capacidade de escala elástica aprovisionada entrada para leituras e escritos em todas as regiões associadas à sua conta Azure Cosmos. O Azure Cosmos DB também oferece cinco modelos de consistência bem definidos e pode optar por aplicar um modelo de consistência específico aos seus dados. Por último, a Azure Cosmos DB é o único serviço de base de dados do setor que fornece um Acordo de Nível de Serviço abrangente [(SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) que abrange a provisão prevista, a latência no percentil 99, a elevada disponibilidade e a consistência. As capacidades acima estão disponíveis em todas as nuvens Azure.

## <a name="next-steps"></a>Passos seguintes

Agora pode aprender sobre conceitos centrais de Azure Cosmos DB com os seguintes artigos:

* [Distribuição global de dados](distribute-data-globally.md)
* [Como gerir uma conta Azure Cosmos DB](manage-account.md)
* [Provisão para contentores e bases de dados Da Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
