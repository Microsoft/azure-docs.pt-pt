---
title: Criar uma aplicação de função com implementação de DevOps - Azure CLI
description: Criar uma Function App e implementar código de função a partir do Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 773a08646f7a69e1ed828621bad48a6c6729eb88
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498533"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Criar uma função em Azure que é implantada a partir de Azure DevOps

Este tópico mostra-lhe como utilizar as Funções Azure para criar uma aplicação de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) utilizando o plano [de Consumo.](../functions-scale.md#consumption-plan) A aplicação de função, que é um recipiente para as suas funções, é continuamente implantada a partir de um repositório Azure DevOps. 

Para concluir este tópico, tem de ter:

* Um repositório do Azure DevOps que contém o projeto da aplicação de funções e para o qual tem permissões administrativas.
* Um [token de acesso pessoal (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) para aceder ao seu repositório do Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se, em vez disso, utilizar a CLI do Azure localmente, terá de instalar e utilizar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma Function App do Azure e implementa código de função a partir do Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma conta de armazenamento, uma aplicação de funções e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para a aplicação de funções. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan)sem servidor. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa uma aplicação de funções a um repositório do Git ou do Mercurial. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
