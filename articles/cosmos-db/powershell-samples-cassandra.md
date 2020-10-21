---
title: Amostras Azure PowerShell para Azure Cosmos DB Cassandra API
description: Obtenha as amostras Azure PowerShell para executar tarefas comuns em Azure Cosmos DB Cassandra API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: d4cd0e5980a0bd80f1a25d4aae36a3599fed13e3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92285106"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Amostras Azure PowerShell para Azure Cosmos DB Cassandra API

A tabela seguinte inclui links para scripts Azure PowerShell comumente usados para Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os cmdlets Azure Cosmos DB PowerShell estão disponíveis na [Referência Azure PowerShell](/powershell/module/az.cosmosdb). Por favor, verifique se há atualizações `Az.CosmosDB` regularmente. Você também pode forme estas amostras PowerShell para Cosmos DB do nosso repositório GitHub, [Cosmos DB PowerShell Samples no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Amostras comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as regiões de uma conta cosmos DB. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos DB. |
|[Criar uma conta Cosmos com firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos com ip firewall ativada. |
|||

## <a name="cassandra-api-samples"></a>Amostras de Cassandra API

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, espaço-chave e tabela](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta Azure Cosmos, espaço-chave e tabela. |
|[Criar uma conta, espaço-chave e tabela com autoescala](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta Azure Cosmos, espaço-chave e tabela com autoescala. |
|[Listar ou obter espaços ou tabelas chave](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha espaços ou tabelas de chaves. |
|[Operações de produção](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de produção para um espaço-chave ou tabela, incluindo obter, atualizar e migrar entre a escala automática e a produção padrão. |
|[Bloquear recursos da supressão](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
