---
title: Atualização RU/s para um espaço de chaves Cassandra e mesa para Azure Cosmos DB
description: Atualização RU/s para Cassandra um espaço de chave e mesa para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71827308"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Atualização RU/s para um espaço de chaves cassandra e mesa usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria um espaço de chaves Cassandra com entrada partilhada e uma tabela Cassandra com entrada dedicada, em seguida, atualiza o resultado tanto para o espaço de chave como para a tabela.

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
| [az cosmosdb cassandra espaço chave criar](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Cria um espaço de chaves Azure Cosmos Cassandra. |
| [az cosmosdb cassandra mesa criar](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Cria uma mesa Azure Cosmos Cassandra. |
| [az cosmosdb cassandra keyspace atualização de entrada](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Atualização RU/s para um espaço de keyspace Azure Cosmos Cassandra. |
| [az cosmosdb cassandra tabela atualização de entrada](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Atualização RU/s para uma mesa Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Cosmos DB CLI, consulte a [documentação Do CD D. Azure Cosmos.](/cli/azure/cosmosdb)

Todas as amostras de script soro D DD Do Azure Cosmos podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
