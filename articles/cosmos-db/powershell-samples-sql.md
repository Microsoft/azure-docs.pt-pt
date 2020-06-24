---
title: Amostras Azure PowerShell para Azure Cosmos DB - SQL (Core) API
description: Obtenha as amostras Azure PowerShell para executar várias tarefas comuns nas contas API API da Azure Cosmos DB SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 82d709567e75540a865758162ac87f55dfa84bea
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126098"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Amostras Azure PowerShell para Azure Cosmos DB - SQL (Core) API

A tabela seguinte inclui links para scripts Azure PowerShell comumente usados para Azure Cosmos DB para API SQL (Core). Se quiser retirar estas amostras powerShell para Cosmos DB do nosso repositório GitHub, visite [cosmos DB PowerShell Samples no GitHub.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)

Para amostras adicionais da Cosmos DB PowerShell para API (Core) E documentação, consulte [Manage Azure Cosmos DB SQL API resources usando PowerShell](manage-with-powershell.md). Para amostras cosmos DB PowerShell para outras APIs, consulte [Cassandra API,](powershell-samples-cassandra.md) [MongoDB API,](powershell-samples-mongodb.md) [Gremlin API](powershell-samples-gremlin.md)e [Table API](powershell-samples-table.md).

> [!NOTE]
> As amostras usam cmdlets de gestão [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Por favor, verifique se há atualizações `Az.CosmosDB` regularmente.

| | |
|---|---|
|[Criar uma conta, base de dados e contentor](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos, base de dados e contentor. |
|[Criar um recipiente com uma grande chave de partição](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um recipiente com uma grande chave de partição. |
|[Listar ou obter bases de dados ou contentores](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha base de dados ou contentores. |
|[Obter RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha RU/s para uma base de dados ou contentor. |
|[Atualizar RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize RU/s para uma base de dados ou recipiente. |
|[Criar um recipiente sem política de índice](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um recipiente Azure Cosmos com a política de índice desligada.|
|[Atualizar uma conta](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as regiões de uma conta cosmos DB. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos DB. |
|[Criar uma conta Cosmos com firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos com ip firewall ativada. |
|[Bloquear recursos da supressão](scripts/powershell/sql/powershell-sql-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
