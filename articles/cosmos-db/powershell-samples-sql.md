---
title: Amostras Azure PowerShell para Azure Cosmos DB - SQL (Core) API
description: Obtenha as amostras Azure PowerShell para executar várias tarefas comuns nas contas DaPi API Da Azure Cosmos DB SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366177"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Amostras Azure PowerShell para Azure Cosmos DB - SQL (Core) API

A tabela seguinte inclui links para scripts Azure PowerShell comumente usados para Azure Cosmos DB para API SQL (Core). Se quiser bifurcar estas amostras PowerShell para Cosmos DB do nosso repositório GitHub, visite [cosmos DB PowerShell Samples no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Para obter amostras adicionais da Cosmos DB PowerShell para API SQL (Core) e documentação, consulte [manage Azure Cosmos DB SQL API recursos utilizando powerShell](manage-with-powershell.md). Para amostras cosmos DB PowerShell para outras APIs, consulte [Cassandra API,](powershell-samples-cassandra.md) [MongoDB API,](powershell-samples-mongodb.md) [Gremlin API](powershell-samples-gremlin.md)e [Table API](powershell-samples-table.md).

> [!NOTE]
> As amostras usam cmdlets de gestão [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Por favor, `Az.CosmosDB` note que as cmdlets ainda estão na pré-visualização e podem mudar antes de serem libertadas. Por favor, verifique `Az.CosmosDB` se há atualizações regularmente.

| | |
|---|---|
|[Criar uma conta, base de dados e um recipiente](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta Azure Cosmos DB, base de dados e recipiente. |
|[Crie um recipiente com uma grande chave de partição](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um recipiente com uma grande chave de partição. |
|[Lista ou obter bases de dados ou contentores](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista ou obter base de dados ou contentores. |
|[Obter RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Coma RU/s para uma base de dados ou recipiente. |
|[Atualização RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar o RU/s para uma base de dados ou contentor. |
|[Criar um recipiente sem política de índices](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contentor Azure Cosmos com a política de índices desligada.|
|[Atualizar uma conta](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão da conta Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as regiões da conta Cosmos DB. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cordas de ligação](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos DB. |
|[Crie uma Conta Cosmos com firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta Azure Cosmos DB com ip firewall ativado. |
|||
