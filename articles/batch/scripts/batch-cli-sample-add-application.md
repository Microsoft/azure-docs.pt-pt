---
title: Exemplo do Script da CLI do Azure - Adicionar uma Aplicação no Batch
description: Este script de amostra demonstra como adicionar uma aplicação para uso com um pool Azure Batch ou uma tarefa.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765288"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Exemplo da CLI: Adicionar uma aplicação a uma conta do Azure Batch

Este script demonstra como adicionar uma aplicação para utilizar com um conjunto ou tarefas do Azure Batch. Para configurar uma aplicação para adicionar à sua conta do Batch, reúna o executável juntamente com quaisquer dependências num ficheiro zip. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.20 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos.
Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de armazenamento. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Cria uma aplicação.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Adiciona um pacote de aplicação à aplicação especificada.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Atualiza as propriedades de uma aplicação.  |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
