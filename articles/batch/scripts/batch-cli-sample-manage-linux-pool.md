---
title: Exemplo de script do Azure CLI - Piscina linux em lote
description: Este script demonstra alguns dos comandos disponíveis no CLI Azure para criar e gerir um conjunto de nós de computação Linux em Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b2e0fbf44be5718cf5577f6bc9aea436968e2fc3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073558"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Exemplo da CLI: Criar e gerir um conjunto do Linux no Azure Batch

Este script demonstra alguns dos comandos disponíveis no CLI Azure para criar e gerir um conjunto de nós de computação Linux em Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Lista os SKUs do agente de nó e as informações da imagem disponíveis.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Cria um conjunto de nós de computação.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Redimensiona o número de VMs em execução no conjunto especificado.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Apresenta as propriedades de um conjunto.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Lista todos os nós de computação no conjunto especificado.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Reinicia o nó de computação especificado.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Elimina os nós listados do conjunto especificado.  |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
