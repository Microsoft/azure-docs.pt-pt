---
title: PowerShell script para criar base de dados e gráfico da API Azure Cosmos DB Gremlin
description: Script Azure PowerShell - Azure Cosmos DB criar base de dados e gráfico da API Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: b8fb4bee9e31915d1ff88ffdc73b4fae9eb5caf8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659738"
---
# <a name="create-a-database-and-graph-for-azure-cosmos-db---gremlin-api"></a>Crie uma base de dados e gráfico para Azure Cosmos DB - Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-create.ps1 "Create a database and graph for Gremlin API")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cria uma conta Cosmos DB. |
| [New-AzCosmosDBGremlinDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | Cria uma base de dados da API Gremlin. |
| [New-AzCosmosDBGremlinConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlinconflictresolutionpolicy) | Cria uma Política de Resolução de Conflitos gremlin API. |
| [Novo AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | Cria um gráfico gremlin API. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).