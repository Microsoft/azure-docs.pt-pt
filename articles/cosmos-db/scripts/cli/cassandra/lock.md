---
title: Crie bloqueio de recursos para um espaço-chave Cassandra e tabela para Azure Cosmos DB
description: Crie bloqueio de recursos para um espaço-chave Cassandra e tabela para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5683eece3f6dc1c63be27ce3c98664e972b8d7c6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086749"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Crie um bloqueio de recursos para o espaço-chave Azure Cosmos Cassandra API e tabela usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para alterações efetuadas pelos utilizadores que se ligam utilizando qualquer Cassandra SDK, CQL Shell ou o Portal Azure, a menos que a conta Cosmos DB seja bloqueada pela primeira vez com a `disableKeyBasedMetadataWriteAccess` propriedade ativada. Para saber mais sobre como ativar esta propriedade ver, [Prevenindo alterações de SDKs.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [bloqueio az criar](/cli/azure/lock#az-lock-create) | Cria uma fechadura. |
| [lista de bloqueio az](/cli/azure/lock#az-lock-list) | Lista informação de bloqueio. |
| [az lock show](/cli/azure/lock#az-lock-show) | Mostrar propriedades de uma fechadura. |
| [bloqueio az eliminar](/cli/azure/lock#az-lock-delete) | Apaga uma fechadura. |

## <a name="next-steps"></a>Passos seguintes

-[Bloquear recursos para evitar alterações inesperadas](../../../../azure-resource-manager/management/lock-resources.md)

-[Documentação do Azure Cosmos DB CLI](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
