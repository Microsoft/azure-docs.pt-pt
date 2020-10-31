---
title: Roteiro PowerShell para criar base de dados ESPI API AZURE Cosmos DB SQL
description: Azure PowerShell script - Azure Cosmos DB criar base de dados e recipiente SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3a803ef8c85769e8995602b980ee3249f4edcce8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074578"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Criar uma base de dados e um recipiente para Azure Cosmos DB - SQL API
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

Este script cria uma conta Cosmos para API SQL (Core) em duas regiões com consistência de nível de sessão, uma base de dados, e um recipiente com uma chave de partição, política de indexação personalizada, política chave única, TTL, produção dedicada, e último escritor ganha a política de resolução de conflitos com um caminho personalizado de resolução de conflitos que será usado quando `multipleWriteLocations=true` .

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
|**BD do Cosmos para o Azure**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cria uma conta De Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Cria uma Base de Dados DE DB SQL cosmos. |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Cria um objeto PSSqlUniqueKey usado como parâmetro para New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Cria um objeto PSSqlUniqueKeyPolicy usado como parâmetro para New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Cria um objeto PSCompositePath usado como parâmetro para New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Cria um objeto PSIndexes usado como parâmetro para New-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Cria um objeto PSIncludedPath usado como parâmetro para New-AzCosmosDBSqlIndexingPolicy. |
| [Nova-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Cria um objeto PSSqlIndexingPolicy usado como parâmetro para New-AzCosmosDBSqlContainer. |
| [Nova-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Cria um objeto PSSqlConflictResolutionPolicy usado como parâmetro para New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Cria um novo recipiente Cosmos DB SQL. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).