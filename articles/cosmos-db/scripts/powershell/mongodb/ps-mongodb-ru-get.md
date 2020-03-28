---
title: PowerShell script para obter a produção (RU/s) para a API da Azure Cosmos DB para MongoDB
description: Script Azure PowerShell - Azure Cosmos DB Get Throughput (RU/s) para MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 25ed59b73f3b508fd29a964bb6b981049957143f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365653"
---
# <a name="get-throughput-rus-for-a-database-or-collection-for-azure-cosmos-db---mongodb-api"></a>Obtenha APi (RU/s) para uma base de dados ou recolha para a Azure Cosmos DB - MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for MongoDB API")]

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
| [Get-AzCosmosDBMongoDBDatabasePutput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabasethroughput) | Obtém o valor de entrada da base de dados aPI de MongoDB especificada. |
| [Get-AzCosmosDBMongoDBCollectionPutput](hhttps://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollectionthroughput) | Obtém o valor de entrada da especificada Coleção MongoDB API. |
|**Grupos de Recursos Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Pode ver exemplos do script do Azure PowerShell Cosmos DB adicionais nos [scripts do PowerShell do Azure Cosmos DB](../../../powershell-samples.md).