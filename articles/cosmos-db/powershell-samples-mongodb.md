---
title: Amostras Azure PowerShell para Azure Cosmos DB - MongoDB API
description: Obtenha as amostras do Azure PowerShell para executar várias tarefas comuns na API da Azure Cosmos DB para a MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 697dc68018ced08e22efdc179f84a2c968f953a2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563868"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Amostras Azure PowerShell para Azure Cosmos DB MongoDB API

A tabela seguinte inclui links para provar scripts Azure PowerShell para Azure Cosmos DB para MongoDB API.

> [!NOTE]
> Atualmente só é possível criar a versão 3.2 (isto é, contas que utilizam o ponto final no formato `*.documents.azure.com` ) da API da Azure Cosmos DB para as contas MongoDB utilizando modelos powerShell, CLI e Resource Manager. Para criar a versão 3.6 das contas, utilize o portal Azure.

> [!NOTE]
> As amostras usam cmdlets de gestão [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Por favor, verifique se há atualizações `Az.CosmosDB` regularmente.

|Tarefa | Description |
|---|---|
|[Criar uma conta, base de dados e recolha](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, base de dados e recolha da Azure Cosmos. |
|[Listar ou obter bases de dados ou coleções](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha base de dados ou recolha. |
|[Obter RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha RU/s para uma base de dados ou coleção. |
|[Atualizar RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize RU/s para uma base de dados ou recolha. |
|[Atualizar uma conta ou adicionar uma região](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adicione uma região a uma conta cosmos. Também pode ser usado para modificar outras propriedades da conta, mas estes devem ser separados das alterações às regiões. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos. |
|[Criar uma conta Cosmos com firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta Azure Cosmos com IP Firewall ativada. |
|[Bloquear recursos da supressão](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
