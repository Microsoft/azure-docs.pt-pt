---
title: Conectar uma conta existente do Azure cosmos com pontos de extremidade de serviço de rede virtual
description: Conectar uma conta existente do Azure cosmos com pontos de extremidade de serviço de rede virtual
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46e93e864034c451e1da1848a318ab176a292b6e
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275564"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Conectar uma conta existente do Azure cosmos com pontos de extremidade de serviço de rede virtual usando CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando o CLI do Azure versão 2.0.73 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

Este exemplo destina-se a mostrar como conectar uma conta existente do Azure Cosmos a uma nova rede virtual existente na qual a sub-rede ainda não está configurada para pontos de extremidade `ignore-missing-vnet-service-endpoint` de serviço usando o parâmetro. Isso permite que a configuração da conta Cosmos seja concluída sem erros antes que a configuração para a sub-rede da rede virtual seja concluída. Depois que a configuração de sub-rede for concluída, a conta Cosmos será acessível por meio da sub-rede configurada.

> [!NOTE]
> Este exemplo demonstra como usar uma conta de API do SQL (núcleo). Para usar este exemplo para outras APIs, aplique os `enable-virtual-network` parâmetros `virtual-network-rules` e no script abaixo ao script específico da API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede virtual do Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Cria uma sub-rede para uma rede virtual do Azure. |
| [AZ Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Retorna uma sub-rede para uma rede virtual do Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Cria uma conta do Azure Cosmos DB. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Atualiza uma sub-rede para uma rede virtual do Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure Cosmos DB, consulte [Azure Cosmos DB documentação da CLI](/cli/azure/cosmosdb).

Todos os exemplos de script da CLI do Azure Cosmos DB podem ser encontrados no [repositório GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
