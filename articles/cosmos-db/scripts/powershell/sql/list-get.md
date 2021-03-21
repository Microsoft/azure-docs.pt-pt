---
title: Roteiro PowerShell para listar e obter recursos API API API da Azure Cosmos DB SQL
description: Azure PowerShell script - Lista DB da Azure Cosmos e obter operações para API SQL
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 849e73e61341576c4387aba8da8a648cf396052a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680856"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Listar e obter bases de dados e contentores para Azure Cosmos DB - SQL (Core) API
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Esta amostra requer Azure PowerShell Az 5.4.0 ou mais tarde. Corra `Get-Module -ListAvailable Az` para ver quais versões estão instaladas.
Se precisar de instalar, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Executar [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para iniciar sação no Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Lista contas de DB cosmos ou obtém uma conta de DB cosmos especificada. |
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Lista as bases de dados do Cosmos DB numa Conta ou obtém uma base de dados de Cosmínio De Cosmos numa Conta. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Lista os contentores do Cosmos DB numa base de dados ou obtém um recipiente de DB cosmos especificado numa base de dados. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).