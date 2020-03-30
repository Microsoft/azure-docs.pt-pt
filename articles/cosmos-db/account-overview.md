---
title: Trabalhar com contas da Azure Cosmos DB
description: Este artigo descreve como criar e usar contas Azure Cosmos. Também mostra a hierarquia de elementos numa conta Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246906"
---
# <a name="work-with-azure-cosmos-account"></a>Trabalhar com uma conta do Azure Cosmos

Azure Cosmos DB é uma plataforma totalmente gerida como um serviço (PaaS). Para começar a utilizar o Azure Cosmos DB, deverá inicialmente criar uma conta Azure Cosmos na sua subscrição Azure. A sua conta Azure Cosmos contém um nome DNS único e pode gerir uma conta utilizando o portal Azure, o Azure CLI ou utilizando diferentes SDKs específicos em línguas. Para mais informações, consulte [como gerir a sua conta Azure Cosmos.](how-to-manage-database-account.md)

A conta Azure Cosmos é a unidade fundamental de distribuição global e alta disponibilidade. Para distribuir globalmente os seus dados e produção em várias regiões azure, pode adicionar e remover as regiões Azure à sua conta Azure Cosmos a qualquer momento. Pode configurar a sua conta Azure Cosmos para ter uma única ou múltipla região de escrita. Para mais informações, consulte [como adicionar e remover as regiões Azure à sua conta Azure Cosmos](how-to-manage-database-account.md). Pode configurar o nível de [consistência padrão](consistency-levels.md) na conta Azure Cosmos. A Azure Cosmos DB fornece SLAs abrangentes que englobam a entrada, latência no percentil 99, consistência e elevada disponibilidade. Para mais informações, consulte [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Para gerir de forma segura o acesso a todos os dados da sua conta Azure Cosmos, pode utilizar as [chaves principais](secure-access-to-data.md) associadas à sua conta. Para garantir um acesso mais seguro aos seus dados, pode configurar um ponto final de [serviço VNET](vnet-service-endpoint.md) e [uma firewall IP](firewall-support.md) na sua conta Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos numa conta Azure Cosmos

O contentor Azure Cosmos é a unidade fundamental da escalabilidade. Pode praticamente ter um fornecimento ilimitado (RU/s) e armazenamento num recipiente. A Azure Cosmos DB divisória transparentemente o seu recipiente utilizando a chave de partição lógica que especifica para escalar elástico a sua entrada e armazenamento aprovisionados. Para mais informações, consulte [o trabalho com recipientes e itens da Azure Cosmos.](databases-containers-items.md)

Atualmente, pode criar um máximo de 100 contas Azure Cosmos sob uma subscrição Azure. Uma única conta Azure Cosmos pode praticamente gerir uma quantidade ilimitada de dados e a sua provisão. Para gerir os seus dados e a sua entrada aprovisionada, pode criar uma ou mais bases de dados da Azure Cosmos na sua conta e dentro dessa base de dados, pode criar um ou mais contentores. A imagem seguinte mostra a hierarquia dos elementos numa conta azure cosmos:

![Hierarquia de uma conta Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir a sua conta Azure Cosmos e outros conceitos:

* [Como gerir a sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhar com contentores e itens Azure Cosmos](databases-containers-items.md)
* [Ponto final do serviço VNET para a sua conta Azure Cosmos](vnet-service-endpoint.md)
* [Firewall IP para a sua conta Azure Cosmos](firewall-support.md)
* [Como adicionar e remover as regiões azure à sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
