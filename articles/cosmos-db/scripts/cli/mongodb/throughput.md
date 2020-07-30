---
title: Atualizar RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB
description: Atualizar RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6f3bd23f11abf0e72dc4b7affe54ada42e0cf712
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432177"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão 2.9.1 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados com produção partilhada e uma coleção com produção dedicada para Azure Cosmos DB para a MongoDB API, e depois atualiza o resultado para a base de dados e recolha.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Update RU/s for an Azure Cosmos DB MongoDB API database and collection.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
