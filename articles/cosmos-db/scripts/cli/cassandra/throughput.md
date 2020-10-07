---
title: Atualizar RU/s para um espaço-chave Cassandra e tabela para Azure Cosmos DB
description: Atualize RU/s para Cassandra um espaço-chave e tabela para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c0026202e8f110123a7360a5fda521947ed8e9ee
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803658"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Atualizar RU/s para um espaço-chave Cassandra e tabela usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão 2.9.1 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria um espaço-chave Cassandra com produção partilhada e uma tabela Cassandra com produção dedicada, em seguida, atualiza a produção tanto para o espaço-chave como para a mesa.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace criar](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Cria um espaço-chave Azure Cosmos Cassandra. |
| [az cosmosdb cassandra tabela criar](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Cria uma mesa Azure Cosmos Cassandra. |
| [az cosmosdb cassandra keyspace atualização de produção](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest&preserve-view=true#az-cosmosdb-cassandra-keyspace-throughput-update) | Atualize RU/s para um espaço-chave Azure Cosmos Cassandra. |
| [az cosmosdb cassandra tabela de saída](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Atualize RU/s para uma tabela Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
