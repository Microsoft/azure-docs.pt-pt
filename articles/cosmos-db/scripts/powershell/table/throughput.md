---
title: Scripts PowerShell para operações de produção (RU/s) para Azure Cosmos DB Table API
description: Scripts PowerShell para operações de produção (RU/s) para Azure Cosmos DB Table API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 061bd06c0fa54497d8f00db0e5d4a808e949c2d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679207"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>Operações de produção (RU/s) com a PowerShell para uma tabela para Azure Cosmos DB - Tabela API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Esta amostra requer Azure PowerShell Az 5.4.0 ou mais tarde. Corra `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se precisar de instalar, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Executar [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para iniciar sação no Azure.

## <a name="get-throughput"></a>Obtenção de débito

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>Atualização de produção

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>Produção de migração

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

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
| [Get-AzCosmosDBTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | Obtém o valor de produção da tabela API de tabela especificada. |
| [Atualização-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Atualiza o valor de produção da Tabela API tabela. |
| [Invocar-AzCosmosDBTableThroughmigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | Migrar o rendimento de uma tabela API de tabela. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).