---
title: Scripts Azure CLI para operações de produção (RU/s) para recursos API Azure Cosmos DB Core (SQL)
description: Scripts Azure CLI para operações de produção (RU/s) para recursos API Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 8310de5ce8fd3f90e422555a5111569fadcca982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566406"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Operações de produção (RU/s) com Azure CLI para uma base de dados ou contentor para Azure Cosmos DB Core (SQL) API
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.12.1 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados API Core (SQL) com produção partilhada e um recipiente API Core (SQL) com produção dedicada, atualizando então a produção tanto para a base de dados como para o contentor. O script migra então de padrão para produção de escala automática e depois lê o valor da produção de autoescala depois de ter sido migrado.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [az cosmosdb sql base de dados criar](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Cria uma base de dados Azure Cosmos Core (SQL). |
| [recipiente az cosmosdb sql criar](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Cria um recipiente Azure Cosmos Core (SQL). |
| [az cosmosdb sql base de dados atualização de produção](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Atualizar a produção para uma base de dados Azure Cosmos Core (SQL). |
| [az cosmosdb sql atualização de produção de recipiente](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Atualização da produção de um recipiente Azure Cosmos Core (SQL). |
| [az cosmosdb sql base de dados migram](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Migrar para uma base de dados do Azure Cosmos Core (SQL). |
| [az cosmosdb sql produção de recipiente migram](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Migrar para um recipiente Azure Cosmos Core (SQL). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
