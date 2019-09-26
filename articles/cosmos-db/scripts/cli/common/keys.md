---
title: Trabalhar com chaves de conta e cadeias de conexão para uma conta do Azure Cosmos
description: Trabalhar com chaves de conta e cadeias de conexão para uma conta do Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: e766f52d729a4f916eefd2b148d926d929b4f540
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275557"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Trabalhar com chaves de conta e cadeias de conexão para uma conta do Azure Cosmos usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Esse script demonstra quatro operações.

- Listar todas as chaves de conta
- Listar chaves de conta somente leitura
- Listar cadeias de conexão
- Regenerar chaves de conta

> [!NOTE]
> Este exemplo demonstra o uso de uma conta de API do SQL (Core), mas as operações de chave da conta e cadeia de conexão são idênticas em todas as APIs de banco de dados no Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [lista de chaves AZ cosmosdb](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | Listar as chaves para uma conta de Azure Cosmos DB. |
| [AZ cosmosdb List-somente leitura-chaves](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | Listar as chaves somente leitura para uma conta de Azure Cosmos DB. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Listar as cadeias de conexão para uma conta de Azure Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Regenerar chaves para uma conta de Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
