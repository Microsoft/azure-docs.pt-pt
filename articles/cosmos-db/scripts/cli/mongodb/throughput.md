---
title: Scripts Azure CLI para operações de produção (RU/s) para Azure Cosmos DB API para recursos mongoDB
description: Scripts Azure CLI para operações de produção (RU/s) para Azure Cosmos DB API para recursos mongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 54aebb48986b94dbebea400e6b6ee155a54f514b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566219"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Operações de produção (RU/s) com O Azure CLI para uma base de dados ou gráfico para Azure Cosmos DB API para a MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.12.1 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados MongoDB com produção e recolha partilhadas com produção dedicada, e depois atualiza o resultado para ambos. O script migra então de padrão para produção de escala automática e depois lê o valor da produção de autoescala depois de ter sido migrado.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

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
| [az cosmosdb mongodb base de dados criar](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Cria uma base de dados Azure Cosmos MongoDB API. |
| [coleção az cosmosdb mongodb criar](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Cria uma coleção Azure Cosmos MongoDB API. |
| [az cosmosdb mongodb base de dados atualização de produção](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Atualize RUs para uma base de dados Azure Cosmos MongoDB API. |
| [az cosmosdb mongodb coleção de produção](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Atualizar RUs para uma coleção Azure Cosmos MongoDB API. |
| [az cosmosdb mongodb base de dados migram](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-migrate) | Migrar para uma base de dados. |
| [az cosmosdb mongodb coleção de produção migram](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-migrate) | Migrar para uma coleção. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
