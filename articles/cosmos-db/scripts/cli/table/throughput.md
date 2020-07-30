---
title: Atualizar RU/s para uma tabela API de tabela para Azure Cosmos DB
description: Atualizar RU/s para uma tabela API de tabela para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 3f3a39e546c83620ded539180197f6fda760260f
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431539"
---
# <a name="update-rus-for-a-table-api-table-for-azure-cosmos-db-azure-cli"></a>Atualizar RU/s para uma tabela API de tabela para Azure Cosmos DB Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão 2.9.1 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma tabela API de tabela e, em seguida, atualiza o resultado da tabela.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Update RU/s for a Table API table.")]

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
| [az cosmosdb criar](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Cria uma tabela API de tabela Azure Cosmos. |
| [az cosmosdb tabela de saída](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Atualize RU/s para uma tabela API de tabela Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
