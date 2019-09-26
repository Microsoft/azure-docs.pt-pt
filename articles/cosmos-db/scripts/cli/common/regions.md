---
title: Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos
description: Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275550"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Esse script demonstra três operações.

- Adicione uma região a uma conta existente do Azure Cosmos.
- Alterar a prioridade de failover regional (aplica-se às contas que usam failover automático)
- Disparar um failover manual de regiões primárias para secundárias (aplica-se às contas com failover manual)

> [!NOTE]
> Adicionar e remover operações de região em uma conta do cosmos não pode ser feito durante a alteração de outras propriedades.

> [!NOTE]
> Este exemplo demonstra como usar uma conta de API do SQL (núcleo), mas essas operações são idênticas em todas as APIs de banco de dados no Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Atualiza uma conta de Azure Cosmos DB (adicionar ou remover região). |
| [AZ cosmosdb failover-Priority-ALTER](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Atualize a prioridade de failover ou o failover de gatilho em uma conta de Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
