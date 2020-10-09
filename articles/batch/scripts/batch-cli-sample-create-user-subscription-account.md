---
title: Exemplo de script do Azure CLI - Criar conta Batch - subscrição do utilizador
description: Este script cria uma conta do Azure Batch no modo de subscrição do utilizador. Esta conta atribui nós computacional à sua subscrição.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c60b9c3de5f2370d7bbc557d36db61d23d32ccf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494405"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemplo da CLI: Criar uma conta do Batch no modo de subscrição do utilizador

Este script cria uma conta do Azure Batch no modo de subscrição do utilizador. Uma conta que aloca nós de computação na sua subscrição tem de ser autenticada através de um token do Azure Active Directory. Os nós de computação alocados contam para a quota de vCPU (núcleo) da sua subscrição. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.20 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az role assignment create](/cli/azure/role) | Crie uma nova atribuição de função para um utilizador, grupo ou principal de serviço. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Cria um cofre de chaves. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Atualize a política de segurança do cofre de chaves especificado. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Batch.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica contra a conta do Batch especificada para obter mais interação da CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).
