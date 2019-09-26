---
title: Atualizar RU/s para um banco de dados e uma coleção para a API do MongoDB para Azure Cosmos DB
description: Atualizar RU/s para um banco de dados e uma coleção para a API do MongoDB para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275508"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para um banco de dados e uma coleção para a API do MongoDB para Azure Cosmos DB usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Esse script cria um banco de dados com taxa de transferência compartilhada e uma coleção com taxa de transferência dedicada para Azure Cosmos DB para a API do MongoDB e, em seguida, atualiza a taxa de transferência para o banco de dados e a coleção.

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
| [criar banco de dados do AZ cosmosdb MongoDB](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Cria um banco de dados da API MongoDB do Azure Cosmos. |
| [criação de coleção AZ cosmosdb MongoDB](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Cria uma coleção de API do MongoDB do Azure Cosmos. |
| [atualização de produtividade do banco de dados AZ cosmosdb MongoDB](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Atualizar RUs para um banco de dados da API MongoDB do Azure Cosmos. |
| [atualização de taxa de transferência de coleção AZ cosmosdb MongoDB](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Atualizar RUs para uma coleção de API do MongoDB do Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
