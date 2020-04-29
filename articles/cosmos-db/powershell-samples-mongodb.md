---
title: Amostras azure PowerShell para Azure Cosmos DB - MongoDB API
description: Obtenha as amostras Azure PowerShell para executar várias tarefas comuns na API da Azure Cosmos DB para o MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366210"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Amostras azure PowerShell para Azure Cosmos DB MongoDB API

A tabela seguinte inclui links para provar scripts Azure PowerShell para Azure Cosmos DB para MongoDB API.

> [!NOTE]
> Atualmente só pode criar versão 3.2 (isto é, contas `*.documents.azure.com`usando o ponto final no formato) da API do Azure Cosmos DB para contas MongoDB utilizando modelos PowerShell, CLI e Resource Manager. Para criar 3.6 versão de contas, utilize o portal Azure.

> [!NOTE]
> As amostras usam cmdlets de gestão [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) Por favor, `Az.CosmosDB` note que as cmdlets ainda estão na pré-visualização e podem mudar antes de serem libertadas. Por favor, verifique `Az.CosmosDB` se há atualizações regularmente.

| | |
|---|---|
|[Criar uma conta, base de dados e recolha](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta Azure Cosmos, base de dados e recolha. |
|[Listar ou obter bases de dados ou coleções](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista ou obter base de dados ou recolha. |
|[Obter RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Coma RU/s para uma base de dados ou recolha. |
|[Atualização RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar RU/s para uma base de dados ou recolha. |
|[Atualizar uma conta ou adicionar uma região](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Adicione uma região a uma conta cosmos. Também pode ser usado para modificar outras propriedades da conta, mas estas devem ser separadas das alterações às regiões. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cordas de ligação](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos. |
|[Crie uma Conta Cosmos com firewall IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta Azure Cosmos com ip firewall ativado. |
|||
