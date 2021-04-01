---
title: Scripts Azure CLI para operações de produção (RU/s) para recursos API API AZure Cosmos DB Gremlin
description: Scripts Azure CLI para operações de produção (RU/s) para recursos API API AZure Cosmos DB Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 0aa05d165f83eec4bacb588ce974a18034918028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565581"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Operações de produção (RU/s) com Azure CLI para uma base de dados ou gráfico para Azure Cosmos DB - Gremlin API
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.12.1 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados Gremlin com produção partilhada e um gráfico Gremlin com produção dedicada, em seguida, atualiza o resultado para a base de dados e gráfico. O script migra então de padrão para produção de escala automática e depois lê o valor da produção de autoescala depois de ter sido migrado.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

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
| [az cosmosdb gremlin base de dados migram](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Migrar para uma base de dados Azure Cosmos Gremlin. |
| [az cosmosdb gremlin produção de gráfico migrar](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Migrar para um gráfico de Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Azure Cosmos DB CLI, consulte [a documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

Todas as amostras de scriptS Azure Cosmos DB CLI podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
