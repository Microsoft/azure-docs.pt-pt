---
title: Atualizar RU/s para uma base de dados EPI (Core) E um recipiente para Azure Cosmos DB
description: Atualizar RU/s para uma base de dados EPI (Core) E um recipiente para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 23b18d903bfd4e9d723524924e6c31db45c14d62
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429887"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para uma base de dados EPI (Core) E um recipiente para Azure Cosmos DB usando O Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão 2.9.1 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados API SQL (Core) com produção partilhada e um recipiente API SQL (Core) com produção dedicada, atualizando então a produção tanto para a base de dados como para o contentor.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

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
| [az cosmosdb sql base de dados atualização de produção](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Atualize RU/s para uma base de dados Azure Cosmos SQL (Core). |
| [az cosmosdb sql atualização de produção de recipiente](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Atualize RU/s para um recipiente Azure Cosmos SQL (Core). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
