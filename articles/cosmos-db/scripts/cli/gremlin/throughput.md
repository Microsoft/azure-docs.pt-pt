---
title: Atualizar RU/s para uma base de dados gremlin e gráfico para Azure Cosmos DB
description: Atualizar RU/s para uma base de dados gremlin e gráfico para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4774f5b4e74a963a5a4a07873305c79e2e7019f4
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432190"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para uma base de dados gremlin e gráfico para Azure Cosmos DB usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão 2.9.1 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados Gremlin com produção partilhada e um gráfico Gremlin com produção dedicada, em seguida, atualiza o resultado para a base de dados e gráfico.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

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
| [az cosmosdb gremlin base de dados criar](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Cria uma base de dados Azure Cosmos Gremlin. |
| [gráfico az cosmosdb gremlin criar](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Cria um gráfico Azure Cosmos Gremlin. |
| [az cosmosdb gremlin atualização de produção de dados](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Atualize RU/s para uma base de dados Azure Cosmos Gremlin. |
| [az cosmosdb gremlin gráfico atualização de produção](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Atualize RU/s para um gráfico Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
