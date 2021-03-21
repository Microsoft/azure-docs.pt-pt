---
title: Amostras Azure PowerShell para Azure Cosmos DB Core (SQL) API
description: Obtenha as amostras Azure PowerShell para executar tarefas comuns em Azure Cosmos DB para Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: ab9904127d085113ba09bf6fcd3616842dade14d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503315"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Amostras Azure PowerShell para Azure Cosmos DB Core (SQL) API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A tabela seguinte inclui links para scripts Azure PowerShell comumente usados para Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os cmdlets Azure Cosmos DB PowerShell estão disponíveis na [Referência Azure PowerShell](/powershell/module/az.cosmosdb). O `Az.CosmosDB` módulo agora faz parte do `Az` módulo. [Descarregue e instale](/powershell/azure/install-az-ps) a versão mais recente do módulo Az para obter os cmdlets DB do Azure Cosmos. Também pode obter a versão mais recente da [PowerShell Gallery.](https://www.powershellgallery.com/packages/Az/5.4.0) Você também pode forme estas amostras PowerShell para Cosmos DB do nosso repositório GitHub, [Cosmos DB PowerShell Samples no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Para os cmdlets PowerShell para outras APIs ver [amostras de powerShell para Cassandra,](powershell-samples-cassandra.md) [Amostras de PowerShell para API mongodb,](powershell-samples-mongodb.md) [amostras de energia para Gremlin,](powershell-samples-gremlin.md) [Amostras de Energia para tabela](powershell-samples-table.md)

## <a name="common-samples"></a>Amostras comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as regiões de uma conta cosmos DB. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos DB. |
|[Criar uma conta Cosmos com firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos com ip firewall ativada. |
|||

## <a name="core-sql-api-samples"></a>Amostras de API core (SQL)

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, base de dados e contentor](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos, base de dados e contentor. |
|[Criar uma conta, base de dados e contentor com autoescala](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos, base de dados e contentor com autoescala. |
|[Criar um recipiente com uma grande chave de partição](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um recipiente com uma grande chave de partição. |
|[Criar um recipiente sem política de índice](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um recipiente Azure Cosmos com a política de índice desligada.|
|[Listar ou obter bases de dados ou contentores](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha base de dados ou contentores. |
|[Operações de produção](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de produção de uma base de dados ou contentor, incluindo obter, atualizar e migrar entre a escala automática e a produção padrão. |
|[Bloquear recursos da supressão](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
