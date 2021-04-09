---
title: Criar uma base de dados E um recipiente API core (SQL) com autoescala para Azure Cosmos DB
description: Criar uma base de dados E um recipiente API core (SQL) com autoescala para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 80cbb31bb60c97965c701665de9e3fe174e1cc2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566202"
---
# <a name="create-an-azure-cosmos-core-sql-api-account-database-and-container-with-autoscale-using-azure-cli"></a>Criar uma conta AZure Cosmos Core (SQL) API, base de dados e contentor com autoescala utilizando O Azure CLI
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.73 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/autoscale.sh "Create an Azure Cosmos DB Core (SQL) API account, database, and container with autoscale.")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Cria uma conta do Azure Cosmos DB. |
| [az cosmosdb sql base de dados criar](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Cria uma base de dados Azure Cosmos SQL (Core). |
| [recipiente az cosmosdb sql criar](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Cria um recipiente Azure Cosmos SQL (Core). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
