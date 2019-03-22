---
title: Trabalhar com contas do Azure Cosmos DB
description: Este artigo descreve como criar e utilizar contas do Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: e40b763aa2a36e3afd85a82b66e393ac78d3afaa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839581"
---
# <a name="work-with-azure-cosmos-account"></a>Trabalhar com uma conta do Azure Cosmos

O Azure Cosmos DB é um totalmente gerido plataforma-como-um-serviço (PaaS). Para começar a utilizar o Azure Cosmos DB, inicialmente deve criar uma conta do Cosmos do Azure na sua subscrição do Azure. Sua conta do Cosmos do Azure contém um nome DNS exclusivo e pode gerir uma conta com o portal do Azure, CLI do Azure ou através de SDKs de específicas de idiomas diferentes. Para obter mais informações, consulte [como gerir a sua conta do Azure Cosmos](how-to-manage-database-account.md).

A conta do Cosmos do Azure é a unidade fundamental de distribuição global e de elevada disponibilidade. Para distribuir globalmente os seus dados e débito em várias regiões do Azure, pode adicionar e remover regiões do Azure à sua conta do Cosmos do Azure em qualquer altura. Pode configurar a sua conta do Cosmos do Azure para ter uma única ou várias regiões de escrita. Para obter mais informações, consulte [como adicionar e remover regiões do Azure à sua conta do Azure Cosmos](how-to-manage-database-account.md). Pode configurar o [consistência predefinida](consistency-levels.md) nível na conta do Cosmos do Azure. O Azure Cosmos DB oferece SLAs abrangentes que abrange o débito, latência no percentil de 99, a consistência e a elevada disponibilidade. Para obter mais informações, consulte [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Para gerir com segurança o acesso a todos os dados na sua conta do Cosmos do Azure, pode utilizar as chaves mestras associadas à sua conta. Para proteger ainda mais o acesso aos seus dados, pode configurar um ponto final de serviço VNET e a firewall do IP na sua conta do Cosmos do Azure. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos de uma conta do Cosmos do Azure

O contentor do Azure Cosmos DB é a unidade fundamental de escalabilidade. Pode ter praticamente ilimitado débito aprovisionado (RU/s) e o armazenamento num contêiner. O Azure Cosmos DB de forma transparente partições do seu contentor com a chave de partição lógica que especificar para dimensionar de forma elástica o débito aprovisionado e armazenamento. Para obter mais informações, consulte [trabalhar com contentores do Azure Cosmos e itens](databases-containers-items.md).

Atualmente, pode criar um máximo de 100 contas do Cosmos do Azure sob uma subscrição do Azure. Uma única conta do Cosmos do Azure virtualmente pode gerir uma quantidade ilimitada de dados e o débito aprovisionado. Para gerir os seus dados e o débito aprovisionado, pode criar um ou mais bases de dados do Cosmos do Azure na sua conta e nesse banco de dados, pode criar um ou mais contentores. A imagem seguinte mostra a hierarquia dos elementos numa conta do Cosmos do Azure:

![Hierarquia de uma conta do Cosmos do Azure](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber como gerir a sua conta do Cosmos do Azure ou veja outros conceitos associados com o Azure Cosmos DB:

* [Como gerir a sua conta do Cosmos do Azure](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhar com contentores do Azure Cosmos e itens](databases-containers-items.md)
* [Ponto final de serviço VNET para a sua conta do Cosmos do Azure](vnet-service-endpoint.md)
* [Firewall do IP para a sua conta do Cosmos do Azure](firewall-support.md)
* [Procedimentos: adicionar e remover regiões do Azure à sua conta do Cosmos do Azure](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
