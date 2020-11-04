---
title: Amostras Azure PowerShell para Azure Cosmos DB Gremlin API
description: Obtenha as amostras Azure PowerShell para executar tarefas comuns na AZure Cosmos DB Gremlin API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: c406ea58bfba0128a31489aa7b834c6a586d74d3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332955"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Amostras Azure PowerShell para Azure Cosmos DB Gremlin API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

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

## <a name="gremlin-api-samples"></a>Amostras de API de Gremlin

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, base de dados e gráfico](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, base de dados e gráfico Azure Cosmos. |
|[Criar uma conta, base de dados e gráfico com autoescala](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, base de dados e gráfico Azure Cosmos com autoescala. |
|[Listar ou obter bases de dados ou gráficos](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha base de dados ou gráfico. |
|[Operações de produção](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de produção de uma base de dados ou gráfico, incluindo obter, atualizar e migrar entre a autoescala e a produção padrão. |
|[Bloquear recursos da supressão](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
