---
title: Criar bloqueio de recursos para uma base de dados ESPI API (SQL) AZure Cosmos
description: Criar bloqueio de recursos para uma base de dados ESPI API (SQL) AZure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5a44376e0039e2783081785b06191f7c36c19f60
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514707"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Criar bloqueio de recursos para uma base de dados ESPI AZure Cosmos DB (SQL) usando Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> As fechaduras de recursos não funcionam para alterações efetuadas pelos utilizadores que se ligam utilizando qualquer Cosmos DB SDK, quaisquer ferramentas que se conectem através de chaves de conta, ou o Portal Azure, a menos que a conta Cosmos DB seja bloqueada pela primeira vez com a `disableKeyBasedMetadataWriteAccess` propriedade ativada. Para saber mais sobre como ativar esta propriedade ver, [Prevenindo alterações de SDKs.](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

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
