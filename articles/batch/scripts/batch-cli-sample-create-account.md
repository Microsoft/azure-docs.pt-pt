---
title: Exemplo de script do Azure CLI - Criar conta Batch - Serviço batch
description: Este script cria uma conta do Azure Batch no modo de serviço do Batch e mostra como consultar ou atualizar várias propriedades da conta.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42f2766130c9809fe2e05d9ce82bf8a78fc712f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494422"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Exemplo da CLI: Criar uma conta do Batch no modo de serviço do Batch

Este script cria uma conta do Azure Batch no modo de serviço do Batch e mostra como consultar ou atualizar várias propriedades da conta. Quando cria uma conta do Batch no modo de serviço do Batch predefinido, os respetivos nós de computação são atribuídos internamente pelo serviço do Batch. Os nós de computação alocados estão sujeitos a uma quota de vCPU (núcleo) separada e a conta pode ser autenticada através de credenciais de chave partilhada ou um token do Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Atualiza propriedades da conta do Batch.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Obtém os detalhes da conta do Batch especificada.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Obtém as chaves de acesso da conta do Batch especificada.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
