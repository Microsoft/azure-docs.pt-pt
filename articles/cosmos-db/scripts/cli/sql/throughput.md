---
title: Atualização RU/s para uma base de dados EPI SQL (Core) e recipiente para Azure Cosmos DB
description: Atualização RU/s para uma base de dados EPI SQL (Core) e recipiente para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275396"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Atualização RU/s para uma base de dados ECONTENTOR SQL (Core) para Azure Cosmos DB utilizando o Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados SQL (Core) API com entrada partilhada e um recipiente SQL (Core) API com entrada dedicada, em seguida, atualiza a entrada tanto para a base de dados como para o recipiente.

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
| [az cosmosdb sql recipiente criar](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Cria um recipiente Azure Cosmos SQL (Core). |
| [az cosmosdb sql base de dados de entrada de dados atualização](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Atualização RU/s para uma base de dados Azure Cosmos SQL (Core). |
| [az cosmosdb sql recipiente de entrada de entrada](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Atualização RU/s para um recipiente Azure Cosmos SQL (Core). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Cosmos DB CLI, consulte a [documentação Do CD D. Azure Cosmos.](/cli/azure/cosmosdb)

Todas as amostras de script soro D DD Do Azure Cosmos podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
