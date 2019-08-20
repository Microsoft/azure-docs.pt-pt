---
title: CLI do Azure script – replicação de várias regiões para Azure Cosmos DB
description: Exemplo do Script da CLI do Azure - Replicação de várias regiões para o Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 7c957aff49e3428fec4b7ac7f2db19eeed9d7a40
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614754"
---
# <a name="replicate-an-azure-cosmos-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli"></a>Replicar uma conta do banco de dados Cosmos do Azure em várias regiões e configurar as prioridades de failover usando o CLI do Azure

Este exemplo Replica qualquer tipo de conta de banco de dados Cosmos do Azure em várias regiões e configura as prioridades de failover usando o CLI do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh "Scale Azure Cosmos DB into multiple regions, change fail-over regions")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Atualiza uma conta do Azure Cosmos DB. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Azure Cosmos DB adicionais na [documentação da CLI do Azure Cosmos DB](../cli-samples.md).
