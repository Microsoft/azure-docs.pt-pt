---
title: Atualizar RU/s para um banco de dados e contêiner da API do SQL (núcleo) para Azure Cosmos DB
description: Atualizar RU/s para um banco de dados e contêiner da API do SQL (núcleo) para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275396"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para um banco de dados de API do SQL (núcleo) e contêiner para Azure Cosmos DB usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Esse script cria um banco de dados de API do SQL (núcleo) com taxa de transferência compartilhada e um contêiner de API do SQL (núcleo) com taxa de transferência dedicada e atualiza a taxa de transferência para o banco de dados e o contêiner.

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
| [criar banco de dados SQL AZ cosmosdb](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Cria um banco de dados do Azure Cosmos SQL (núcleo). |
| [criar contêiner de SQL AZ cosmosdb](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Cria um contêiner do Azure Cosmos SQL (núcleo). |
| [atualização de taxa de transferência do banco de dados SQL AZ cosmosdb](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Atualize RU/s para um banco de dados do Azure Cosmos SQL (núcleo). |
| [atualização de produtividade de contêiner AZ cosmosdb SQL](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Atualize RU/s para um contêiner do SQL Cosmos do Azure (núcleo). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
