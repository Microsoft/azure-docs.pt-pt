---
title: Atualização RU/s para uma base de dados Gremlin e gráfico para Azure Cosmos DB
description: Atualização RU/s para uma base de dados Gremlin e gráfico para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275522"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Atualização RU/s para uma base de dados Gremlin e gráfico para Azure Cosmos DB usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este script cria uma base de dados Gremlin com entrada partilhada e um gráfico Gremlin com entrada dedicada, em seguida, atualiza a entrada tanto para a base de dados como para o gráfico.

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
| [az cosmosdb gremlin gráfico criar](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Cria um gráfico Azure Cosmos Gremlin. |
| [az cosmosdb gremlin atualização de entrada de base de dados](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Atualização RU/s para uma base de dados Azure Cosmos Gremlin. |
| [az cosmosdb gremlin gráfico atualização de entrada](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Atualização RU/s para um gráfico Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Cosmos DB CLI, consulte a [documentação Do CD D. Azure Cosmos.](/cli/azure/cosmosdb)

Todas as amostras de script soro D DD Do Azure Cosmos podem ser encontradas no [Repositório Azure Cosmos DB CLI GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
