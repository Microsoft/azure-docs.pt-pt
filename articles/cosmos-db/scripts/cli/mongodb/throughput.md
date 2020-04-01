---
title: Atualização RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB
description: Atualização RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275508"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Atualização RU/s para uma base de dados e recolha para MongoDB API para Azure Cosmos DB usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados com entrada partilhada e uma coleção com entrada dedicada para O Azure Cosmos DB para MongoDB API, em seguida, atualiza o suporte de dados tanto para a base de dados como para a recolha.

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
| [az cosmosdb mongodb coleção criar](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Cria uma coleção Azure Cosmos MongoDB API. |
| [az cosmosdb mongodb base de dados atualização de entrada](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Atualizar RUs para uma base de dados Azure Cosmos MongoDB API. |
| [az cosmosdb mongodb recolha de atualização de entrada](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Atualização rUs para uma coleção Azure Cosmos MongoDB API. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Cosmos DB CLI, consulte a [documentação Do CD D. Azure Cosmos.](/cli/azure/cosmosdb)

Todas as amostras de script soro D DD Do Azure Cosmos podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
