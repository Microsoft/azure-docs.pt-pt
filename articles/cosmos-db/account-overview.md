---
title: Trabalhar com as contas da Azure Cosmos DB
description: Este artigo descreve como criar e usar as contas da Azure Cosmos. Também mostra a hierarquia de elementos numa conta do Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: d01164552b1f3f10f592cedf80eafe57566bf09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91661079"
---
# <a name="work-with-azure-cosmos-account"></a>Trabalhar com uma conta do Azure Cosmos

Azure Cosmos DB é uma plataforma totalmente gerida como um serviço (PaaS). Para começar a usar a Azure Cosmos DB, deverá inicialmente criar uma conta Azure Cosmos na sua subscrição Azure. A sua conta Azure Cosmos contém um nome DNS único e pode gerir uma conta utilizando o portal Azure CLI ou utilizando diferentes SDKs específicos da linguagem. Para mais informações, consulte [como gerir a sua conta Azure Cosmos.](how-to-manage-database-account.md)

A conta Azure Cosmos é a unidade fundamental de distribuição global e alta disponibilidade. Para distribuir globalmente os seus dados e produção em várias regiões do Azure, pode adicionar e remover as regiões do Azure na sua conta Azure Cosmos a qualquer momento. Pode configurar a sua conta Azure Cosmos para ter uma única ou múltiplas regiões de escrita. Para mais informações, consulte [como adicionar e remover as regiões de Azure na sua conta Azure Cosmos.](how-to-manage-database-account.md) Pode configurar o nível [de consistência padrão](consistency-levels.md) na conta Azure Cosmos. A Azure Cosmos DB fornece SLAs abrangentes englobando a produção, latência no percentil 99, consistência e alta disponibilidade. Para mais informações, consulte [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Para gerir de forma segura o acesso a todos os dados dentro da sua conta Azure Cosmos, pode utilizar as [chaves primárias associadas](secure-access-to-data.md) à sua conta. Para garantir um acesso mais seguro aos seus dados, pode configurar um ponto final de [serviço VNET](vnet-service-endpoint.md) e [firewall IP](firewall-support.md) na sua conta Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos numa conta do Azure Cosmos

O contentor Azure Cosmos é a unidade fundamental de escalabilidade. Você pode praticamente ter uma produção de provisão ilimitada (RU/s) e armazenamento em um recipiente. A Azure Cosmos DB partilha de forma transparente o seu recipiente utilizando a chave de partição lógica que especifica para escalar elasticamente a sua produção e armazenamento a provisionados. Para mais informações, consulte [o trabalho com os recipientes e itens da Azure Cosmos.](databases-containers-items.md)

Atualmente, pode criar um máximo de 50 contas Azure Cosmos sob uma subscrição do Azure (este é um limite suave que pode ser aumentado através de pedido de suporte). Uma única conta Azure Cosmos pode gerir virtualmente uma quantidade ilimitada de dados e produção a provisionada. Para gerir os seus dados e o seu rendimento a provisionado, pode criar uma ou mais bases de dados Azure Cosmos na sua conta e dentro dessa base de dados, pode criar um ou mais contentores. A imagem a seguir mostra a hierarquia dos elementos numa conta da Azure Cosmos:

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Hierarquia de uma conta de Azure Cosmos" border="false":::

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir a sua conta Azure Cosmos e outros conceitos:

* [Como gerir a sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhar com contentores e itens da Azure Cosmos](databases-containers-items.md)
* [Ponto final de serviço VNET para a sua conta Azure Cosmos](vnet-service-endpoint.md)
* [FIREWALL para a sua conta Azure Cosmos](firewall-support.md)
* [Como adicionar e remover regiões do Azure na sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
