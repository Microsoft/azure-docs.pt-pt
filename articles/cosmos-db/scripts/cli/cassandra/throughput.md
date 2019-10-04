---
title: Atualizar RU/s para um Cassandra e uma tabela de espaço para Azure Cosmos DB
description: Atualizar RU/s para Cassandra um keyspace e uma tabela para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827308"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Atualizar RU/s para um keyspace e uma tabela do Cassandra usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Esse script cria um keyspace Cassandra com taxa de transferência compartilhada e uma tabela Cassandra com taxa de transferência dedicada e, em seguida, atualiza a taxa de transferência para o keyspace e a tabela.

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
| [AZ cosmosdb Cassandra keyspace Create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Cria um keyspace do Azure Cosmos Cassandra. |
| [criar tabela de cosmosdb Cassandra AZ](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Cria uma tabela Cassandra do Azure Cosmos. |
| [atualização de taxa de transferência de keyspace do AZ cosmosdb Cassandra](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Atualizar RU/s para um keyspace do Azure Cosmos Cassandra. |
| [atualização de taxa de transferência de tabela AZ cosmosdb Cassandra](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Atualizar RU/s para uma tabela do Azure Cosmos Cassandra. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
