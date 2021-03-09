---
title: Amostras Azure PowerShell para Azure Cosmos DB API para MongoDB
description: Obtenha as amostras Azure PowerShell para executar tarefas comuns em Azure Cosmos DB API para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 2f852b1ec359033c8bc700c8a382705b4479d6de
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503356"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Amostras Azure PowerShell para Azure Cosmos DB API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A tabela seguinte inclui links para scripts Azure PowerShell comumente usados para Azure Cosmos DB. Utilize os links no direito de navegar para amostras específicas da API. As amostras comuns são as mesmas em todas as APIs. As páginas de referência para todos os cmdlets Azure Cosmos DB PowerShell estão disponíveis na [Referência Azure PowerShell](/powershell/module/az.cosmosdb). O `Az.CosmosDB` módulo agora faz parte do `Az` módulo. [Descarregue e instale](/powershell/azure/install-az-ps) a versão mais recente do módulo Az para obter os cmdlets DB do Azure Cosmos. Também pode obter a versão mais recente da [PowerShell Gallery.](https://www.powershellgallery.com/packages/Az/5.4.0) Você também pode forme estas amostras PowerShell para Cosmos DB do nosso repositório GitHub, [Cosmos DB PowerShell Samples no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Amostras comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as regiões de uma conta cosmos DB. |
|[Alterar prioridade de failover ou desencadear falha](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mude a prioridade regional de failover de uma conta Azure Cosmos ou desencadeie uma falha manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cordas de ligação ou regenerar uma chave de conta de uma conta Azure Cosmos DB. |
|[Criar uma conta Cosmos com firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta DB Azure Cosmos com ip firewall ativada. |
|||

## <a name="mongo-db-api-samples"></a>Amostras da API do Mongo DB

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, base de dados e recolha](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, base de dados e recolha da Azure Cosmos. |
|[Criar uma conta, base de dados e recolha com autoescala](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, base de dados e recolha Azure Cosmos com autoescala. |
|[Listar ou obter bases de dados ou coleções](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha base de dados ou recolha. |
|[Operações de produção](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operações de produção de uma base de dados ou recolha, incluindo obter, atualizar e migrar entre a autoescala e a produção padrão. |
|[Bloquear recursos da supressão](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Evite que os recursos sejam eliminados com bloqueios de recursos. |
|||
